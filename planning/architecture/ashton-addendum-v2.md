# ASHTON Platform — Architecture Addendum v2

> Status: future/background reference. This file preserves deeper planning
> rationale and deferred ideas; it does not describe the current runtime stack.
>
> Use this file for:
> - technology alternatives and future tradeoffs
> - schema sketches worth adapting later
> - deferred infrastructure and scaling ideas
>
> Use these files instead for current truth:
> - `planning/repo-briefs/`
> - repo-local `README.md`, `docs/roadmap.md`, ADRs, migrations, and schemas
> - `planning/IMPLEMENTATION-BOARD.md`
> - `planning/sprints/TRACER-MATRIX.md`
>
> If this addendum conflicts with current repo docs, this addendum loses.
>
> Superseded or re-scoped areas to watch for:
> - `check-in` terminology is now `presence` in current working docs
> - APOLLO now separates visit history from workout history
> - APOLLO owns member intent while ATHENA owns physical truth

**Covers:** Technology alternatives, Rust positioning, matchmaking system (ARES),
tap-in adapter patterns, Go learning path, database strategy, and section-by-section outlines.

---

## 1 — Technology Alternatives: Industry Standard vs Up-and-Coming

Every major decision in the ASHTON platform has at least two viable paths.
This table maps the choice made, the incumbent alternative, and the emerging
challenger — with reasoning for each.

### PROMETHEUS (Infrastructure Layer)

| Domain | Chosen | Industry Standard | Up-and-Coming | Why This Choice |
|--------|--------|-------------------|---------------|-----------------|
| **Container orchestration** | Kubernetes (Talos) | Kubernetes (kubeadm/EKS/GKE) | Nomad (HashiCorp) | K8s is the industry lingua franca. Talos makes it immutable and API-driven — no SSH, no drift. Nomad is simpler but has a fraction of the ecosystem and job market signal. |
| **OS** | Talos Linux | Ubuntu Server / Flatcar | Bottlerocket (AWS), Kairos | Talos is purpose-built immutable K8s OS. Bottlerocket is AWS-tied. Kairos is interesting but less battle-tested. Talos is the most opinionated and secure choice for bare-metal. |
| **CNI / Networking** | Cilium | Calico | Cilium (it IS the up-and-comer that won) | Cilium replaced Calico as the default in most new clusters. eBPF-based, replaces kube-proxy entirely, does L2/L4/L7 natively. No contest for a greenfield cluster. |
| **GitOps** | Flux | ArgoCD | Flux (neck-and-neck) | ArgoCD has a richer UI and is more popular by GitHub stars. Flux is more composable, lighter, and better for single-operator platforms where you don't need a dashboard — you need reconciliation. ArgoCD would also be a valid choice. |
| **Secrets** | SOPS + age | HashiCorp Vault | External Secrets Operator + 1Password/Doppler | Vault is overkill for a homelab (operationally heavy). SOPS is file-based, GitOps-native, and dead simple. ESO is gaining traction for teams using cloud secret managers. |
| **Ingress** | Cilium Gateway API | Nginx Ingress / Traefik | Cilium Gateway API / Envoy Gateway | Gateway API is the successor to Ingress resources. Cilium implements it natively, so no additional controller needed. Traefik is mature and featureful but adds another moving part. |
| **Observability** | Prometheus + Grafana + Loki | Datadog / New Relic (SaaS) | VictoriaMetrics + Grafana, or OpenTelemetry Collector + Tempo | Prometheus/Grafana is the CNCF standard. VictoriaMetrics is a Prometheus-compatible drop-in with better performance at scale. OpenTelemetry is the future for traces but adds complexity. Start with Prometheus, migrate later if needed. |
| **DNS** | AdGuard Home | CoreDNS (cluster) + Pi-hole (LAN) | Blocky | AdGuard is already deployed and working. Blocky is lighter and more K8s-native but less known. Doesn't matter much — DNS is DNS. |
| **Remote access** | Tailscale | WireGuard (manual) | Tailscale (it IS the standard now), Netbird, Headscale | Tailscale is the product you're targeting for employment. Using it in your own infra is both practical and narratively perfect. Headscale is the self-hosted control plane if you ever want to decouple from Tailscale's coordination server. |
| **Model serving** | vLLM | Ollama / TGI (Text Generation Inference) | vLLM (it won), SGLang | vLLM is the production standard for GPU inference. SGLang is newer and faster for some workloads but less mature. Ollama is for hobbyists. Decision already made correctly. |
| **Agent orchestration** | LangGraph | LangChain / CrewAI / AutoGen | Strands (AWS), DSPy, ControlFlow | LangGraph gives you explicit graph control, durable checkpoints, HITL, and tool calling. CrewAI is opinionated and higher-level. DSPy is for prompt optimization, not orchestration. Strands is AWS-native and new. LangGraph remains the best fit for infrastructure-minded builders. |
| **Semantic memory** | Mem0 + Qdrant | LangMem / Zep | Letta (MemGPT) | Mem0 is already deployed and validated. Letta is interesting (memory-in-the-loop) but operationally heavier. Qdrant is best-in-class for the vector store. TEI for embeddings keeps everything local. |
| **Event bus** | NATS | RabbitMQ / Kafka | NATS JetStream / Redpanda | Kafka is massive overkill for facility-scale events. RabbitMQ is solid but heavier than NATS. Redpanda is Kafka-compatible but simpler. NATS is the lightest operational footprint with JetStream available for durable streams if needed. |
| **Cache** | Redis | Memcached | Dragonfly / Valkey / KeyDB | Redis is the universal standard. Dragonfly claims 25x throughput but is less battle-tested. Valkey is the Redis fork after the license change — functionally identical, truly open source. Consider Valkey over Redis for licensing hygiene. |

