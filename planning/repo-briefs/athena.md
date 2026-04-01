# athena

Real-time facility presence tracking, occupancy monitoring, and predictive analytics for the Ashtonbee Athletics facility. The first Go service in the ASHTON platform.

ATHENA integrates with physical or mocked ingress sources through a pluggable adapter pattern, tracks presence and occupancy in real time, predicts peak hours using weighted historical analysis, and surfaces every data point through Prometheus metrics, a CLI, an HTTP API, and MCP tool definitions for AI agent consumption.

## Architecture

```
                    ┌─────────────────────────────────────┐
                    │            ATHENA                     │
  ┌──────────┐     │                                       │
  │ RFID /   │────▶│  Adapter ──▶ Service ──▶ Repository  │
  │ ToF /    │     │     │           │            │        │
  │ Mock     │     │     │      ┌────┴────┐   ┌──┴──┐     │
  └──────────┘     │     │      │Presence │   │Postgres│   │
                    │     │      │ +       │   │(sqlc) │    │
  ┌──────────┐     │     │      │Capacity │   └───────┘    │
  │ APOLLO / │────▶│  HTTP API  │Predictor│                 │
  │ HERMES   │     │     │      └─────────┘                 │
  │ Clients  │     │     │                                  │
  └──────────┘     │     │                                  │
                    │  ┌──┴────────────┐  ┌──────────────┐ │
  ┌──────────┐     │  │ Prometheus    │  │ NATS         │ │
  │ CLI /    │────▶│  │ /metrics      │  │ (event pub)  │ │
  │ Agent    │     │  └───────────────┘  └──────────────┘ │
  └──────────┘     └─────────────────────────────────────┘
```

## Tech Stack

| Component | Technology | Why |
|-----------|-----------|-----|
| Language | Go 1.23+ | Tailscale's language. Infrastructure lingua franca. |
| HTTP | chi | Minimal, idiomatic, net/http compatible. |
| CLI | cobra + viper | Industry standard (kubectl, docker, gh). |
| SQL | sqlc | Type-safe Go from handwritten SQL. No ORM. |
| Database | Postgres 16 | JSONB, materialized views, advisory locks. |
| Migrations | golang-migrate | File-based, version-controlled. |
| Events | NATS | Lightweight pub/sub for presence and occupancy events. |
| Metrics | prometheus/client_golang | Native instrumentation. |
| Redis | Planned, deferred | Fast occupancy counters and short-lived hot-state caches when needed. |
| Logging | slog (stdlib) | Structured JSON, ships to Loki. |
| Auth | Tailscale WhoIs + sessions | Staff and operator access to admin surfaces. |
| Testing | stdlib + testcontainers-go | Real Postgres in CI. |
| Container | distroless/static | Minimal. Single Go binary + nothing. |
| CI | GitHub Actions | Build, test, lint, push to GHCR. |
| Deploy | Flux (Kustomization) | GitOps from homelab-gitops repo. |

## Project Structure

```
athena/
├── cmd/
│   └── athena/
│       └── main.go                # Entrypoint: cobra root command
├── internal/
│   ├── adapter/                   # Physical ingress adapters
│   │   ├── adapter.go             # IngressAdapter interface
│   │   ├── mock.go                # MockAdapter (synthetic events)
│   │   ├── database.go            # DatabaseAdapter (read-only, proprietary DB)
│   │   ├── csv.go                 # CSVAdapter (file import)
│   │   └── hardware.go            # Shared hardware plumbing before concrete adapters split
│   ├── presence/                  # Presence and ingress domain logic
│   │   ├── service.go
│   │   ├── service_test.go
│   │   └── repository.go          # sqlc-generated interface
│   ├── capacity/                  # Capacity prediction engine
│   │   ├── predictor.go           # EWMA + historical binning
│   │   ├── predictor_test.go
│   │   └── model.go               # Prediction types
│   ├── server/                    # HTTP server setup
│   │   ├── server.go              # chi router, middleware, routes
│   │   ├── handlers.go            # HTTP handlers
│   │   └── middleware.go          # Auth, logging, metrics middleware
│   └── config/                    # Configuration (viper)
│       └── config.go
├── pkg/
│   └── client/                    # Go client for other services to import
│       └── client.go              # athena.NewClient(baseURL) → typed API calls
├── db/
│   ├── migrations/                # SQL migration files
│   │   ├── 001_initial.up.sql
│   │   └── 001_initial.down.sql
│   ├── queries/                   # sqlc query files
│   │   ├── presence.sql
│   │   └── capacity.sql
│   └── sqlc.yaml                  # sqlc configuration
├── deploy/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   ├── service.yaml
│   └── servicemonitor.yaml        # Prometheus auto-discovery
├── docs/
│   ├── growing-pains.md
│   ├── adr/
│   │   └── 001-adapter-pattern.md
│   └── grafana/
│       └── facility-overview.json  # Grafana dashboard export
├── tools.json                      # MCP tool manifest
├── Dockerfile                      # Multi-stage: build → distroless
├── Makefile                        # build, test, lint, migrate, generate
├── .github/
│   └── workflows/
│       └── ci.yml                  # Build, test, lint, push container
├── go.mod
├── go.sum
└── .golangci.yml                   # Linter configuration
```

