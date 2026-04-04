# Portfolio Architecture: The Ashtonbee Platform

> Status: future/background reference. This file is not a statement of current
> runtime truth.
>
> Use this essay for:
> - future-state architecture ideas
> - deferred technology choices
> - preserved rationale from earlier planning passes
>
> Use these files instead for current truth:
> - `planning/repo-briefs/`
> - repo-local `README.md`, `docs/roadmap.md`, ADRs, migrations, and schemas
> - `planning/IMPLEMENTATION-BOARD.md`
> - `planning/sprints/TRACER-MATRIX.md`
>
> If this essay conflicts with current repo docs, this essay loses.
>
> Superseded or re-scoped areas to watch for:
> - `check-in` terminology is now `presence` in current working docs
> - `HERMES` is staff-facing only, not the student-facing product surface
> - APOLLO now uses a multi-mode member model with explicit privacy and availability state
> - APOLLO product language prefers `lobby` over `queue`

**Codename: ASHTON**
**Author: Izzet**
**Date: March 2026**
**Status: Future-State Architecture Background**

---

## 0 — Design Philosophy

Every project in this portfolio exists as a node in a single interconnected platform.
No project stands alone. Each one exercises a different technical axis while sharing
infrastructure, observability, authentication, and operational patterns with every other.

The entire platform is **AI-agent-native from day one**. Every service exposes:

- An HTTP/gRPC API for programmatic consumers
- A CLI binary for human operators and AI agents alike
- A tool manifest (MCP-compatible schema) so LangGraph agents can invoke any service without custom integration code
- Structured JSON output by default on every endpoint and CLI command

The narrative an interviewer reads across these repos is not "student built some projects."
It is: **"this person designed, built, operated, and instrumented a distributed platform
serving real users on self-hosted infrastructure, and every component is agent-operable."**

---

## 1 — System Map