### ATHENA (Facility Intelligence)

| Domain | Chosen | Industry Standard | Up-and-Coming | Why This Choice |
|--------|--------|-------------------|---------------|-----------------|
| **Language** | Go | Python (Django/FastAPI), Java (Spring) | Go (it IS the standard for infra), Rust | Go is the Tailscale language, the K8s language, the infrastructure language. Python is too slow for the backend-systems narrative you're building. Java is enterprise bloat. |
| **HTTP framework** | chi | Gin, Echo | chi, Fiber | chi is minimal and idiomatic. Gin is more popular but adds abstractions. Fiber is fast but uses fasthttp (non-standard). chi uses net/http directly, which means your code is portable. |
| **SQL toolkit** | sqlc | GORM, Ent | sqlc, Bun | GORM is a full ORM — hides the SQL, makes debugging harder, and generates unpredictable queries. sqlc generates type-safe Go from handwritten SQL — you own every query, it's auditable, and it's fast. Bun is newer and nice but less adopted. |
| **CLI** | cobra + viper | urfave/cli | cobra (it IS the standard), Kong | cobra is used by kubectl, docker, gh, hugo, and essentially every major Go CLI. Non-negotiable choice. |
| **Database** | Postgres 16 | MySQL 8, MariaDB | Postgres (it IS the standard for new projects), CockroachDB, TiDB | More on this below in the database section. |
| **Migrations** | golang-migrate | goose, atlas | Atlas (declarative) | golang-migrate is simple file-based migrations. Atlas is declarative (you define desired state, it computes the diff) — more modern but more magic. Start with golang-migrate for explicitness, consider Atlas later. |
| **Prediction** | EWMA + historical binning | Prophet, ARIMA | Temporal fusion transformers, NeuralProphet | A facility with 300 users doesn't need a neural network. EWMA with confidence bands is honest, auditable, and performant. If someone asks "why not Prophet?" in an interview, the answer is "because the data doesn't justify the complexity." |

### HERMES (Operations Bot)

| Domain | Chosen | Industry Standard | Up-and-Coming | Why This Choice |
|--------|--------|-------------------|---------------|-----------------|
| **Gateway protocol** | WebSocket + gRPC | REST polling | WebTransport, Server-Sent Events | WebSocket gives bidirectional streaming for real-time chat. gRPC connects the Go gateway to the Python LangGraph agent with typed schemas. WebTransport is HTTP/3-based and emerging but browser support is still patchy. |
| **Go↔Python bridge** | gRPC streaming | REST + JSON | Connect-go (Buf), Cap'n Proto | gRPC is the industry standard for polyglot service communication. Connect-go is newer and HTTP-native but less tooling. Cap'n Proto is faster but niche. |
| **Auth** | Tailscale WhoIs | OAuth2 / OIDC (Keycloak, Auth0) | Tailscale + short-lived tokens | Tailscale WhoIs gives you zero-config identity for anyone on the mesh. No login page, no token management, no OAuth dance. This is the most elegant auth story possible for an internal tool. |

### APOLLO (Training Intelligence)

| Domain | Chosen | Industry Standard | Up-and-Coming | Why This Choice |
|--------|--------|-------------------|---------------|-----------------|
| **Frontend** | SvelteKit | React (Next.js), Vue (Nuxt) | SvelteKit (gaining fast), Solid, Astro | SvelteKit compiles away the framework. Smaller bundles, faster loads, better PWA. You already used it in the capstone — consistency matters. |
| **PWA offline** | Service worker + IndexedDB | Workbox (Google) | Serwist (Workbox successor) | Serwist is the modern fork of Workbox, maintained by the community after Google deprioritized Workbox. Use Serwist for service worker generation. |
| **Matchmaking** | OpenSkill (Weng-Lin) | Elo, Glicko-2 | OpenSkill, Elo-MMR | Full breakdown below. |

