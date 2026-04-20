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
- bounded live browser-reachable deployment of that same edge path, now running
  the `v0.7.0` storage/analytics line
- Postgres-backed append-only edge observations plus commit markers behind the
  same tap handler when `ATHENA_EDGE_POSTGRES_DSN` is set, with the older file
  path retained only as an explicit fallback
- the current repo/runtime `v0.8.0` line adds normalized fail reasons,
  facility-local identity subjects/links, explicit policy versions, and
  accepted-presence records on top of that same immutable observation line
- restart/reload replay of committed `pass` observations into a fresh
  projection when Postgres or the legacy history path is configured, with
  accepted presence now driving replay truth in the new repo/runtime line
- bounded internal history and analytics reads over durable observations and
  derived session facts
- internal-only owner CLI policy and identity commands over the same Postgres
  substrate
- validated file-backed facility truth for facility catalog, hours, zones,
  closure windows, and bounded metadata through internal HTTP and CLI reads
- identified arrival and departure publication through the shared
  `ashton-proto` helper

Prediction and broader adapters remain future direction, not current runtime
truth. Postgres-backed observation storage, derived sessions, and bounded
internal analytics are now real in both repo/runtime and bounded deployed
truth. The new `v0.8.0` policy-backed accepted-presence line is repo/runtime
truth only until a separate deployment packet proves it live. Broad
operator/report surfaces, booking logic, AI summaries, and prediction remain
deferred.

## Current Real Slice

| Surface | Status | Notes |
| --- | --- | --- |
| `athena serve` | Real | Starts the HTTP server and can run the publish worker when `ATHENA_NATS_URL` is set |
| `GET /api/v1/health` | Real | Returns service health and adapter name |
| `GET /api/v1/presence/count` | Real | Reads through the canonical occupancy path; in explicit projection mode this is the in-memory edge projection |
| `GET /metrics` | Real | Exposes `athena_current_occupancy` from the same read path |
| `athena presence count` | Real | CLI read surface using the same service logic |
| `GET /api/v1/presence/history` | Real, bounded internal support | Reads privacy-safe facility-filtered history from the configured durable store and returns source `result` plus separate accepted-presence fields |
| `GET /api/v1/presence/analytics` | Real, bounded internal support | Reads Postgres-backed observation and session analytics by facility, zone, node, and time window without widening into dashboards |
| `GET /api/v1/facilities` | Real, internal-only, config-gated | Lists facility summaries from `ATHENA_FACILITY_CATALOG_PATH` |
| `GET /api/v1/facilities/{facility_id}` | Real, internal-only, config-gated | Returns one facility's hours, zones, closure windows, and bounded metadata from the same validated catalog |
| `athena facility list` | Real, internal-only | CLI catalog summary read over the same validated facility file |
| `athena facility show --facility <id>` | Real, internal-only | CLI facility detail read without derived scheduling answers |
| `athena presence publish-identified` | Real | One-shot identified-arrival publish through NATS |
| `athena presence publish-identified-departures` | Real | One-shot identified-departure publish through NATS |
| background publish worker | Real | Enabled only when NATS is configured |
| CSV ingress adapter | Real | `ATHENA_ADAPTER=csv` plus `ATHENA_CSV_PATH` feeds the canonical occupancy read path from one bounded export shape |
| `POST /api/v1/edge/tap` | Real | Authenticates per-node clients, hashes raw IDs, keeps `fail` local, and can update live occupancy plus identified publish |
| `athena edge replay-touchnet` | Real | Replays raw TouchNet exports through the same edge ingress path used by the live browser bridge |
| `athena edge history` | Real, internal-only | Reads recent durable observations from Postgres when `--postgres-dsn` is provided; the older file-backed history path remains a legacy fallback |
| `athena edge analytics` | Real, internal-only | Reads bounded Postgres-backed observation, flow, occupancy, and derived-session analytics over a requested window |
| `athena policy ...` | Real, internal-only | Creates, disables, and lists accepted-presence policies through an owner CLI; there is still no HTTP admin surface |
| `athena identity ...` | Real, internal-only | Inspects facility-local subjects and adds privacy-safe links without exposing raw IDs or relying on name auto-merge |
| append-only durable edge storage | Real, primary when Postgres is configured | Writes privacy-safe `pass` and `fail` observations plus commit markers behind the existing tap handler; the older file-backed history path remains fallback only |
| policy-backed accepted presence | Real, primary in repo/runtime when Postgres plus explicit policy config are present | Keeps source `fail` truth immutable while allowing explicit accepted presence for recognized-denied testing windows |
| derived session facts | Real, internal-only | Derives `open`, `closed`, and `unmatched_exit` session truth from accepted `pass` observations without rewriting the raw durable history |
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