```
┌─────────────────────────────────────────────────────────────────────┐
│                         TAILSCALE MESH                              │
│   (all nodes, all services, all operator access)                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │              PROMETHEUS (Tower — Talos K8s)                   │   │
│  │  Ryzen 5950X · RTX 3090 · 32 GB · Cilium · Flux · SOPS     │   │
│  │                                                               │   │
│  │  ┌─────────────┐  ┌──────────────┐  ┌───────────────────┐   │   │
│  │  │   vLLM      │  │  LangGraph   │  │  Mem0 + Qdrant    │   │   │
│  │  │  Mistral-7B │  │  Orchestrator│  │  Semantic Memory   │   │   │
│  │  │  (GPU)      │  │  + Postgres  │  │  + TEI Embeddings  │   │   │
│  │  └──────┬──────┘  └──────┬───────┘  └────────┬──────────┘   │   │
│  │         │                │                    │               │   │
│  │  ┌──────┴────────────────┴────────────────────┴──────────┐   │   │
│  │  │              SHARED DATA PLANE                         │   │   │
│  │  │  Postgres (state) · Redis (cache/pubsub) · NATS (events)│  │   │
│  │  └───────────────────────┬───────────────────────────────┘   │   │
│  │                          │                                    │   │
│  │  ┌───────────┐  ┌───────┴─────┐  ┌────────────────────┐     │   │
│  │  │ ATHENA    │  │  HERMES     │  │  APOLLO             │     │   │
│  │  │ Check-in  │  │  Ops Bot    │  │  Training Engine    │     │   │
│  │  │ + Capacity│  │  + Chat     │  │  + Recommendations  │     │   │
│  │  │ (Go)      │  │  (Go)       │  │  (Go + Python)      │     │   │
│  │  └───────────┘  └─────────────┘  └────────────────────┘     │   │
│  │                                                               │   │
│  │  ┌────────────────────────────────────────────────────────┐  │   │
│  │  │              OBSERVABILITY PLANE                        │  │   │
│  │  │  Prometheus · Grafana · Loki · AlertManager             │  │   │
│  │  └────────────────────────────────────────────────────────┘  │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐              │
│  │  NUC-1       │  │  NUC-2       │  │  NUC-3       │              │
│  │  (Talos)     │  │  (Talos)     │  │  (Talos)     │              │
│  │  CPU worker  │  │  CPU worker  │  │  CPU worker  │              │
│  │  + etcd      │  │  + etcd      │  │  + etcd      │              │
│  └──────────────┘  └──────────────┘  └──────────────┘              │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │              NAS (TrueNAS or Unraid)                         │   │
│  │  Bulk storage · model cache overflow · backups               │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2 — The Projects

### 2.1 — PROMETHEUS (Exists — Layer 1)

**Repo:** `homelab-gitops` + `tower-bootstrap`
**Purpose:** The bare-metal Kubernetes platform everything runs on.
**Status:** Live and stable.

This is already built. It is the substrate. Every project below deploys onto it
through Flux GitOps. No further description needed here — the existing documentation,
growing-pains log, and ADRs speak for themselves.

**What gets added to Prometheus during the ASHTON build:**

- Redis (Helm chart via Flux) — used as cache and pub/sub broker across services
- NATS (Helm chart via Flux) — event bus for inter-service communication
- Prometheus + Grafana + Loki (observability stack, Phase 4 of existing roadmap)
- NUC worker nodes joining the cluster (multi-node expansion)
- Ingress controller (Traefik or Nginx via Cilium Gateway API)

---

### 2.2 — ATHENA: Facility Intelligence Platform

**Repo:** `athena`
**Language:** Go
**Tagline:** Real-time facility check-in, capacity tracking, and predictive analytics
with AI-agent operability.

#### What It Does

ATHENA is the foundational facility application. It integrates with the existing
proprietary tap-in system (read-only database access or API polling), tracks real-time
occupancy, predicts peak hours using historical patterns, and surfaces alerts when
capacity thresholds are approached. Every data point is instrumented — Prometheus
metrics, structured logs to Loki, Grafana dashboards showing live and historical
facility usage.

#### Why It Matters for the Portfolio

This is the Go project. It directly addresses Tailscale's primary language requirement.
It demonstrates SQL database experience (Postgres), observability (Prometheus/Grafana),
distributed systems patterns (event-driven updates via NATS), and CI/CD (GitHub Actions
building Go binaries and container images, Flux deploying them).

Most critically: **it has real users.** Ashtonbee staff and students interact with
the system daily. Every peak-hour capacity alert, every tap-in event processed,
every dashboard query is real production traffic.

#### Technical Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    ATHENA SERVICE                         │
│                                                           │
│  ┌─────────────┐    ┌──────────────┐    ┌─────────────┐ │
│  │  Ingress    │    │  Go HTTP     │    │  Go CLI     │ │
│  │  (PWA/Web)  │───▶│  Server      │    │  (athena)   │ │
│  │             │    │  chi router  │    │  cobra cmds │ │
│  └─────────────┘    └──────┬───────┘    └──────┬──────┘ │
│                            │                    │        │
│                     ┌──────┴────────────────────┴──┐     │
│                     │        Core Domain            │     │
│                     │  ┌──────────┐ ┌────────────┐ │     │
│                     │  │ Checkin  │ │ Capacity   │ │     │
│                     │  │ Service  │ │ Predictor  │ │     │
│                     │  └────┬─────┘ └─────┬──────┘ │     │
│                     │       │              │        │     │
│                     │  ┌────┴──────────────┴─────┐ │     │
│                     │  │    Repository Layer      │ │     │
│                     │  │    (sqlc generated)      │ │     │
│                     │  └────────────┬────────────┘ │     │
│                     └───────────────┼──────────────┘     │
│                                     │                     │
│  ┌──────────────┐    ┌──────────────┴──┐    ┌──────────┐ │
│  │  Tap-In      │    │   Postgres      │    │ NATS     │ │
│  │  Adapter     │───▶│   (shared)      │    │ (events) │ │
│  │  (polling/   │    │                 │    │          │ │
│  │   db read)   │    └─────────────────┘    └────┬─────┘ │
│  └──────────────┘                                │       │
│                                                   │       │
│  ┌────────────────────────────────────────────────┴────┐  │
│  │  Publishes Events:                                  │  │
│  │    checkin.occurred                                  │  │
│  │    capacity.threshold.warning                        │  │
│  │    capacity.threshold.critical                       │  │
│  └─────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────┘

Exposed Prometheus Metrics:
  athena_checkins_total{facility="ashtonbee"}           counter
  athena_current_occupancy{facility="ashtonbee"}        gauge
  athena_capacity_utilization_ratio{facility="ashtonbee"} gauge
  athena_prediction_accuracy_ratio                       gauge
  athena_http_request_duration_seconds                   histogram
  athena_tap_adapter_poll_duration_seconds                histogram
  athena_tap_adapter_errors_total                         counter
```

#### Tech Stack Deep Dive