---

## 2 — Where Rust Fits (and Where It Doesn't... Yet)

Your friend at ClearStreet is right that Rust is a serious language — ClearStreet almost certainly uses it for low-latency financial systems where garbage collection pauses are unacceptable. Here's the honest breakdown for your situation.

### Why Go First, Not Rust

Rust prioritizes maximum safety and control, even if it slows teams down. Go prioritizes speed of development, onboarding, and long-term maintainability across growing teams. You're a solo operator who needs to ship three services in four months. Go's simpler syntax and tooling reduce friction across the entire development lifecycle. Writing the same service in Rust would take roughly 2-3x longer due to the borrow checker learning curve, and the infrastructure job market overwhelmingly hires for Go — Kubernetes, Docker, Terraform, Prometheus, Grafana, Tailscale, CockroachDB, and etcd are all Go.

### Where Rust Enters the ASHTON Platform

Rust has a specific, defensible place in this portfolio — but as a **Phase 2 addition**, not a Phase 1 language:

**The MCP Gateway.** This is the highest-throughput, lowest-latency component in the platform. Every agent tool invocation routes through it. Every request must be authenticated, metered, logged, and forwarded with minimal overhead. This is exactly the kind of service where Rust's zero-cost abstractions and memory efficiency shine. After shipping the gateway in Go first (to get it working), rewriting it in Rust using Axum (the modern async Rust web framework) demonstrates:

- Polyglot capability (Go + Rust + Python in one platform)
- Understanding of when Rust is justified vs overkill
- The exact profile ClearStreet, Cloudflare, and Tailscale hire for

**The `skillratings` crate.** There's an existing Rust library called `skillratings` implementing Elo, Glicko-2, TrueSkill, and Weng-Lin algorithms. The matchmaking engine (ARES) could use this crate compiled to a shared library or WebAssembly module, called from Go via FFI or as a sidecar. This is a natural Rust integration point that doesn't require rewriting any core service.

**Timeline for Rust:**
- Months 1-4: Ship everything in Go. Learn Go deeply by building real things.
- Month 5+: Rewrite the MCP gateway in Rust (Axum + tokio). Integrate skillratings crate.
- Narrative: "I built the platform in Go for velocity, then rewrote the hottest path in Rust for performance. Here are the benchmarks comparing the two implementations."

That story is infinitely more impressive than "I tried to learn two systems languages at once and shipped nothing."

---

## 3 — ARES: Matchmaking and Team Formation System

**New project within APOLLO — not a separate repo.**

This is the feature that makes APOLLO more than a workout tracker. Students can opt into matchmaking for pickup games, intramural teams, training partners, and fitness challenges. The system ranks participants, forms balanced teams, and tracks competitive outcomes.

### The Algorithms (All Open Source, No License Issues)

| Algorithm | Origin | License | Best For | Library |
|-----------|--------|---------|----------|---------|
| **Elo** | Chess (1960s) | Public domain | 1v1 matchups, simple leaderboards | Any — trivial to implement from scratch |
| **Glicko-2** | Mark Glickman (2001) | Open specification | 1v1 with rating confidence and volatility | `go-glicko` (Go), `skillratings` (Rust) |
| **TrueSkill** | Microsoft (2005) | **Patented by Microsoft** — cannot use commercially without license | Team-based games | **AVOID — patent encumbered** |
| **OpenSkill / Weng-Lin** | Weng & Lin (2011), open-source implementation (2024) | **MIT License — fully free** | Multi-team, asymmetric, multiplayer | `openskill.py` (Python), `skillratings` (Rust) |
| **Elo-MMR** | Ebtekar & Liu (2021) | **Open source (MIT)** | Massive multiplayer competitions, very fast | `Elo-MMR` (Rust) |

### The Choice: OpenSkill (Weng-Lin Bayesian)

OpenSkill provides a more accurate representation of individual player contributions and speeds up the computation of ranks compared to Elo and Glicko-2. It's the **license-free alternative to TrueSkill** — mathematically similar Bayesian approach, supports team-based and multiplayer scenarios, MIT licensed, and has implementations in Python, Rust, JavaScript, Kotlin, Elixir, and Lua.

**Why not TrueSkill?** It's patented by Microsoft. You can study it, understand it, but you cannot deploy it commercially. OpenSkill achieves comparable accuracy without the legal risk.

**Why not just Elo?** Elo was designed for chess — 1v1 with no team dynamics. It doesn't model uncertainty (new players are treated the same as veterans), and it can't handle team-based matchups. For a facility where you're matching 5v5 basketball teams or pairing training partners with different experience levels, you need Bayesian rating with team support.