## Quick Start

### Local Development (MockAdapter)

```bash
# Prerequisites: Go 1.23+, Postgres running, NATS running (optional)

# Clone and build
git clone https://github.com/ixxet/athena.git
cd athena
make build

# Run database migrations
export DATABASE_URL="postgres://localhost:5432/athena?sslmode=disable"
make migrate-up

# Start with mock adapter
export ATHENA_ADAPTER=mock
export ATHENA_FACILITY_CAPACITY=200
./bin/athena serve

# In another terminal
./bin/athena capacity current
./bin/athena presence list --since 1h
```

### On the Cluster (Flux)

ATHENA deploys via Flux from the `homelab-gitops` repo. The deployment manifests
in `deploy/` are referenced by a Flux Kustomization.

## CLI Reference

```bash
athena serve                                    # Start HTTP server
athena presence list   [--since] [--format]      # List recent presence events
athena presence count  [--facility] [--format]   # Current present count
athena capacity current [--facility]             # Real-time occupancy
athena capacity predict [--facility] [--window]  # Predicted occupancy
athena capacity history [--facility] [--range]   # Historical occupancy
athena adapter status                            # Adapter health check
athena migrate up                                # Run database migrations
athena version                                   # Build version info
```

All commands support `--format json` for structured output (AI agent consumption).

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/v1/health` | Health check with adapter status |
| GET | `/api/v1/presence` | List presence events (paginated) |
| GET | `/api/v1/presence/count` | Current present count by facility |
| GET | `/api/v1/capacity/current` | Real-time occupancy snapshot |
| GET | `/api/v1/capacity/predict` | Predicted occupancy for next N hours |
| GET | `/api/v1/capacity/history` | Historical occupancy data |
| GET | `/metrics` | Prometheus metrics endpoint |

## Prometheus Metrics

| Metric | Type | Description |
|--------|------|-------------|
| `athena_presence_events_total` | counter | Total presence events processed |
| `athena_current_occupancy` | gauge | Real-time occupancy count |
| `athena_capacity_utilization_ratio` | gauge | Current / max capacity |
| `athena_prediction_accuracy_ratio` | gauge | Predicted vs actual (rolling) |
| `athena_http_request_duration_seconds` | histogram | Request latency by route |
| `athena_tap_adapter_poll_duration_seconds` | histogram | Adapter polling latency |
| `athena_tap_adapter_errors_total` | counter | Adapter error count |

## Presence And Occupancy Rules

ATHENA is the physical truth layer. It publishes presence and occupancy. It does not decide whether a member wants to be visible, recruitable, or in a lobby. That intent lives in APOLLO.

- Presence events represent arrival, departure, or location changes
- Occupancy aggregates represent how full a facility or zone is
- Identity may be attached when RFID or another identified source exists
- Matchmaking intent is explicitly out of scope for ATHENA

## Capacity Prediction

ATHENA will keep the original EWMA-plus-historical-binning prediction approach, but the first implementation wave defers the predictor until the presence read path is real. The design is preserved in an ADR so the algorithm does not disappear while the tracer bullet stays small.

## Ingress Adapter Pattern

ATHENA never hard-codes a specific ingress path. A pluggable adapter
interface abstracts the data source. Swap adapters via the `ATHENA_ADAPTER`
environment variable — zero code changes to the core service.

| Adapter | Env Value | Use Case |
|---------|-----------|----------|
| MockAdapter | `mock` | Development, demos, CI |
| DatabaseAdapter | `database` | Read-only access to proprietary DB |
| CSVAdapter | `csv` | Import from exported spreadsheets |
| ToFAdapter | `tof` | Anonymous occupancy via people counting |
| RFIDAdapter | `rfid` | Identified presence via tag scans |
| HardwareAdapter | `hardware` | Legacy catch-all until concrete adapters split |

## Testing

```bash
make test          # Unit tests
make test-int      # Integration tests (requires Docker for testcontainers)
make lint          # golangci-lint
make coverage      # Coverage report
```

## Status

| Milestone | Status |
|-----------|--------|
| Project scaffold | Done |
| Database schema + migrations | Not started |
| MockAdapter | Done |
| Presence service + repository | Not started |
| Capacity predictor | Not started |
| HTTP API | Initial slice done |
| CLI | Initial slice done |
| Prometheus metrics | Initial slice done |
| NATS event publishing | Not started |
| Grafana dashboard | Not started |
| MCP tool manifest | Not started |
| CI pipeline | Initial image workflow done |
| Cluster deployment via Flux | Initial slice wired |
| Real tap-in adapter | Not started |
| PWA frontend | Not started |

## Part of the ASHTON Platform

```
                   ashton-proto (shared schemas)
                        │
    ┌───────────────────┼───────────────────┐
    ▼                   ▼                   ▼
 [athena] ◄──────── hermes ────────▶ apollo
    │                   │                   │
    └───────────────────┼───────────────────┘
                        ▼
                ashton-mcp-gateway
```

ATHENA is the first service built. HERMES depends on it for occupancy and operational truth. APOLLO depends on it for presence and facility context.

## License

MIT