| Component | Technology | Justification |
|-----------|-----------|---------------|
| Language | **Go 1.22+** | Tailscale's language. Compiles to single binary. First-class concurrency. |
| HTTP router | **chi** | Lightweight, idiomatic Go, middleware-friendly. No framework bloat. |
| CLI framework | **cobra + viper** | Industry standard for Go CLIs. Used by kubectl, docker, gh. |
| SQL access | **sqlc** | Generates type-safe Go from raw SQL. No ORM. Queries are auditable. |
| Database | **Postgres 16** (shared cluster instance) | Already running on Prometheus. New database per service. |
| Migrations | **golang-migrate** | SQL-file migrations. Version-controlled. No magic. |
| Event bus | **NATS** | Lightweight pub/sub. No Kafka overhead. Perfect for facility-scale events. |
| Metrics | **prometheus/client_golang** | Native Go Prometheus client. Every endpoint instrumented. |
| Structured logging | **slog** (stdlib) | Go 1.21+ structured logging. JSON output. Ships to Loki. |
| Auth | **Tailscale + session cookies** | Tailscale WhoIs for staff identity. Cookie sessions for facility PWA. |
| Testing | **Go stdlib testing + testcontainers-go** | Integration tests spin real Postgres in containers. No mocks for DB. |
| CI | **GitHub Actions** | Build, test, lint (golangci-lint), build container, push to GHCR. |
| Deployment | **Flux HelmRelease or Kustomization** | GitOps. Image automation updates the tag on push. |
| Container | **distroless/static** | Minimal attack surface. Go static binary + nothing else. |

#### CLI Design (Agent-Operable)

```bash
# Human operator usage
athena checkin list --since 1h --format table
athena checkin count --facility ashtonbee
athena capacity current --facility ashtonbee
athena capacity predict --facility ashtonbee --window 4h
athena capacity history --facility ashtonbee --range 7d --format json

# AI agent usage (same commands, structured output)
athena checkin count --facility ashtonbee --format json
# → {"facility":"ashtonbee","count":142,"timestamp":"2026-07-15T18:30:00Z"}

athena capacity predict --facility ashtonbee --window 4h --format json
# → {"facility":"ashtonbee","predictions":[{"hour":"19:00","expected":185,"confidence":0.87},...]}
```

#### MCP Tool Manifest (for LangGraph)

```json
{
  "name": "athena",
  "description": "Facility check-in and capacity intelligence for Ashtonbee",
  "tools": [
    {
      "name": "get_current_occupancy",
      "description": "Returns real-time facility occupancy count and utilization ratio",
      "input_schema": {
        "type": "object",
        "properties": {
          "facility": {"type": "string", "default": "ashtonbee"}
        }
      }
    },
    {
      "name": "predict_capacity",
      "description": "Predicts facility occupancy for the next N hours based on historical patterns",
      "input_schema": {
        "type": "object",
        "properties": {
          "facility": {"type": "string"},
          "window_hours": {"type": "integer", "default": 4}
        }
      }
    },
    {
      "name": "get_checkin_history",
      "description": "Returns check-in events for a facility within a time range",
      "input_schema": {
        "type": "object",
        "properties": {
          "facility": {"type": "string"},
          "since": {"type": "string", "description": "ISO 8601 duration or timestamp"},
          "limit": {"type": "integer", "default": 100}
        }
      }
    }
  ]
}
```

#### Capacity Prediction Model

Not a deep learning model — that would be overengineering for this scale.
A straightforward approach that's honest and defensible:

1. **Historical binning:** aggregate check-in counts by (day_of_week, hour) over rolling 8 weeks
2. **Exponential weighted moving average:** recent weeks weighted higher than older weeks
3. **Confidence interval:** standard deviation of the bin gives prediction uncertainty
4. **Holiday/break detection:** flag weeks with anomalous total counts, exclude from model
5. **Output:** predicted count ± confidence band for each upcoming hour

This is explainable in an interview: "It's a weighted historical average with
confidence bands, not a neural network. The facility has ~300 daily users — a
neural network would be cosplaying as data science. This approach is auditable,
explainable, and accurate within 15% on backtesting."

#### Tap-In Adapter Pattern

```go
// adapter.go — pluggable data source interface
type TapInAdapter interface {
    // Poll returns new check-in events since the last poll
    Poll(ctx context.Context, since time.Time) ([]CheckinEvent, error)
    // Health returns adapter health status
    Health(ctx context.Context) error
}

// Implementations:
// - MockAdapter: generates synthetic events for development
// - DatabaseAdapter: reads from the proprietary system's database (read-only)
// - APIAdapter: polls an HTTP endpoint if the proprietary system exposes one
// - CSVAdapter: imports historical data from exported spreadsheets
```

This pattern means development starts immediately with MockAdapter.
The real integration happens whenever you get database access — zero code changes
to the core service.

---

### 2.3 — HERMES: Facility Operations Bot

**Repo:** `hermes`
**Language:** Go (core) + LangGraph orchestration (Python sidecar)
**Tagline:** AI-powered operations assistant for facility staff, accessible over
Tailscale from any device.

#### What It Does