### How ARES Works in APOLLO

```
Student opts into matchmaking → Profile created with:
  - Sport/activity preferences
  - Self-reported skill level (seed)
  - Physical attributes (optional: height, weight for contact sports)
  - Schedule availability
  - Training goals

System assigns initial OpenSkill rating:
  μ (mu) = 25.0 (mean skill estimate)
  σ (sigma) = 8.33 (uncertainty — high for new players)

Match/game occurs → outcome reported → ratings update:
  Winner's μ increases, σ decreases (more confident)
  Loser's μ decreases, σ decreases (more confident)
  Margin of victory can modulate update magnitude

Matchmaking algorithm:
  1. Filter by sport, availability, and constraints
  2. Score potential matchups by expected closeness (|μ₁ - μ₂| minimized)
  3. Penalize matchups with high σ difference (don't match veterans with unknowns)
  4. For team formation: balance total team μ within threshold
  5. Return ranked matchup suggestions
```

### ARES Tech Stack

| Component | Technology | Notes |
|-----------|-----------|-------|
| Rating engine | **OpenSkill via Go implementation** or **Rust `skillratings` crate via FFI/sidecar** | Go-native is simpler. Rust sidecar is the future Rust integration point. |
| Match solver | **Go** — constraint satisfaction with greedy optimization | Not NP-hard at facility scale. Greedy with quality threshold works fine for <100 concurrent players. |
| Storage | **Postgres (apollo schema, ares_* tables)** | Ratings, match history, player profiles, team compositions. |
| Real-time queue | **NATS + Redis** | Players enter queue via NATS publish. Redis sorted set ranks waiting players by μ for fast nearest-skill lookup. |
| CLI | `apollo match find`, `apollo match history`, `apollo rating show` | Same cobra CLI, extended with matchmaking subcommands. |
| MCP tools | `ares.find_match`, `ares.form_teams`, `ares.get_rating` | HERMES can say "find me 10 players for a pickup basketball game at 6pm" and ARES handles the rest. |

---

## 4 — Tap-In Adapter Pattern (Deep Explanation)

You asked what happens if there's no API. This is exactly what the adapter pattern solves.

### What Is It

The tap-in adapter is a **pluggable interface** — a contract that says "give me check-in events" without caring how those events are obtained. The core ATHENA service never talks to the proprietary system directly. It talks to the adapter interface. The adapter is the only component that knows how to extract data from whatever source exists.

### Why This Matters

The proprietary system at Ashtonbee might expose:
- A SQL database you can read from (best case)
- An HTTP API (good case)
- A CSV/Excel export (okay case)
- Absolutely nothing — a black box with no access (worst case)

The adapter pattern means **you build ATHENA regardless.** The core service doesn't change. Only the adapter implementation changes based on what access you actually get.

### The Implementations

```go
// The interface — this never changes
type TapInAdapter interface {
    Poll(ctx context.Context, since time.Time) ([]CheckinEvent, error)
    Health(ctx context.Context) error
}

// Implementation 1: MockAdapter (development + demo)
// Generates realistic synthetic events based on historical patterns.
// This is what you build with FIRST. The entire ATHENA service runs on this.
// Your Grafana dashboards, your CLI, your predictions — all work with mock data.
// When someone says "show me ATHENA working," you demo with MockAdapter.
type MockAdapter struct { ... }

// Implementation 2: DatabaseAdapter (read-only access to proprietary DB)
// If you get credentials to the tap-in system's database, this adapter
// runs a SELECT query on their events table, filtered by timestamp.
// Read-only. You never write to their database. You never modify their data.
type DatabaseAdapter struct {
    db *sql.DB  // connection to THEIR database, read-only
}

// Implementation 3: CSVAdapter (manual export)
// If the proprietary system can export attendance data as CSV/Excel,
// this adapter watches a directory for new files and imports them.
// Not real-time, but gives you real historical data for predictions.
type CSVAdapter struct {
    watchDir string
}

// Implementation 4: ScreenScrapeAdapter (last resort)
// If the system has a web interface but no API/DB access,
// you could scrape the attendance page periodically.
// Fragile, but functional. Document it as a workaround.
type ScreenScrapeAdapter struct {
    baseURL  string
    username string
    password string
}

// Implementation 5: HardwareAdapter (nuclear option — build your own reader)
// If all else fails, buy a USB RFID/NFC reader (~$30),
// read student ID cards directly, and generate your own events.
// This is actually the most impressive option because it means
// you bypassed the proprietary system entirely with your own hardware.
type HardwareAdapter struct {
    devicePath string  // /dev/ttyUSB0 or similar
}
```

### The Key Insight

