# hermes

AI-powered facility operations assistant for Ashtonbee Athletics staff. Accessible over Tailscale from any device — ask questions, book rooms, report equipment issues, and receive proactive capacity alerts through natural conversation.

HERMES is a Go WebSocket gateway connected to a LangGraph agent with tool-calling capabilities. It doesn't just answer questions — it takes real actions (create bookings, file maintenance tickets, send alerts) with human-in-the-loop confirmation on write operations.

## Architecture

```
  Staff phone              ┌─────────────────────────────────────────┐
  (via Tailscale)          │              HERMES                      │
       │                   │                                          │
       ▼                   │  ┌────────────┐     ┌────────────────┐  │
  ┌──────────┐             │  │ Go Gateway │     │ LangGraph Agent│  │
  │WebSocket │────────────▶│  │            │◄───▶│ (Python)       │  │
  │  + REST  │             │  │ Tailscale  │gRPC │                │  │
  └──────────┘             │  │ WhoIs auth │     │ Tools:         │  │
                           │  │            │     │  athena.*      │  │
  ┌──────────┐             │  │ cobra CLI  │     │  hermes.*      │  │
  │ CLI /    │────────────▶│  │            │     │  apollo.*      │  │
  │ Agent    │             │  └─────┬──────┘     │  ares.*        │  │
  └──────────┘             │        │            └───────┬────────┘  │
                           │        │                    │           │
                           │  ┌─────┴────────────────────┴────────┐ │
                           │  │           Data Layer               │ │
                           │  │  Postgres: bookings, equipment,   │ │
                           │  │            maintenance, audit_log  │ │
                           │  │  Mem0: cross-session staff context │ │
                           │  │  NATS: capacity event subscriber  │ │
                           │  └───────────────────────────────────┘ │
                           └─────────────────────────────────────────┘
```

## Tech Stack

| Component | Technology | Why |
|-----------|-----------|-----|
| Gateway | Go + gorilla/websocket | Real-time bidirectional chat streaming. |
| Agent | LangGraph (Python) | Tool-calling, HITL approval, durable checkpoints. Already on Prometheus. |
| Bridge | gRPC streaming | Typed, bidirectional, low-latency Go↔Python communication. |
| LLM | vLLM → Mistral-7B | Local inference on RTX 3090. Structured output via guided generation. |
| Memory | Mem0 | Staff preferences and context persist across sessions. |
| Database | Postgres 16 (hermes schema) | Bookings, equipment, maintenance, audit trail. |
| Events | NATS subscriber | Listens to ATHENA capacity alerts for proactive notifications. |
| Auth | Tailscale WhoIs | If you're on the mesh, you're authenticated. Zero login friction. |
| CLI | cobra | `hermes book create`, `hermes equip status`, `hermes maint file` |
| Metrics | prometheus/client_golang | Conversation latency, tool call frequency, error rates. |
| CI | GitHub Actions | Build, test, lint, push container. |
| Deploy | Flux | GitOps from homelab-gitops. |

## Project Structure

```
hermes/
├── cmd/
│   └── hermes/
│       └── main.go
├── internal/
│   ├── gateway/               # Go WebSocket + REST server
│   │   ├── server.go
│   │   ├── websocket.go       # WebSocket handler + session management
│   │   ├── handlers.go        # REST endpoints for bookings, equipment
│   │   └── middleware.go      # Tailscale WhoIs auth extraction
│   ├── bridge/                # gRPC client to LangGraph agent
│   │   ├── client.go
│   │   └── stream.go
│   ├── booking/               # Booking domain logic
│   │   ├── service.go
│   │   └── repository.go
│   ├── equipment/             # Equipment + maintenance domain
│   │   ├── service.go
│   │   └── repository.go
│   ├── notify/                # Notification dispatch
│   │   └── notifier.go        # NATS listener → WebSocket push
│   └── config/
│       └── config.go
├── agent/                     # LangGraph agent (Python)
│   ├── graph.py               # Agent graph definition
│   ├── tools.py               # Tool implementations (calls Go API + ATHENA)
│   ├── prompts.py             # System prompts for facility ops context
│   ├── requirements.txt
│   └── Dockerfile             # Separate container for the Python agent
├── proto/
│   └── bridge/
│       └── v1/
│           └── bridge.proto   # gRPC service definition for Go↔Python
├── pkg/
│   └── client/
│       └── client.go          # Go client for other services
├── db/
│   ├── migrations/
│   └── queries/
├── deploy/
├── docs/
│   ├── growing-pains.md
│   ├── adr/
│   ├── conversation-examples.md
│   └── grafana/
├── tools.json                 # MCP tool manifest
├── Dockerfile                 # Go gateway container
├── docker-compose.dev.yml     # Local dev: gateway + agent + deps
├── Makefile
├── .github/workflows/ci.yml
├── go.mod
└── go.sum
```