HERMES is a conversational interface for facility operations. Staff text or speak
queries — "Is the gym busy right now?" "When was the treadmill in bay 3 last
serviced?" "Book the multipurpose room for Thursday 2-4pm." — and HERMES answers
by orchestrating calls to ATHENA (capacity data), its own equipment/booking database,
and the LLM through vLLM for natural language understanding and response generation.

HERMES runs as a LangGraph agent with tool-calling capability. It doesn't just
answer questions — it can take actions: create bookings, file maintenance tickets,
send capacity alerts. Every action requires confirmation (HITL pattern already
built into LangGraph on Prometheus).

#### Why It Matters for the Portfolio

This project directly demonstrates:

- **Tailscale as infrastructure:** staff access HERMES over the Tailscale mesh from
  personal phones. The bot is not exposed to the public internet — it's a private
  internal tool, exactly like what the Tailscale job describes.
- **Agentic AI in production:** not a chatbot demo, but an agent that calls real tools
  (ATHENA, booking system, maintenance tracker) and takes real actions with human
  approval.
- **Inter-service communication:** HERMES calls ATHENA's API (service-to-service
  within the cluster), demonstrating distributed system design.
- **Go + Python polyglot:** the HTTP/WebSocket gateway is Go (fast, minimal), the
  agent orchestration is LangGraph/Python (already running on Prometheus).

#### Technical Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                      HERMES SERVICE                           │
│                                                               │
│  ┌───────────────┐         ┌─────────────────────────────┐   │
│  │  Go Gateway   │         │  LangGraph Agent (Python)   │   │
│  │               │         │                             │   │
│  │  WebSocket    │◀──gRPC──│  ┌───────────────────────┐  │   │
│  │  REST API     │  stream │  │  Tool: athena.capacity│  │   │
│  │  CLI          │         │  │  Tool: hermes.booking │  │   │
│  │               │         │  │  Tool: hermes.equip   │  │   │
│  │  Tailscale    │         │  │  Tool: hermes.maint   │  │   │
│  │  WhoIs auth   │         │  │  Tool: hermes.notify  │  │   │
│  └───────┬───────┘         │  └───────────┬───────────┘  │   │
│          │                 │              │               │   │
│          │                 │  ┌───────────┴───────────┐  │   │
│          │                 │  │  Mem0 (cross-session   │  │   │
│          │                 │  │  staff context)        │  │   │
│          │                 │  └───────────────────────┘  │   │
│          │                 └─────────────────────────────┘   │
│          │                                                    │
│  ┌───────┴──────────────────────────────────────────────┐    │
│  │                  Postgres                             │    │
│  │  bookings · equipment · maintenance_tickets · audit   │    │
│  └───────────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────────┘

Cross-Service Communication:
  HERMES ──HTTP──▶ ATHENA (capacity queries)
  HERMES ──NATS──▶ subscribes to capacity.threshold.* events
  HERMES ──gRPC──▶ vLLM (inference)
  HERMES ──HTTP──▶ Mem0 (semantic memory read/write)
```

#### Tech Stack

| Component | Technology | Justification |
|-----------|-----------|---------------|
| Gateway | **Go + gorilla/websocket** | Real-time streaming responses. Tailscale WhoIs for zero-config staff auth. |
| Agent | **LangGraph (Python)** | Already running on Prometheus. Tool-calling, HITL, durable checkpoints. |
| Go↔Python | **gRPC streaming** | Typed, bidirectional, low-latency. Protobuf schemas shared between services. |
| LLM | **vLLM → Mistral-7B** (upgradable) | Already serving on Prometheus. Structured output via guided generation. |
| Memory | **Mem0** | Staff preferences and context persist across sessions. Already deployed. |
| Booking store | **Postgres (hermes schema)** | Separate schema, same cluster. Booking conflicts handled with advisory locks. |
| Event ingestion | **NATS subscriber** | Subscribes to ATHENA capacity events to proactively notify staff. |
| Notifications | **NATS publish → notification worker** | Future: push notifications, SMS via Twilio, email via SES. |
| CLI | **cobra (Go)** | `hermes book create`, `hermes equip status`, `hermes maint list` |

#### Conversation Example

```
Staff (via phone, over Tailscale):
  "Hey, is the gym packed right now? I need to decide if I should open the overflow room."

HERMES internally:
  1. Calls athena.get_current_occupancy(facility="ashtonbee")
     → {"count": 187, "capacity": 200, "utilization": 0.935}
  2. Calls athena.predict_capacity(facility="ashtonbee", window_hours=2)
     → {"predictions": [{"hour":"18:00","expected":195},{"hour":"19:00","expected":178}]}
  3. Generates response via vLLM with tool results as context