## Current Platform Truth

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
- Postgres-backed append-only observation storage is now real behind the same
  tap handler in repo/runtime and on the bounded live `v0.7.0` deploy line
- the current repo/runtime line now also normalizes fail reasons and stores
  explicit accepted-presence, policy-version, and facility-local
  identity-linkage truth without rewriting source observations
- immutable replay identity hardening is now real, so replay commit truth no
  longer relies on bare caller-supplied `event_id`
- fail-open shadow-write is now real, so durable-write failure does not break
  the existing live tap response or projection/publish outcome
- restart/reload replay in the current repo/runtime line now rebuilds occupancy
  from accepted-presence truth, while the bounded deployed line remains the
  earlier committed-pass posture until separately redeployed
- accepted `pass` observations now derive Postgres-backed session facts with
  explicit `open`, `closed`, and `unmatched_exit` states
- bounded internal `GET /api/v1/presence/history`,
  `GET /api/v1/presence/analytics`, `athena edge history`, and
  `athena edge analytics` reads are now real over that durable store
- bounded internal `athena policy ...` and `athena identity ...` commands are
  now real over the same Postgres-backed substrate
- validated facility catalog reads are now real for CLI and internal HTTP when
  `ATHENA_FACILITY_CATALOG_PATH` is set, while missing catalog config still
  fails cleanly instead of inventing defaults
- bounded deployed truth still includes the earlier live mock-backed
  `ATHENA -> NATS -> APOLLO` departure-close boundary
- downstream replay safety is intentionally a consumer responsibility beyond
  ATHENA's in-process dedupe set
- bounded deployed truth now also includes the `v0.7.0` storage/analytics
  line while keeping the external surface narrowed to `/api/v1/edge/tap` and
  `/api/v1/health`

## Project Shape

| Path | Purpose |
| --- | --- |
| `cmd/athena/` | CLI entrypoint and serve command |
| `internal/adapter/` | active adapter interface plus mock and CSV implementations |
| `internal/facility/` | validated file-backed facility truth loading and read access |
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
- broad operator override or identity-reconciliation UI
- accepted-presence session cutover
- public report/export surfaces
- adapter widening beyond the first CSV slice
- member-intent logic that belongs in APOLLO

## Current Closeout Line

- `v0.5.0` / `Tracer 16` is shipped as the durable-history release line
- `v0.5.1` is now shipped as a bounded Tracer 17 support follow-up on that
  same durable-history branch
- `v0.6.0` / `Tracer 18` is shipped as the facility-truth release line
- `v0.6.1` is shipped as the Milestone 2.0 hardening follow-up on that same
  facility-truth branch
- `v0.7.0` is now shipped and live as the bounded Postgres-backed observation,
  session, and analytics line
- `v0.8.0` is now the current repo/runtime line for policy-backed accepted
  presence, but it is not yet part of bounded deployed truth
- what became real: append-only durable edge history, immutable replay
  identity hardening, fail-open shadow-write, restart/reload replay
  groundwork, a CLI-only internal history surface, and one bounded privacy-safe
  facility-history read for HERMES reconciliation
- what also became real for Tracer 18: validated file-backed facility catalog,
  hours, zones, closure windows, and bounded metadata reads through
  `athena facility list`, `athena facility show`, `GET /api/v1/facilities`, and
  `GET /api/v1/facilities/{facility_id}`
- what also became real for `v0.7.0`: Postgres-backed `pass` and `fail`
  observation storage, derived session facts, bounded internal history and
  analytics reads, and the bounded live deploy repin to that storage line over
  the existing edge surface
- what also became real for `v0.8.0` in repo/runtime: normalized fail reasons,
  explicit accepted-presence truth, facility-local identity subjects/links,
  explicit policy versions with actor attribution, and CLI-only policy/identity
  management without changing the live tap contract
- what stayed deferred: prediction, public dashboards or broad
  operator/report surfaces, booking/scheduling runtime, AI occupancy summary,
  identity-reconciliation UX, accepted-presence session cutover, and any
  derived scheduling answers such as `is_open_now`

## Next Ladder

| Line | Focus | Hard stop |
| --- | --- | --- |
| current `v0.8.0` line | policy-backed accepted presence for explicit recognized-denied testing windows | do not rewrite source fail truth, do not widen into HTTP admin, session cutover, public reports, or prediction |
| later than `v0.8.0` | accepted-presence session cutover, broader diagnostics, and capacity prediction runtime | do not ship predictive UX before ingress, durable history, accepted presence, derived sessions, and facility truth are trusted |
