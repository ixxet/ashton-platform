# athena

ATHENA is the physical-truth service in ASHTON.

It owns presence input, occupancy reads, ingress normalization, and the active
identified visit-lifecycle publish path. It does not own member intent, lobby
state, or workouts.

## Current Role

The active ATHENA slice is intentionally narrow:

- mock-backed presence input plus one source-backed CSV ingress line
- one canonical occupancy read path shared by CLI, HTTP, and Prometheus
- push-based edge tap ingress for identified visit-lifecycle publication
- explicit in-memory edge-driven occupancy projection in `serve`
- bounded live browser-reachable deployment of that same edge path
- identified arrival and departure publication through the shared
  `ashton-proto` helper

Prediction, broader adapters, and persistence exist only as authored future
direction, not as current runtime truth.

## Current Real Slice

| Surface | Status | Notes |
| --- | --- | --- |
| `athena serve` | Real | Starts the HTTP server and can run the publish worker when `ATHENA_NATS_URL` is set |
| `GET /api/v1/health` | Real | Returns service health and adapter name |
| `GET /api/v1/presence/count` | Real | Reads through the canonical occupancy path; in explicit projection mode this is the in-memory edge projection |
| `GET /metrics` | Real | Exposes `athena_current_occupancy` from the same read path |
| `athena presence count` | Real | CLI read surface using the same service logic |
| `athena presence publish-identified` | Real | One-shot identified-arrival publish through NATS |
| `athena presence publish-identified-departures` | Real | One-shot identified-departure publish through NATS |
| background publish worker | Real | Enabled only when NATS is configured |
| CSV ingress adapter | Real | `ATHENA_ADAPTER=csv` plus `ATHENA_CSV_PATH` feeds the canonical occupancy read path from one bounded export shape |
| `POST /api/v1/edge/tap` | Real | Authenticates per-node clients, hashes raw IDs, keeps `fail` local, and can update live occupancy plus identified publish |
| `athena edge replay-touchnet` | Real | Replays raw TouchNet exports through the same edge ingress path used by the live browser bridge |
| prediction runtime | Deferred | Still design-level only |
| additional real ingress adapters | Deferred | CSV is the only source-backed adapter today |

## Ownership Rules

| ATHENA owns | ATHENA does not own |
| --- | --- |
| physical presence events | member auth |
| occupancy counts and ingress classification | visibility and availability intent |
| identified visit-lifecycle publication | lobby eligibility or lobby membership |
| adapter normalization | workouts and recommendations |

Tap-in changes physical truth. It does not create social intent.

## Current Milestone Truth

- CLI, HTTP, and Prometheus read through one shared occupancy path
- invalid adapter and interval config fail fast
- one CSV source-backed adapter now proves ATHENA can ingest physical-truth
  export data without widening the public read surface
- explicit edge-driven occupancy projection is now real in `serve` and shares
  one normalized event stream with identified publish
- `athena.identified_presence.arrived` and
  `athena.identified_presence.departed` are published through the shared
  contract
- bounded deployed truth now includes live browser-reachable HTTPS edge ingress,
  in-memory occupancy updates, and direct NATS publish from that same accepted
  pass-event stream
- bounded deployed truth still includes the earlier live mock-backed
  `ATHENA -> NATS -> APOLLO` departure-close boundary
- downstream replay safety is intentionally a consumer responsibility beyond
  ATHENA's in-process dedupe set
- deployed truth is now real for the bounded `v0.4.1` edge path, not only the
  earlier read deployment line

## Project Shape

| Path | Purpose |
| --- | --- |
| `cmd/athena/` | CLI entrypoint and serve command |
| `internal/adapter/` | active adapter interface plus mock and CSV implementations |
| `internal/presence/` | canonical occupancy read logic |
| `internal/publish/` | identified visit-lifecycle build and publish flow |
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
- adapter widening beyond the first CSV slice
- member-intent logic that belongs in APOLLO

## Next Ladder

| Line | Focus | Hard stop |
| --- | --- | --- |
| `v0.5.0` / `Tracer 16` | durable edge-observation groundwork plus ingress hardening | do not imply append-only persistence or broad ATHENA ingress rollout |
| `v0.6.0` | capacity prediction runtime | do not ship predictive UX before ingress and event history are trusted |
