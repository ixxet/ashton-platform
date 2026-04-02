# athena

ATHENA is the physical-truth service in ASHTON.

It owns presence input, occupancy reads, ingress normalization, and the first
identified-arrival publish path. It does not own member intent, lobby state,
or workouts.

## Current Role

The active ATHENA slice is intentionally narrow:

- mock-backed presence input
- one canonical occupancy read path shared by CLI, HTTP, and Prometheus
- identified-arrival publication through the shared `ashton-proto` helper

Prediction, broader adapters, and persistence exist only as authored future
direction, not as current runtime truth.

## Current Real Slice

| Surface | Status | Notes |
| --- | --- | --- |
| `athena serve` | Real | Starts the HTTP server and can run the publish worker when `ATHENA_NATS_URL` is set |
| `GET /api/v1/health` | Real | Returns service health and adapter name |
| `GET /api/v1/presence/count` | Real | Reads through the canonical occupancy path |
| `GET /metrics` | Real | Exposes `athena_current_occupancy` from the same read path |
| `athena presence count` | Real | CLI read surface using the same service logic |
| `athena presence publish-identified` | Real | One-shot identified-arrival publish through NATS |
| background publish worker | Real | Enabled only when NATS is configured |
| prediction runtime | Deferred | Still design-level only |
| real hardware adapters | Deferred | Mock is the only active adapter |

## Ownership Rules

| ATHENA owns | ATHENA does not own |
| --- | --- |
| physical presence events | member auth |
| occupancy counts and ingress classification | visibility and availability intent |
| identified-arrival publication | lobby eligibility or lobby membership |
| adapter normalization | workouts and recommendations |

Tap-in changes physical truth. It does not create social intent.

## Current Milestone Truth

- CLI, HTTP, and Prometheus read through one shared occupancy path
- invalid adapter and interval config fail fast
- `athena.identified_presence.arrived` is published through the shared contract
- downstream replay safety is intentionally a consumer responsibility beyond
  ATHENA's in-process dedupe set

## Project Shape

| Path | Purpose |
| --- | --- |
| `cmd/athena/` | CLI entrypoint and serve command |
| `internal/adapter/` | active adapter interface and mock implementation |
| `internal/presence/` | canonical occupancy read logic |
| `internal/publish/` | identified-arrival build and publish flow |
| `internal/server/` | health and count HTTP surfaces |
| `internal/metrics/` | Prometheus wiring |
| `db/migrations/` | authored future relational schema |
| `docs/` | roadmap, runbooks, diagrams, ADRs, and growing pains |

## Verification Standard

Treat ATHENA as trustworthy only when:

- full Go tests pass repeatedly
- the binary builds
- the container image builds reproducibly
- the local smoke path covers health, count, and metrics
- the publish path is exercised against real NATS when that boundary is in scope

## Deployment Boundary

ATHENA owns its own runtime and image build. GitOps manifests and live cluster
verification live in `Prometheus`. Cluster claims are not valid until manifests
render cleanly and rollout is verified from a real kube context.

## Deferred On Purpose

- prediction runtime
- Postgres-backed read or write paths
- adapter widening beyond the mock slice
- member-intent logic that belongs in APOLLO