HERMES responds:
  "Current occupancy is 187/200 (93.5%). The next hour is predicted to peak at 195.
   It's going to stay tight until about 7 PM. Want me to create a booking for the
   overflow room from now until 7 PM?"

Staff: "Yeah do it"

HERMES internally:
  1. Calls hermes.booking.create(room="overflow", start=now, end="19:00",
     booked_by="staff_name", reason="capacity overflow")
  2. Awaits HITL confirmation (LangGraph approval node)

HERMES responds:
  "Done — overflow room booked until 7 PM under your name. I'll let you know
   when main gym occupancy drops below 80%."
  [Sets up NATS subscription for capacity.threshold.resolved event]
```

---

### 2.4 — APOLLO: Training Intelligence Engine

**Repo:** `apollo`
**Language:** Go (API + CLI) + Python (ML pipeline)
**Tagline:** Workout logging, personalized programming, and AI-powered training
recommendations backed by real user data and local inference.

#### What It Does

APOLLO is the member-facing application. Users log workouts through a PWA — exercises,
sets, reps, weight, RPE (rate of perceived exertion). The system tracks training
history, identifies patterns (volume trends, strength progressions, recovery signals),
and generates personalized training suggestions through the local LLM.

The recommendation engine isn't a generic "do more squats" chatbot. It uses:

1. **Structured training data** from the user's history (Postgres)
2. **Semantic memory** from past interactions and stated goals (Mem0)
3. **LLM reasoning** through LangGraph with tool access to query both data sources
4. **Constrained output** — recommendations follow a schema (exercise, sets, reps, load range, rationale)

#### Why It Matters for the Portfolio

- **Real users generating real data.** Ashtonbee members logging workouts daily.
- **Full-stack Go + Python polyglot.** API and CLI in Go, ML pipeline in Python.
- **Demonstrates the AI platform under load.** vLLM serving inference requests
  from concurrent users. Mem0 handling cross-session memory at scale.
- **PWA expertise.** Responsive web app, offline-capable, installable on phones.
- **Data pipeline experience.** Training data → aggregation → feature extraction →
  LLM context window → structured recommendation.

#### Technical Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                      APOLLO SERVICE                           │
│                                                               │
│  ┌────────────────┐    ┌─────────────────────────────────┐   │
│  │  PWA Frontend  │    │  Go API Server                  │   │
│  │  (SvelteKit)   │───▶│                                 │   │
│  │                │    │  /api/workouts    CRUD           │   │
│  │  - Log workout │    │  /api/history     queries        │   │
│  │  - View history│    │  /api/recommend   LLM pipeline   │   │
│  │  - Get recs    │    │  /api/profile     user prefs     │   │
│  │  - Compare     │    │                                 │   │
│  └────────────────┘    └───────────┬─────────────────────┘   │
│                                    │                          │
│  ┌─────────────────────────────────┴──────────────────────┐  │
│  │                 Recommendation Pipeline                 │  │
│  │                                                         │  │
│  │  1. Fetch user workout history (Postgres, last 8 weeks) │  │
│  │  2. Compute volume/intensity/frequency features          │  │
│  │  3. Fetch user goals + preferences (Mem0)               │  │
│  │  4. Build structured prompt with training context         │  │
│  │  5. Call vLLM with guided JSON output schema             │  │
│  │  6. Validate recommendation against constraints          │  │
│  │  7. Store recommendation + user feedback                 │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                               │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────────┐  │
│  │ Postgres │  │  Mem0    │  │  vLLM    │  │  NATS       │  │
│  │ workouts │  │ goals +  │  │ Mistral  │  │ publishes:  │  │
│  │ history  │  │ context  │  │ (GPU)    │  │ workout.log │  │
│  │ recs     │  │          │  │          │  │ rec.gen     │  │
│  └──────────┘  └──────────┘  └──────────┘  └─────────────┘  │
└──────────────────────────────────────────────────────────────┘

Prometheus Metrics:
  apollo_workouts_logged_total                     counter
  apollo_recommendations_generated_total            counter
  apollo_recommendation_feedback{rating}            counter
  apollo_vllm_inference_duration_seconds            histogram
  apollo_active_users_current                       gauge
```

#### Tech Stack

| Component | Technology | Justification |
|-----------|-----------|---------------|
| API | **Go + chi** | Consistent with ATHENA. Shared patterns across the platform. |
| Frontend | **SvelteKit** (pre-built, served by Go) | Consistent with capstone. Fast, lightweight, PWA-capable. |
| Workout storage | **Postgres (apollo schema)** | Relational data with complex queries (volume aggregation, PR tracking). |
| Feature extraction | **SQL aggregation + Go** | Moving averages, volume load calculations, frequency analysis. |
| Recommendation | **LangGraph + vLLM** | Same agent infra as HERMES. Tool calls for data retrieval. |
| Memory | **Mem0** | User goals, injury history, preferences persist across sessions. |
| Offline support | **Service worker + IndexedDB** | Log workouts offline, sync when connected. |
| CLI | **cobra** | `apollo workout log`, `apollo history`, `apollo recommend` |