**MockAdapter is not a fallback. It IS the primary development path.** You build the entire system against mock data. You demo against mock data. You test against mock data. The real adapter is plugged in later as a configuration change — literally swapping an environment variable:

```yaml
# In the Kubernetes deployment manifest
env:
  - name: ATHENA_TAP_ADAPTER
    value: "mock"          # development
  # value: "database"      # when you get DB access
  # value: "csv"           # when you get export access
  # value: "hardware"      # when you buy an RFID reader
```

This is the same pattern that your capstone used with `USE_MOCK_LLM` — you already understand it. ATHENA extends the same idea to a hardware integration boundary.

---

## 5 — Database Strategy

### Why Postgres, Not MySQL, Not MongoDB

**Postgres is the correct choice.** Here's why, with no ambiguity:

**Postgres vs MySQL:** MySQL had a moment in the 2000s-2010s as the default web database. In 2026, Postgres has won the new-project default decisively. Postgres has better JSON support (JSONB with indexing), better full-text search, better extension ecosystem (PostGIS, pgvector, pg_trgm), better standards compliance, better concurrent write handling (MVCC), advisory locks for distributed coordination, and materialized views for precomputed aggregations. Every major infrastructure company (Tailscale, Cloudflare, GitLab, Supabase) runs Postgres. MySQL is legacy — maintained, functional, but not what you'd choose for a new system in 2026.

**Postgres vs MongoDB (NoSQL):** MongoDB's pitch was "schema-free for rapid prototyping." In practice, you always end up with a schema — it's just implicit and enforced in application code instead of the database. For the ASHTON platform, your data is inherently relational: users have workouts, workouts have exercises, exercises have sets, users have ratings, ratings update based on matches that involve teams of users. Trying to model this in MongoDB would create either massive document bloat or reference-heavy documents that are just a worse relational database. Postgres with JSONB columns gives you the flexibility of schemaless data where you need it (user preferences, equipment metadata) while keeping the relational guarantees where they matter (foreign keys, transactions, aggregations).

**The advanced Postgres play:** When you're ready for the matchmaking system, Postgres has `pgvector` for similarity search — you could embed player profiles and find nearest-skill matches using vector distance instead of just raw μ comparison. That's a future enhancement that wouldn't require a new database.

### Schema Architecture (Per-Service Isolation)

Each service gets its own Postgres **schema** (not database) within the shared cluster instance. This gives logical isolation while sharing the same connection pool and backup pipeline.

```sql
-- ATHENA schema
CREATE SCHEMA athena;

CREATE TABLE athena.facilities (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name        TEXT NOT NULL UNIQUE,
    capacity    INT NOT NULL,
    created_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE athena.checkin_events (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    facility_id UUID NOT NULL REFERENCES athena.facilities(id),
    student_id  TEXT NOT NULL,           -- from tap-in adapter
    direction   TEXT NOT NULL CHECK (direction IN ('in', 'out')),
    source      TEXT NOT NULL,           -- 'mock', 'database', 'hardware', etc.
    recorded_at TIMESTAMPTZ NOT NULL,    -- when the tap occurred
    ingested_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    metadata    JSONB                    -- flexible field for adapter-specific data
);

CREATE INDEX idx_checkin_events_facility_time
    ON athena.checkin_events (facility_id, recorded_at DESC);

CREATE INDEX idx_checkin_events_student
    ON athena.checkin_events (student_id, recorded_at DESC);

-- Materialized view for hourly aggregation (capacity prediction input)
CREATE MATERIALIZED VIEW athena.hourly_counts AS
SELECT
    facility_id,
    date_trunc('hour', recorded_at) AS hour,
    EXTRACT(dow FROM recorded_at)::int AS day_of_week,
    COUNT(*) FILTER (WHERE direction = 'in') AS checkins,
    COUNT(*) FILTER (WHERE direction = 'out') AS checkouts
FROM athena.checkin_events
GROUP BY facility_id, date_trunc('hour', recorded_at), EXTRACT(dow FROM recorded_at);

-- Refresh this materialized view on a schedule (pg_cron or application-side)
```