## How Conversations Work

```
Staff: "Is the gym packed right now?"

HERMES agent internally:
  1. Calls tool: athena.get_current_occupancy(facility="ashtonbee")
     → {"count": 187, "capacity": 200, "utilization": 0.935}
  2. Calls tool: athena.predict_capacity(window_hours=2)
     → [{"hour":"19:00","expected":195}, {"hour":"20:00","expected":168}]
  3. Generates response via vLLM with tool results as context

HERMES: "Currently at 187/200 (93.5%). Expected to peak at 195 around 7 PM,
         then drop to 168 by 8 PM. Want me to book the overflow room?"

Staff: "Yeah book it until 8"

HERMES agent internally:
  1. Calls tool: hermes.booking.create(room="overflow", end="20:00")
  2. HITL gate: awaits staff confirmation (LangGraph approval node)

HERMES: "Confirm: Book overflow room until 8 PM under your name? [Yes/No]"

Staff: "Yes"

HERMES: "Done — overflow booked. I'll ping you when main gym drops below 80%."
  [Subscribes to NATS: athena.capacity.resolved]
```

## CLI Reference

```bash
hermes serve                                    # Start gateway + agent
hermes chat                                     # Interactive CLI chat session
hermes book create  [--room] [--start] [--end]  # Create booking
hermes book list    [--date] [--room]            # List bookings
hermes book cancel  [--id]                       # Cancel booking
hermes equip list   [--location] [--status]      # List equipment
hermes equip status [--id]                       # Equipment details
hermes maint file   [--equipment] [--desc]       # File maintenance ticket
hermes maint list   [--status] [--priority]      # List tickets
hermes maint resolve [--id]                      # Resolve ticket
hermes version
```

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/v1/health` | Health check |
| WS | `/api/v1/chat` | WebSocket chat endpoint |
| GET/POST | `/api/v1/bookings` | CRUD bookings |
| GET | `/api/v1/equipment` | List equipment |
| GET/POST | `/api/v1/maintenance` | CRUD maintenance tickets |
| GET | `/api/v1/audit` | Audit log (staff actions + agent actions) |
| GET | `/metrics` | Prometheus metrics |

## Prometheus Metrics

| Metric | Type | Description |
|--------|------|-------------|
| `hermes_conversations_total` | counter | Total conversations started |
| `hermes_tool_calls_total{tool}` | counter | Tool invocations by tool name |
| `hermes_tool_call_duration_seconds{tool}` | histogram | Tool call latency |
| `hermes_llm_inference_duration_seconds` | histogram | vLLM response time |
| `hermes_hitl_approvals_total{action,outcome}` | counter | HITL confirmations |
| `hermes_active_sessions` | gauge | Active WebSocket sessions |
| `hermes_nats_events_received_total{type}` | counter | NATS events consumed |

## Status

| Milestone | Status |
|-----------|--------|
| Project scaffold | Not started |
| Database schema + migrations | Not started |
| Go WebSocket gateway | Not started |
| Tailscale WhoIs auth middleware | Not started |
| gRPC bridge to LangGraph | Not started |
| LangGraph agent + tool definitions | Not started |
| Booking service | Not started |
| Equipment + maintenance service | Not started |
| NATS capacity event listener | Not started |
| Mem0 integration (staff context) | Not started |
| CLI | Not started |
| Prometheus metrics | Not started |
| Grafana dashboard | Not started |
| MCP tool manifest | Not started |
| CI pipeline | Not started |
| Cluster deployment via Flux | Not started |

## Part of the ASHTON Platform

```
                   ashton-proto
                        │
    ┌───────────────────┼───────────────────┐
    ▼                   ▼                   ▼
  athena ◄────────  [hermes] ────────▶ apollo
    │                   │                   │
    └───────────────────┼───────────────────┘
                        ▼
                ashton-mcp-gateway
```

HERMES depends on ATHENA for occupancy data and on APOLLO/ARES for staff-side insight into member activity when needed.
HERMES is the staff operational interface to the platform, not the student-facing product surface.

## License

MIT