#### CLI / Agent Interface

```bash
# Log a workout
apollo workout log --exercise "back squat" --sets 5 --reps 5 --weight 315 --rpe 8

# Get training recommendation
apollo recommend --user izzet --format json
# → {"recommendations":[{"exercise":"front squat","sets":4,"reps":6,
#    "load_range":"225-245","rationale":"Quad volume is 18% below your
#    8-week average. Front squats complement your back squat frequency."}]}

# Query training history
apollo history --user izzet --exercise "bench press" --range 12w --format json
# → {"exercise":"bench press","sessions":24,"volume_trend":"increasing",
#    "estimated_1rm":{"current":275,"12w_ago":255},"progression_rate":"1.6lb/week"}
```

---

## 3 — Shared Infrastructure (The Connective Tissue)

These components are not separate projects — they're shared services that every
application depends on. They live in the Prometheus GitOps repo.

### 3.1 — Event Bus: NATS

**Why NATS over Kafka:** Kafka is designed for millions of events/second with
durable replay. A facility with 300 daily users generates maybe 1,000 events/day.
NATS is operationally simpler, uses 50MB of RAM, and supports pub/sub + request/reply
natively. If the system ever needs Kafka semantics, NATS JetStream provides
durable streams without replacing the broker.

**Event Schema Convention:**

```
Subject pattern: {service}.{entity}.{action}

Examples:
  athena.checkin.occurred        — new tap-in event
  athena.capacity.warning        — utilization > 85%
  athena.capacity.critical       — utilization > 95%
  hermes.booking.created         — new room booking
  hermes.booking.cancelled       — booking cancelled
  hermes.maintenance.filed       — new maintenance ticket
  apollo.workout.logged          — member logged a workout
  apollo.recommendation.generated — rec engine produced output
  apollo.recommendation.feedback  — user rated a recommendation
```

Every event is JSON with a standard envelope:

```json
{
  "id": "evt_01HXYZ...",
  "source": "athena",
  "type": "checkin.occurred",
  "timestamp": "2026-07-15T18:30:00Z",
  "data": { ... }
}
```

### 3.2 — Cache Layer: Redis

**Purpose:** Session storage, rate limiting, real-time counters, pub/sub fan-out.

- ATHENA uses Redis for real-time occupancy counter (INCR/DECR on check-in/check-out)
- HERMES uses Redis for WebSocket session state and rate limiting
- APOLLO uses Redis for caching expensive training history aggregations
- All services use Redis for distributed rate limiting on the API layer

### 3.3 — Observability Stack

```
┌─────────────────────────────────────────────────────┐
│              OBSERVABILITY PLANE                     │
│                                                       │
│  ┌─────────────┐  ┌──────────┐  ┌────────────────┐  │
│  │ Prometheus  │  │  Loki    │  │  Grafana       │  │
│  │             │  │          │  │                │  │
│  │ Scrapes:    │  │ Ingests: │  │ Dashboards:   │  │
│  │ - athena    │  │ - all    │  │ - Facility    │  │
│  │ - hermes    │  │   slog   │  │   Overview    │  │
│  │ - apollo    │  │   JSON   │  │ - Per-Service │  │
│  │ - vllm      │  │   logs   │  │   Health      │  │
│  │ - postgres  │  │          │  │ - GPU/Infra   │  │
│  │ - redis     │  │          │  │ - Capacity    │  │
│  │ - nats      │  │          │  │   Live View   │  │
│  │ - node      │  │          │  │ - Training    │  │
│  │              │  │          │  │   Analytics   │  │
│  └──────┬──────┘  └────┬─────┘  └───────┬───────┘  │
│         │              │                 │           │
│         └──────────────┴─────────────────┘           │
│                        │                              │
│  ┌─────────────────────┴──────────────────────────┐  │
│  │              AlertManager                       │  │
│  │                                                 │  │
│  │  Alerts:                                        │  │
│  │  - FacilityCapacityCritical (>95%)              │  │
│  │  - ServiceDown (any app unreachable)            │  │
│  │  - GPUMemoryHigh (>90% VRAM)                    │  │
│  │  - InferenceLatencyHigh (p99 > 5s)              │  │
│  │  - PostgresConnectionPoolExhausted              │  │
│  │  - FluxReconciliationFailed                     │  │
│  │  → Routes to: HERMES bot (staff notification)   │  │
│  │  → Routes to: Izzet (operator, via Tailscale)   │  │
│  └─────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────┘
```