```sql
-- HERMES schema
CREATE SCHEMA hermes;

CREATE TABLE hermes.rooms (
    id       UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name     TEXT NOT NULL,
    capacity INT,
    features JSONB  -- {"has_projector": true, "has_mirrors": true, ...}
);

CREATE TABLE hermes.bookings (
    id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    room_id    UUID NOT NULL REFERENCES hermes.rooms(id),
    booked_by  TEXT NOT NULL,      -- Tailscale identity or staff name
    reason     TEXT,
    starts_at  TIMESTAMPTZ NOT NULL,
    ends_at    TIMESTAMPTZ NOT NULL,
    status     TEXT NOT NULL DEFAULT 'confirmed' CHECK (status IN ('confirmed','cancelled')),
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    -- Prevent overlapping bookings for the same room
    EXCLUDE USING gist (
        room_id WITH =,
        tstzrange(starts_at, ends_at) WITH &&
    ) WHERE (status = 'confirmed')
);

CREATE TABLE hermes.equipment (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name            TEXT NOT NULL,
    location        TEXT,
    status          TEXT NOT NULL DEFAULT 'operational',
    last_serviced   TIMESTAMPTZ,
    metadata        JSONB
);

CREATE TABLE hermes.maintenance_tickets (
    id           UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    equipment_id UUID REFERENCES hermes.equipment(id),
    reported_by  TEXT NOT NULL,
    description  TEXT NOT NULL,
    priority     TEXT NOT NULL DEFAULT 'normal' CHECK (priority IN ('low','normal','high','urgent')),
    status       TEXT NOT NULL DEFAULT 'open' CHECK (status IN ('open','in_progress','resolved','closed')),
    created_at   TIMESTAMPTZ NOT NULL DEFAULT now(),
    resolved_at  TIMESTAMPTZ
);

-- Audit log for all HERMES actions (agent accountability)
CREATE TABLE hermes.audit_log (
    id         BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    actor      TEXT NOT NULL,        -- staff name, agent ID, or 'system'
    action     TEXT NOT NULL,        -- 'booking.create', 'maintenance.file', etc.
    target     JSONB NOT NULL,       -- {"type":"booking","id":"..."}
    metadata   JSONB,               -- request context, tool invocation details
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

```sql
-- APOLLO schema
CREATE SCHEMA apollo;

CREATE TABLE apollo.users (
    id             UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    student_id     TEXT UNIQUE,
    display_name   TEXT NOT NULL,
    email          TEXT,
    preferences    JSONB DEFAULT '{}',
    created_at     TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE apollo.workouts (
    id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id    UUID NOT NULL REFERENCES apollo.users(id),
    logged_at  TIMESTAMPTZ NOT NULL DEFAULT now(),
    notes      TEXT,
    metadata   JSONB      -- {"mood": "good", "sleep_hours": 7, "rpe_session": 7}
);

CREATE TABLE apollo.exercises (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workout_id  UUID NOT NULL REFERENCES apollo.workouts(id),
    name        TEXT NOT NULL,       -- normalized: 'back_squat', 'bench_press'
    sets        INT NOT NULL,
    reps        INT NOT NULL,
    weight_kg   NUMERIC(6,2),
    rpe         NUMERIC(3,1),        -- rate of perceived exertion (1-10)
    notes       TEXT
);

CREATE INDEX idx_exercises_user_name
    ON apollo.exercises (name, (SELECT user_id FROM apollo.workouts w WHERE w.id = workout_id));

-- Training volume view for recommendation engine
CREATE VIEW apollo.weekly_volume AS
SELECT
    w.user_id,
    e.name AS exercise,
    date_trunc('week', w.logged_at) AS week,
    SUM(e.sets * e.reps * COALESCE(e.weight_kg, 0)) AS total_volume,
    AVG(e.rpe) AS avg_rpe,
    COUNT(DISTINCT w.id) AS session_count
FROM apollo.workouts w
JOIN apollo.exercises e ON e.workout_id = w.id
GROUP BY w.user_id, e.name, date_trunc('week', w.logged_at);

-- ARES matchmaking tables
CREATE TABLE apollo.ares_ratings (
    id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id    UUID NOT NULL REFERENCES apollo.users(id),
    activity   TEXT NOT NULL,        -- 'basketball', 'badminton', 'volleyball', etc.
    mu         NUMERIC(8,4) NOT NULL DEFAULT 25.0,
    sigma      NUMERIC(8,4) NOT NULL DEFAULT 8.3333,
    games_played INT NOT NULL DEFAULT 0,
    last_played  TIMESTAMPTZ,
    UNIQUE (user_id, activity)
);

CREATE TABLE apollo.ares_matches (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    activity    TEXT NOT NULL,
    match_type  TEXT NOT NULL CHECK (match_type IN ('1v1', 'team', 'ffa')),
    played_at   TIMESTAMPTZ NOT NULL DEFAULT now(),
    metadata    JSONB       -- team compositions, scores, duration
);

CREATE TABLE apollo.ares_match_players (
    match_id    UUID NOT NULL REFERENCES apollo.ares_matches(id),
    user_id     UUID NOT NULL REFERENCES apollo.users(id),
    team        INT,                 -- team number (null for FFA)
    outcome     TEXT NOT NULL CHECK (outcome IN ('win', 'loss', 'draw')),
    mu_before   NUMERIC(8,4) NOT NULL,
    mu_after    NUMERIC(8,4) NOT NULL,
    sigma_before NUMERIC(8,4) NOT NULL,
    sigma_after  NUMERIC(8,4) NOT NULL,
    PRIMARY KEY (match_id, user_id)
);

-- Recommendation feedback loop
CREATE TABLE apollo.recommendations (
    id           UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id      UUID NOT NULL REFERENCES apollo.users(id),
    content      JSONB NOT NULL,      -- the full recommendation payload
    model_used   TEXT NOT NULL,       -- 'mistral-7b-instruct-v0.3'
    latency_ms   INT,
    feedback     TEXT CHECK (feedback IN ('helpful', 'not_helpful', 'ignored')),
    created_at   TIMESTAMPTZ NOT NULL DEFAULT now(),
    feedback_at  TIMESTAMPTZ
);
```

---

## 6 — Go Learning Path (Reach for the Stars Edition)

No tutorials. No toy projects. You learn Go by building ATHENA.

### Week 0 (During school, 30 min/day)

**Read, don't code.** Absorb Go's philosophy before touching a keyboard.

1. **"Go by Example"** (gobyexample.com) — skim the whole site in one sitting. Don't memorize. Get the shape of the language.
2. **"Effective Go"** (go.dev/doc/effective_go) — the official style guide. Read it like a constitution.
3. **"How I Write HTTP Services in Go After 13 Years" by Mat Ryer** — this single blog post teaches you more about production Go than most books. It's the chi + handler pattern you'll use in ATHENA.
4. **Study these repos** (read the source, don't clone):
   - `tailscale/tailscale` — the company you're targeting. Read their `cmd/` and `internal/` structure.
   - `prometheus/prometheus` — the observability tool you're deploying. See how Go structures a real project.
   - `charmbracelet/bubbletea` — beautiful Go TUI framework. Not for ASHTON, but shows what idiomatic Go looks like.

### Week 1-2 (First week after graduation)

**Build the ATHENA scaffold. Learn by hitting compiler errors.**

- `go mod init github.com/ixxet/athena`
- Set up the project structure: `cmd/athena/main.go`, `internal/`, `pkg/`
- Write the first HTTP handler that returns `{"status":"ok"}` using chi
- Write the first cobra CLI command that prints `athena version`
- Write the first sqlc query against a local Postgres
- Write the first test using `testing` package
- Set up GitHub Actions CI: `go build`, `go test`, `go vet`, `golangci-lint`

**You will be slow. You will fight the compiler. That IS the learning.**

### Week 3-4

**Build the core domain. This is where Go clicks.**

- Implement the `TapInAdapter` interface and `MockAdapter`
- Build the check-in service with repository pattern (sqlc-generated)
- Add Prometheus metrics using `prometheus/client_golang`
- Add structured logging with `slog`
- Write integration tests with `testcontainers-go` (real Postgres in CI)

### Week 5+

**You're no longer learning Go. You're writing Go.**

By this point you've written enough Go to be productive. HERMES and APOLLO build on the same patterns. Each new service reinforces the muscle memory.

### Go Resources (Prioritized)

| Priority | Resource | Why |
|----------|----------|-----|
| 1 | gobyexample.com | Fast syntax reference |
| 2 | "Effective Go" (go.dev) | Official idioms |
| 3 | Mat Ryer's HTTP services post | Production patterns |
| 4 | "Learning Go" by Jon Bodner (O'Reilly) | Comprehensive book if you want depth |
| 5 | Tailscale blog posts on Go | How your target company uses it |
| 6 | "Let's Go" and "Let's Go Further" by Alex Edwards | Best web-dev-in-Go books |
| 7 | GopherCon talks on YouTube | Community knowledge |

---

## 7 — Section Outlines per Project

### ATHENA Outline (Repo README Structure)

```
# ATHENA — Facility Intelligence Platform

## What It Does
## Why It Exists
## Architecture
  - System diagram
  - Data flow: tap-in → adapter → service → Postgres → prediction engine
  - Prometheus metrics reference
## Tech Stack (with justification table)
## Quick Start
  - Prerequisites (Go 1.22+, Postgres, NATS)
  - Run locally with MockAdapter
  - Run on the cluster via Flux
## CLI Reference
  - athena checkin [list|count]
  - athena capacity [current|predict|history]
  - athena adapter [status|switch]
## API Reference
  - OpenAPI spec (auto-generated from chi routes)
## MCP Tool Manifest
## Database Schema
## Observability
  - Grafana dashboard screenshots
  - Alert rules
## Testing
  - Unit tests, integration tests (testcontainers), coverage
## Growing Pains
  - Link to docs/growing-pains.md
## Architecture Decision Records
  - Link to docs/adr/
```

### HERMES Outline

```
# HERMES — Facility Operations Bot

## What It Does
## Why It Exists
## Architecture
  - Go gateway ↔ gRPC ↔ LangGraph agent
  - Tool calling flow
  - HITL approval pattern
  - Cross-service communication (ATHENA, ARES)
## Tech Stack
## Quick Start
  - Requires: Tailscale mesh membership
  - Demo mode (mock tools)
  - Live mode (connected to ATHENA + APOLLO)
## Conversation Examples
## CLI Reference
  - hermes book [create|list|cancel]
  - hermes equip [list|status|report]
  - hermes maint [file|list|resolve]
## API Reference
## MCP Tool Manifest
## Database Schema
## Observability
## Testing
## Growing Pains
## ADRs
```

### APOLLO Outline (Including ARES)

```
# APOLLO — Training Intelligence Engine

## What It Does
## Why It Exists
## Architecture
  - Workout logging pipeline
  - Recommendation engine (LangGraph + vLLM)
  - ARES matchmaking subsystem
## Tech Stack
## Quick Start
  - PWA: open https://apollo.home.arpa
  - CLI: apollo workout log / apollo recommend / apollo match find
  - Mock mode vs live LLM mode
## User Guide
  - Logging workouts
  - Viewing training history
  - Getting recommendations
  - Opting into matchmaking
  - Forming teams
## ARES Matchmaking
  - Algorithm: OpenSkill (Weng-Lin Bayesian)
  - How ratings work (mu, sigma)
  - Team formation algorithm
  - Fairness constraints
## CLI Reference
## API Reference
## MCP Tool Manifest
## Database Schema
## Observability
## Testing
## Growing Pains
## ADRs
```

### MCP Gateway Outline

```
# ASHTON MCP Gateway — Agent Orchestration Layer

## What It Does
## Why It Exists (the AI-agent-native thesis)
## Architecture
  - Tool registry pattern
  - Request routing and auth
  - Audit logging
  - HITL gating for write operations
## Registered Tools
  - ATHENA tools
  - HERMES tools
  - APOLLO tools
  - ARES tools
  - System tools (cluster health, Flux status)
## Protocol
  - MCP-compatible JSON-RPC
  - Tool discovery endpoint
  - Streaming support
## Tech Stack
  - Go (Phase 1) → Rust/Axum (Phase 2)
## Quick Start
## Security Model
## Observability
## Testing
## Growing Pains
## ADRs
```

---

## 8 — NUC Acquisition Strategy

You said you have 4 NUCs (3 with family, 1 is MIMIR). Here's the optimal reacquisition plan:

### What You Need

| Role | Min RAM | Min Storage | Priority |
|------|---------|-------------|----------|
| Worker + etcd | 16 GB | 256 GB SSD | High (need 2 for HA quorum) |
| Worker + etcd | 16 GB | 256 GB SSD | High |
| Worker (light) | 8 GB | 128 GB SSD | Medium |
| MIMIR (keep as-is) | Existing | Existing | Already deployed |

### Strategy

1. **Reclaim 2 NUCs from family** — offer to replace them with a Chromecast or similar if they're using them for media. Or just explain the career investment.
2. **Upgrade RAM if needed** — used DDR4 SODIMMs are cheap ($15-25 for 8GB sticks). Max out to 16GB+ on each.
3. **Add SSDs if missing** — 256GB SATA or NVMe SSDs run $25-35 used.
4. **The Lenovo laptop** — keep as a portable operator workstation, not a cluster node. Laptops in K8s clusters cause problems (lid close = node death, WiFi = unreliable, battery management interferes with uptime).
5. **Total cost for 3-NUC expansion:** $50-150 if you already own the hardware and just need RAM/SSD upgrades.

### Cluster Topology After Expansion

```
Tower (5950X + 3090):    control-plane + GPU worker     [taints: gpu=true]
NUC-1 (16GB):            worker + etcd voter            [app workloads]
NUC-2 (16GB):            worker + etcd voter            [observability]
NUC-3 (8-16GB):          worker + etcd voter            [overflow + ingress]
MIMIR:                   stays off-cluster              [NAS, archive sink, NFS]
Lenovo laptop:           operator workstation           [kubectl, Tailscale, development]
```

Three etcd voters (NUC-1, NUC-2, NUC-3) + tower as control-plane gives you
a 4-node cluster that survives any single node failure with quorum intact.

---

## 9 — What This Document Is NOT

This document is architecture and planning. It is not:
- A tutorial (you'll learn by building, not reading)
- A guarantee (execution is everything, plans are free)
- Complete (it will evolve as you build and hit real problems)

The next step is finishing school. Then the first line of Go code in ATHENA.
Everything else follows from shipping.