Every application exposes a `/metrics` endpoint in Prometheus format.
Every application logs structured JSON via Go's `slog` to stdout,
collected by Loki's Kubernetes log discovery.

### 3.4 — Authentication: Tailscale-Native

No OAuth server. No JWT infrastructure. No auth0.

- **Staff access:** Tailscale mesh + WhoIs API. If the request comes from a
  Tailscale IP, the Go middleware extracts the identity automatically.
  No passwords, no tokens, no login pages for staff.
- **Member access (PWA):** Simple session cookie after initial registration
  (student ID + email verification). The PWA is served over HTTPS through
  the cluster ingress.
- **Agent access:** CLI tools authenticate via Tailscale identity (same as staff)
  or API keys for automated pipelines.

### 3.5 — The MCP Gateway (Agent Orchestration Layer)

This is the piece that makes the portfolio futuristic and positions it in
the agentic AI wave that defines 2026.

```
┌──────────────────────────────────────────────────────────┐
│                  MCP GATEWAY SERVICE                      │
│                   (Go, lightweight)                        │
│                                                            │
│  Registers tool manifests from:                            │
│    - ATHENA  (capacity, checkin tools)                     │
│    - HERMES  (booking, equipment, maintenance tools)       │
│    - APOLLO  (workout, history, recommendation tools)      │
│    - System  (cluster health, Flux status, pod restart)    │
│                                                            │
│  Consumers:                                                │
│    - LangGraph agents (tool calling via MCP protocol)      │
│    - Claude / external LLMs (if you expose via Tailscale)  │
│    - Future: any MCP-compatible client                     │
│                                                            │
│  Every tool invocation is:                                 │
│    - Logged (Loki)                                         │
│    - Metered (Prometheus)                                  │
│    - Audited (Postgres audit table)                        │
│    - Gated (HITL approval for write operations)            │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

This means any AI agent — your LangGraph agents, Claude via MCP, future
systems — can discover and invoke any facility tool through a single gateway.
The facility becomes programmable infrastructure that AI agents operate on.

---

## 4 — Multi-Node Cluster Expansion

### Hardware Acquisition Plan (Under $1000 CAD)

| Item | Est. Cost | Role |
|------|-----------|------|
| NUC #1 (used, 16GB+ RAM) | $150-200 | Talos worker — CPU workloads, etcd voter |
| NUC #2 (used, 16GB+ RAM) | $150-200 | Talos worker — CPU workloads, etcd voter |
| NUC #3 (used, 8GB+ RAM) | $100-150 | Talos worker — lightweight / edge testing |
| 1TB NVMe for NAS/tower | $80-100 | Model cache overflow, bulk storage |
| Network switch (if needed) | $30-50 | Gigabit, unmanaged, 8-port |
| **Total** | **$510-700** | |

### Cluster Topology After Expansion

```
Tower (Prometheus):
  Role: control-plane + GPU worker
  Taints: gpu=true:NoSchedule
  Workloads: vLLM, LangGraph, Mem0, Qdrant, TEI (GPU-bound)

NUC-1:
  Role: worker + etcd voter
  Workloads: ATHENA, HERMES gateway, APOLLO API, Postgres, Redis

NUC-2:
  Role: worker + etcd voter
  Workloads: Prometheus, Grafana, Loki, AlertManager, NATS

NUC-3:
  Role: worker + etcd voter
  Workloads: Ingress controller, AdGuard Home, overflow scheduling

Scheduling strategy:
  - GPU workloads pinned to tower via nodeAffinity + RuntimeClass
  - CPU application workloads prefer NUCs via anti-affinity to tower
  - Observability stack isolated on NUC-2 so monitoring survives app failures
  - Postgres could migrate to NUC-1 for IO isolation if contention appears
```

This topology means you can demonstrate to an interviewer:

- **Node failure recovery:** drain a NUC, watch pods reschedule, show the Grafana dashboard
- **Resource scheduling:** GPU vs CPU workload separation with real constraints
- **HA patterns:** 3 etcd voters means the cluster survives losing one node
- **Operational maturity:** growing-pains log entries for real scheduling conflicts

---

## 5 — Repo Structure Across the Portfolio

```
github.com/ixxet/
├── homelab-gitops          ← PROMETHEUS: Flux GitOps state for the entire cluster
├── tower-bootstrap         ← PROMETHEUS: Talos bootstrap artifacts
├── athena                  ← Go facility check-in + capacity platform
├── hermes                  ← Go gateway + LangGraph ops bot
├── apollo                  ← Go API + SvelteKit PWA training platform
├── ashton-proto            ← Shared protobuf definitions (gRPC schemas)
├── ashton-mcp-gateway      ← MCP tool registry + agent gateway
├── User-Adaptive-Summarization_COMP385-402_Group-4_Winter2026
│                           ← Capstone (already shipped)
└── (COMP264 project)       ← AWS serverless project (course requirement)
```

Each repo has:
- `README.md` with architecture diagrams, tech stack, and run instructions
- `docs/growing-pains.md` documenting real failures and fixes
- `docs/adr/` for architecture decision records
- `.github/workflows/` for CI (build, test, lint, container push)
- `Dockerfile` (multi-stage, distroless final image)
- `deploy/` with Flux-compatible Kubernetes manifests
- `cmd/` for the CLI binary entry point
- `internal/` for core domain logic (not exported)
- `pkg/` for shared utilities (exported if needed cross-repo)
- `tools.json` MCP tool manifest for agent discoverability

---

## 6 — Execution Timeline

### Phase 0: Finish School (Now → Mid-April)
- Ship COMP 264 and COMP 385 deliverables
- Do NOT start building ASHTON applications
- DO: read "Learning Go" by Jon Bodner (O'Reilly), do the Go tour, write small programs
- DO: sketch database schemas on paper for ATHENA / HERMES / APOLLO
- DO: acquire NUC hardware if deals appear

### Phase 1: Platform Expansion (Mid-April → End of April, 2 weeks)
- Add Redis and NATS to Prometheus via Flux
- Deploy Prometheus + Grafana + Loki observability stack
- Join first NUC to the Talos cluster
- Validate multi-node scheduling, etcd quorum, pod failover
- Document everything in growing-pains.md

### Phase 2: ATHENA MVP (May, 4 weeks)
- Week 1: Go project scaffold, Postgres schema, sqlc codegen, migrations
- Week 2: Core domain (checkin service, capacity calculator), HTTP API, tests
- Week 3: CLI, Prometheus metrics, tap-in mock adapter, Grafana dashboard
- Week 4: PWA frontend (simple responsive web UI), deploy to cluster, MCP manifest
- End gate: ATHENA serving mock data on the cluster, observable in Grafana,
  CLI and API returning structured JSON, 80%+ test coverage

### Phase 3: HERMES MVP (June, 4 weeks)
- Week 1: Go WebSocket gateway, gRPC bridge to LangGraph, Tailscale auth middleware
- Week 2: LangGraph agent with ATHENA tools + booking tools, HITL approval flow
- Week 3: Equipment tracking, maintenance ticketing, NATS event subscriptions
- Week 4: CLI, metrics, Grafana dashboard, deploy to cluster, MCP manifest
- End gate: HERMES answering natural language queries over Tailscale, calling
  ATHENA for real capacity data, booking rooms with HITL confirmation

### Phase 4: APOLLO MVP (July, 4 weeks)
- Week 1: Workout logging API, Postgres schema, Go handlers, SvelteKit scaffold
- Week 2: Training history queries, volume/intensity feature extraction
- Week 3: Recommendation pipeline via LangGraph + vLLM, Mem0 integration
- Week 4: PWA polish, offline support, deploy to cluster, MCP manifest
- End gate: members logging workouts, viewing history, receiving AI-generated
  recommendations, all observable in Grafana

### Phase 5: Integration + Real Users (August, 4 weeks)
- Connect ATHENA to real tap-in system database (with Kareem's support)
- Onboard 10-20 Ashtonbee users onto APOLLO
- Collect real training data and recommendation feedback
- Build the pitch deck for Ashtonbee upper management
- Stress test the platform under real concurrent load
- Document incident responses in growing-pains.md

### Phase 6: Job Search (September)
- Portfolio is live: 3 interconnected Go services, real users, real telemetry
- Apply to 30+ infrastructure / platform / SRE roles
- Tailscale application with cover letter referencing the full platform
- Interview prep: every "tell me about a time" answer comes from real incidents

---

## 7 — The Narrative for Interviews

When someone asks "tell me about your experience," the answer is:

"I designed and operate a distributed AI platform on bare-metal Kubernetes that serves
a university athletics facility. The platform includes three interconnected Go services —
a real-time capacity tracking system integrated with the facility's check-in hardware,
an AI operations bot that staff access over Tailscale from their phones, and a training
recommendation engine used by facility members. Everything runs on a multi-node Talos
cluster I built, with GitOps deployment through Flux, encrypted secrets, Prometheus
observability, and an MCP-compatible agent gateway so AI systems can programmatically
interact with any facility service. The platform handles real daily traffic from
approximately 300 users, and I've documented every failure and recovery in an
operational journal."

That's not a student talking. That's an infrastructure engineer talking.
