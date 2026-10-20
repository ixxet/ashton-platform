# Booking/Scheduling Closed-Test Readiness

## R1 status and ownership

Intake recorded 2026-10-20. R1 continues the bridge/readiness work; completed
3B packet IDs and dated milestone evidence remain historical. G0 audit, G1
source repairs and G2 real local proof are executed. G3 found concrete external
booking blockers; deployment readiness is not closed. G4 source/docs publication
and exact CI outcomes are recorded below. No live changes are authorised.

Delivery order is booking/scheduling through Hestia and Themis, competition /
rating / game identity, then member services. Broader ATHENA physical truth is
later. Booking does not gain a presence dependency. Daily credits, teams,
reliability scoring, instant booking, payments, public tournaments and OpenSkill
cutover remain outside R1.

Platform owns sequencing and cross-repository readiness. Repository docs own
implemented contracts. APOLLO's [launch expansion audit](https://github.com/ixxet/apollo/blob/main/docs/launch-expansion-audit.md)
still governs competition expansion. The controller owns every edited file;
Contract, Surface/Ops and Verifier guards perform independent read-only audits.
No additional worker chats are created.

## G0 current source and overlap audit

All six remote main SHAs were checked with `git ls-remote origin refs/heads/main`
on 2026-10-20, separately from local origin refs. All match local HEAD.
Hestia/Themis GitHub API checks now succeed; their previous 404 verification gaps
are resolved. GitHub all-state issue lists (limit 50) and PR lists (limit 30)
returned empty for all five ASHTON repositories. Recent commit lists, existing
Markdown, bridge preflight, route/API matrix, flows and deferments were searched.
No equivalent canonical booking-readiness artifact exists. Existing bridge and
frontend evidence is reused below, rather than creating a parallel tracker.

### ASHTON/apollo

Branch `main`; HEAD `165369f30b6ebc39c0938c319382d10503d60652`.

Initial dirty files (unrelated, preserved):

```text
M README.md
 M docs/README.md
 M docs/glossary.md
 M docs/growing-pains.md
 M docs/hardening/README.md
 M docs/runbooks/member-state.md
?? gotest.json
?? junit.xml
```

Recent ten commits:

```text
165369f docs: document APOLLO ATHENA bridge readiness
9e9340d test: cover ATHENA bridge runtime errors
7cd1778 fix: harden ATHENA bridge client handling
43424c1 docs: document operational presence gate
c37f8e1 test: cover operational ATHENA presence gate
1a17633 feat: consume ATHENA ingress bridge
82188d0 docs: record ATHENA presence gate proof
ad0171e feat: add ATHENA presence gate proof
4f0bff1 docs: record live destructive probe planning truth
1eec51d docs: record ATHENA ingress bridge truth
```

### ASHTON/athena

Branch `main`; HEAD `99ca72e961b3502bdde91362eb98db7c0b829881`.

Initial dirty files (unrelated, preserved):

```text
?? gotest.json
?? junit.xml
```

Recent ten commits:

```text
99ca72e docs: document ingress bridge deploy readiness
a8fce6c test: cover ingress bridge deploy-readiness bounds
ec0d47a fix: harden internal ingress bridge read
d6b3386 docs: document operational ingress bridge
9e5f6df test: cover internal ingress bridge read
205027d feat: expose internal ingress bridge read
2477427 docs: record live destructive probe planning truth
d10eed0 docs: document real ingress bridge proof
b58fa9b test: cover real ingress bridge proof
6058042 feat: add real ingress bridge proof
```

### ASHTON/ashton-platform

Branch `main`; HEAD `13f751b3dc32d49dfd59294db6b1d2eeaf890f7f`.

Initial dirty files (unrelated, preserved):

```text
M README.md
 M planning/audits/2026-04-29-competition-system-audit.md
 M planning/milestones/milestone-3.0-packet.md
 M planning/repo-briefs/apollo.md
 M planning/repo-briefs/ashton-mcp-gateway.md
 M planning/repo-briefs/ashton-proto.md
 M planning/repo-briefs/athena.md
 M planning/runbooks/phase-2-launch.md
 M planning/sprints/TRACER-MATRIX.md
```

Recent ten commits:

```text
13f751b docs: add closed-test bridge deployment preflight
4f11b3b docs: record operational bridge deploy readiness
b201ba0 docs: record operational presence trust gate
e2a20ca docs: add live destructive probe readiness checklist
1ec5812 docs: record co-presence private presence gate truth
e156e71 docs: add live destructive probe plan
1527682 docs: record ATHENA real ingress bridge truth
34e348b docs: record game identity policy tuning truth
d5941bc docs: add frontend route api contract matrix
23daefc docs: record rating policy simulation truth
```

### ASHTON/hestia

Branch `main`; HEAD `66010ae8bd453fccdaffbe4e4eb27cb7b4d39535`.

Initial dirty files (unrelated, preserved):

```text
clean
```

Recent ten commits:

```text
66010ae docs: close Hestia 3B trust spine
4672fc2 docs: harden 3B.20 truth
88f9bd4 docs: sync Hestia 3B.20 truth
71a6117 test: cover Hestia game identity boundary
7deb131 feat: add game identity surface
3c190bb test: cover Hestia 3B.19 public competition boundary
7f8ed9f fix: harden Hestia 3B.19 public competition cohesion
1ab7803 docs: sync Hestia 3B.19 truth
cb30f42 test: cover Hestia public competition boundary
cd87a6c feat: add public competition readiness surface
```

### ASHTON/themis

Branch `main`; HEAD `0231e02dd651676667b867f3112ef2179a5f26cb`.

Initial dirty files (unrelated, preserved):

```text
clean
```

Recent ten commits:

```text
0231e02 test: cover Themis 3B.18 safety reliability boundary
2a2e20f fix: harden Themis 3B.18 safety reliability cohesion
b44d626 docs: sync Themis 3B.18 truth
93a5d45 test: cover Themis safety reliability boundary
98a1e48 feat: add manager safety reliability ops surface
0bc9503 docs: sync Themis 3B.12 truth
7af9b6e test: cover Themis lifecycle result boundaries
1d774e1 feat: add competition lifecycle result ops states
7cd3097 test: cover Themis 3B.11 ops boundary
74561a9 fix: harden Themis 3B.11 ops cohesion
```

### Computers/Prometheus

Branch `main`; HEAD `80203883c4fbbe4b88889d8ee8c20e048c83a7e1`.

Initial dirty files (unrelated, preserved):

```text
M docs/runbooks/model-upgrade.md
 M docs/runbooks/network-relocation.md
 M homelab-gitops/apps/agents/langgraph/langgraph-configmap.yaml
 M homelab-gitops/apps/ai/vllm/vllm-deployment.yaml
 M homelab-gitops/apps/summarizer/summarizer-deployment.yaml
 M scripts/verify-after-network-move.sh
 M services/langgraph/app/config.py
```

Recent ten commits:

```text
8020388 chore: roll APOLLO deployment to milestone 3.0 image
86bd13c feat: add competition telemetry scrape and alert example
02033bd docs: record ATHENA named tunnel ingress
2ec2f61 deploy: move ATHENA edge tunnel to tap.lintellabs.net
ad9e83f deploy: stage ATHENA v0.8.2 hardening
2b1d4d0 deploy: repin ATHENA to v0.8.1
49d63fc deploy: stage ATHENA v0.8.0 policy rollout
0105bad observability: add ATHENA edge truth dashboard
e97a449 athena: add Ashtonbee second edge node token
93b1d71 docs: align athena deploy truth in Prometheus README
```

## Confirmed findings and acceptance baseline

| Finding | Current source / owner | Reproduction and desired behaviour | Acceptance evidence planned |
| --- | --- | --- | --- |
| Redirect token forwarding | APOLLO `internal/athena/client.go` | Internal-read client follows redirects by default. Reject every redirect without a follow-up; keep occupancy/analytics behaviour unchanged. | Same-origin/cross-origin 301/302/307/308 target hit counts and token assertions. |
| Incomplete resource bounds | ATHENA `internal/edgehistory/ingress_bridge.go`, shared analytics-session reader; APOLLO client and CLI | Observation/history/session materialisation and response/file reads lack complete bounds. Reject overflow; no eligible silently truncated report. | Exact limit / plus one, query plans, timeout/cancellation and CLI checks. |
| Missing pre-window replay | ATHENA bridge | New empty projector loses pre-window arrivals and other-node state. Replay retained history in storage order across nodes, preserving facility/zone identity scope; bounded unknown blocks eligibility. | Pre-window arrival, in-window exit/repeated arrival, stale/late/same-time and cross-node cases with disposable PostgreSQL. |
| Insufficient envelope validation | APOLLO client / presence gate / CLI | `{}` is accepted; missing safety fields and contradictory scope/identity/eligibility need rejection. | Versioned completeness envelope, strict required fields, valid empty report, additive compatibility, malformed/scope/oversize and file/runtime parity. |
| Themis load-error permission claim | Themis `src/routes/ops/bookings/new/+page.server.ts` | Non-401 load failure sets `canManage:false` and renders read-only state. | Record source and current test result; runtime fix belongs to next booking packet. |
| Malformed public success handling | Hestia `src/lib/server/public-intake.ts` and intake routes | Malformed 202 receipt / 200 status can throw through route handlers. | Reproduce and record; runtime fix belongs to next booking packet. |

Implemented bridge budgets: 1,000 report observations, 4,000 historical/context
observations, requested sessions 1..250 (default 50), 2 MiB encoded response or
file input, 24-hour observation window and 5-second producer operation deadline.
The client uses its configured shorter timeout, capped at 5 seconds for this
bridge read. A database LIMIT bounds result materialisation only; fixture
EXPLAIN and cancellation must separately establish scan/time behaviour.

Internal contract metadata identifies `athena_ingress_bridge_v2`, report-local
identity scope, storage replay order and explicit observations/sessions/context
completeness. Existing prose contracts remain. Legacy unversioned file reports
must be regenerated; harmless additive fields remain accepted. Raw identity
values are never emitted. Report-local references cannot support durable member
credit. Source-pass sessions remain separate from policy-accepted presence.

## Verification and publication plan

Run the packet's exact Go focused/full/race/vet/build commands and both frontend
check/unit/build/e2e suites. Keep raw failure logs outside repositories and
report skipped/unexecuted proof honestly. Use real ATHENA HTTP handler + durable
store/service + APOLLO actual client/classifier/CLI for cross-service proof, not
two mocks. Record query plans, fixtures and no-product-write checks.

Before pushing, inspect service image publication and Flux reconciliation.
Publish only scoped source and Markdown commits through the existing main
workflow if that cannot roll out live. No frontend runtime, GitOps, secret,
migration-on-live, pod or deployed-data mutations are authorised.

## Outstanding readiness decisions

Approved deployment target, external Hestia URL, authenticated Themis access,
server-only trusted-token secret references, APOLLO auth/cookies/routing,
staff provisioning, resource/hour/closure inventory, canary fixtures and rollback
owner/window remain unapproved. Current access and packaging evidence will be
recorded during G3. Booking rollout and optional bridge rollout are separate.
Live deployment requires fresh approval of target, exact images/diff, secrets,
migrations, fixture mutations, window, rollback and abort criteria.

## G1-G2 implemented bridge repairs and local proof

| Repaired defect / decision | Runtime source and regression owner | Local implementation commit |
| --- | --- | --- |
| Bounded snapshot/history/session materialisation; historical cross-node replay; explicit unknown after ambiguous uncommitted state; as-of-window session/node filtering | ATHENA `internal/edgehistory/ingress_bridge.go`, `internal/edgehistory/ingress_bridge_test.go` | [`43bcfb6`](https://github.com/ixxet/athena/commit/43bcfb6958a68945d789558d57129eabd8501aa6) |
| HTTP/CLI time/window/session/byte budgets; no eligible truncation; redacted internal failures; auth/no-store preserved | ATHENA `internal/server/{server.go,server_test.go}`, `cmd/athena/{main.go,main_test.go}` | `43bcfb6` |
| Reject all bridge redirects; cap response read/time; require requested facility/window/zone/node and session scope; preserve analytics/occupancy behaviour | APOLLO `internal/athena/{client.go,client_test.go}` | [`7fab80e`](https://github.com/ixxet/apollo/commit/7fab80e0373417a8832cde9c7e425bac213448c3) |
| Shared required v2 decoder; explicit completeness; supported semantics; evidence/session identity/time/eligibility/summary checks; safe report-local identity allowlist | APOLLO `internal/presence/{athena_ingress_gate.go,athena_ingress_gate_test.go}` | `7fab80e` |
| Bounded regular-file input; reject FIFO/device/directory without blocking; scope checks; real HTTP service/client/classifier/CLI proof | APOLLO `cmd/apollo/{main.go,main_test.go}` | `7fab80e` |
| Explicit completeness line in text mode, including zero-evidence incomplete reports | APOLLO `cmd/apollo/{main.go,main_test.go}` | [`c64b349`](https://github.com/ixxet/apollo/commit/c64b34928f090f48f7ff5ead73d5fbceaf29eb33) |

Source-pass and accepted-policy presence remain separate. Reconstructing all
retained applicable context is conservative: above 4,000 context records, or
when an uncommitted pass would change presence, `replay_context=false` and all
evidence/session signals are blocked. This can reduce eligibility for busy or
ambiguous histories; it does not guess absence or use today's latest marker.
A bounded historical checkpoint/index design is later work if actual use earns
it, not a hidden migration or widened runtime in R1.

The read-only repeatable-read snapshot, cancellation and cap-plus-one queries
bound materialisation. Representative EXPLAIN fixtures are explicitly not full
production-query or production-population load proof: 4,002 history rows were
scanned/sorted to return 4,001, with approximately 378 KiB sort memory and 2 ms
fixture execution. LIMIT is not a physical scan limit. Each PostgreSQL statement
has a four-second timeout inside a five-second operation context; blocked-query
cancellation is regression-tested. Existing shared analytics queries are unchanged.

The wire contract is `athena_ingress_bridge_v2`, with
`identity_scope: report_local`,
`replay_order: stored_at,observed_at,event_id,observation_id`, and required
`completeness: {observations,sessions,replay_context,reason_codes}`. Both
producer and consumer exercise it. Legacy files are rejected with regeneration
guidance, not silently upgraded. Required fields/null/wrong types, `{}`, scope
mismatches, future session closure, contradictory eligibility, unsafe references,
oversize bytes, malformed trailing input and incomplete context are covered;
additive fields and complete empty reports are accepted. Same/cross-origin
301/302/303/307/308 tests assert zero redirect follow-ups. JSON/text modes remain.

Reproducible real-service proof from the APOLLO root, using installed Docker:

```sh
APOLLO_ATHENA_REHEARSAL=1 \
ATHENA_SOURCE_DIR=/Users/zizo/Personal-Projects/ASHTON/athena \
go test -count=1 -v ./cmd/apollo -run TestPresenceAthenaGateRealServicePostgres
```

`TestPresenceAthenaGateRealServicePostgres` builds actual sibling ATHENA into a
temporary directory, applies both existing migration sets in disposable
PostgreSQL 16, starts disposable NATS, creates a synthetic facility admission
policy and sends actual HTTP taps. APOLLO's actual client, shared classifier
and CLI consume four nonempty node-scoped observations. Results: two eligible
co-presence signals, one daily-readiness signal, no source-pass session for a
policy failure, blocked repeated arrival, file/runtime JSON equality and matching
text counts. Actual service reads also verify valid empty reports and 401/403.
The APOLLO product count vector stays `0,0,0,0,0,0` for visits, tap-links,
streaks, streak events, booking requests and schedule blocks. Runtime logs from
the child service are discarded; output is checked for raw identity/token leaks.

The fixture policy, taps, databases, containers and child binary/process are
local disposable artifacts, cleaned by the harness. No test targets a live DB.
The opted-in command requires the sibling checkout and fails on missing source;
single-repo CI explicitly skips this rehearsal and proves its own suite only.

Direct booking/schedule proof, with no ATHENA/Ops dependency supplied to the
actual APOLLO HTTP/PostgreSQL fixture:

```sh
go test -count=1 ./internal/server -run 'Test(PublicBooking|BookingRequest|Schedule)'
```

This passed and exercises existing real booking request/approval/reservation /
status/cancellation and schedule handlers. Frontend mocks remain separate proof.

## G3 booking journey and authority

| Step | Actual endpoint / frontend | Authority and meaning |
| --- | --- | --- |
| Options | Hestia `/intake`; `GET /api/v1/public/booking/options` | Public-listed option IDs/labels; APOLLO owns inventory. |
| Availability | `GET /api/v1/public/booking/options/{optionID}/availability?from=&until=` | Explicit RFC3339 bounds; requestable/closed/booked/unavailable hints, never a reservation. |
| Request | `POST /api/v1/public/booking/requests` | `Idempotency-Key` and `X-Apollo-Public-Intake-Channel: public_web`; 202 receipt creates `requested`, no schedule reservation. |
| Status | Hestia `/intake/status`; `GET /api/v1/public/booking/requests/status?receipt_code=` | Opaque receipt, customer status/message/window/update time. Submission is not confirmation. |
| Staff workspace | Themis `/ops/bookings`; `GET/POST /api/v1/booking/requests`, `GET .../{id}` | Session plus `booking_read` / `booking_manage`. Staff create uses load-born, form-carried idempotency. |
| Decisions | `POST .../{id}/review`, `/needs-changes`, `/reject`, `/approve`, `/cancel` | Manager/owner, server-only trusted proof and `expected_version`. Approval atomically creates linked `reservation` / `hard_reserve` / `internal` schedule truth. |
| Public message and replacement | `POST .../{id}/public-message`, `/edit`, `/rebook` | Versioned changes, separate public-safe message; replacement additionally idempotent. Internal notes remain private. |
| Cancellation | Approved request `/cancel` | Atomically cancels the linked reservation, retains linkage/audit and refuses stale versions or reservation drift. Do not cancel linked reservations through generic schedule controls. |
| Schedule | Themis `/ops/facilities/{facility}/schedule`; `GET/POST /api/v1/schedule/blocks`, `POST .../{id}/cancel` | Supervisor read, manager/owner trusted writes; APOLLO validates resources, relationships, conflicts and versions. |

Source: APOLLO `internal/booking/service.go`, `internal/schedule/service.go`,
`internal/authz/{authz,trusted_surface}.go`, `internal/server/server.go` and
`internal/server/booking_request_integration_test.go`; Hestia
`src/lib/server/public-intake.ts`; Themis `src/routes/ops/bookings` and
`src/lib/server/{client,apollo}.ts`. `booking_read`/`schedule_read` admit
supervisor, manager and owner; manage capabilities admit manager and owner.
Members are denied. Themis injects trusted proof only on the server. Both
browser proxies strip caller-supplied trusted headers; public APOLLO paths are
not broad browser passthroughs.

Booking service dependencies are repository + schedule, not ATHENA. APOLLO
constructs booking/schedule before the optional ATHENA-backed ops overview.
Direct Requests/Schedule navigation exists when facility overview fails; no
occupancy value is invented. Themis's default schedule link still hardcodes
`ashtonbee`; a different facility needs an explicit next-packet decision.

Hestia converts browser-local selections to RFC3339 with explicit offsets.
APOLLO uses IANA zones and calendar arithmetic for recurrence. Existing offset /
Toronto tests do not establish DST gap/fold and 23/25-hour-day acceptance;
that focused proof is required in the next packet. Sequential resource conflicts
are covered. Simultaneous overlapping approvals for distinct request IDs are an
unverified concurrency risk: request-row locks are separate and no resource
exclusion constraint was found. Reproduce before deciding a backend fix.

## G3 reproduced frontend blockers

The existing [route/API matrix](FRONTEND-ROUTE-API-CONTRACT-MATRIX.md) already
tracks both gaps; this record updates their evidence and priority.

- Themis `src/routes/ops/bookings/new/+page.server.ts` catches every non-401
  list-load failure as `canManage:false`; the page then asserts read-only
  permission. Current-function connection-failure reproduction returns
  `{"canManage":false,"hasSafetyKey":true,"hasErrorState":false}`. The next
  packet must separate verified read-only authority from retryable unavailable,
  malformed and load-error states, retaining its idempotency key.
- Hestia `src/lib/server/public-intake.ts` throws on malformed successful
  receipt/status payloads and network failures, which intake/status routes do
  not catch. Current-function 202 `{}` rejects `status must be a string`; 200
  `{}` rejects `receipt_code must be a string`; network failure rejects
  `fetch failed`. Convert these to neutral retryable results, preserving form
  values and the original submission key after ambiguous submission failure.
- Themis new-request defaults still use April 20, 2026. Replace stale defaults;
  do not infer real opening hours or resource availability from fixtures.

These reproductions transpiled actual TypeScript in memory using the existing
`typescript` package and injected mocked upstreams. They are function evidence,
not HTTP, browser, real APOLLO or deployed frontend proof. No frontend runtime,
configuration, dependency or lockfile was changed by R1.

## G3 current deployment inventory

Read-only capture: 2026-10-20, approximately 14:33-14:34 UTC. This supersedes
access failures from the May preflight, not its dated milestone evidence.
The target remains a candidate until explicitly approved.

| Check | Current verified result |
| --- | --- |
| Candidate | `admin@talos-tower`, node `talos-p0d-y77` Ready, namespaces `agents` / `athena` |
| Mac saved API | `https://10.0.0.209:6443` `/readyz` timed out |
| MIMIR saved API | `/home/boi/.config/prometheus/kubeconfig` points at `192.168.2.46`; timed out |
| Rediscovered management | Existing authenticated MIMIR SSH at `100.109.171.72`; Ethernet `172.31.250.1` reaches Talos `172.31.250.162`; existing Kubernetes credentials and per-command server override return `/readyz=ok`, without TLS bypass/config edits |
| APOLLO | Ready 1/1, tag `sha-6b27618`, actual digest `sha256:6c2d4ef8c67d0ea21d3b41963eb9709d0114b53bfea56c30d456cafbb677515a` |
| ATHENA | Ready 1/1, tag `v0.8.2`, actual digest `sha256:6524994c2c30bfd227b684b9144b95a798e3e46e600ac5da46df2a600dfd271d` |
| Health / metrics | APOLLO `ok`, consumer enabled, 34 metric samples; ATHENA `ok`, edge-projection, one occupancy sample; Prometheus `up=1` for both; 15s ServiceMonitors |
| Recent logs | Limited to 30 lines / 30 minutes; APOLLO zero, ATHENA four; identity/message fields suppressed |
| External edge | Valid TLS; `https://tap.lintellabs.net/api/v1/health` 200; bridge path 404 |
| Ingress allowlist | No Kubernetes Ingress, no HTTPRoute resource; nginx permits exact tap/health only, fallback 404; tunnel routes only to edge proxy |
| Frontends | No Hestia or Themis Deployment found; no tracked frontend Dockerfile or image/deploy workflow; adapter-node builds exist |
| Migration ledger | 32 applied, latest `032_competition_safety_reliability_delete_guard.up.sql`; `033` not applied |
| Booking inventory | Zero active/bookable resources; no schedule blocks; public options `[]`; zero booking requests; no verified supervisor/manager/owner accounts returned |
| Wiring | APOLLO trusted-surface and ATHENA settings absent; ATHENA internal-read token absent. Secret values were never retrieved. |
| Restore evidence | Bound local-path PostgreSQL PVC; no agents CronJobs or VolumeSnapshot resource. Independent backup and successful restore remain unverified; this does not prove no external backup exists. |

Database inventory used bounded SELECTs with `default_transaction_read_only=on`
and `statement_timeout=5000`, no identity output or unrestricted dumps. No live
mutations, secret reads/changes, network changes or restarts were performed.

The working authenticated command, retained as dated evidence, is:

```sh
ssh -i /Users/zizo/.ssh/mimir_ed25519 \
  -o BatchMode=yes -o StrictHostKeyChecking=yes -o ConnectTimeout=8 \
  boi@100.109.171.72 \
  'kubectl --kubeconfig /home/boi/.config/prometheus/kubeconfig \
    --server=https://172.31.250.162:6443 --request-timeout=10s \
    get --raw=/readyz'
```

Use that prefix for authenticated GETs of:

```text
/api/v1/namespaces/agents/services/apollo:80/proxy/api/v1/health
/api/v1/namespaces/athena/services/athena:80/proxy/api/v1/health
/api/v1/namespaces/agents/services/apollo:80/proxy/metrics
/api/v1/namespaces/athena/services/athena:80/proxy/metrics
```

Addresses must be rediscovered if these reads fail. Do not change saved
kubeconfig or bypass certificate checks to make inventory pass.

## Deployment wiring for review, not applied

| Decision / setting | Booking rollout requirement | Optional bridge rollout |
| --- | --- | --- |
| Target / URLs | Approve environment, external TLS Hestia hostname and authenticated Themis hostname/access; no URL exists in this evidence | Same approved target, private service access only |
| Node frontend packaging | Add bounded adapter-node container/build publication for Hestia and Themis in the next packet; record exact image digests | No frontend dependency |
| `APOLLO_ORIGIN` | Explicit server-side internal APOLLO origin, e.g. `http://apollo.agents.svc.cluster.local`; both frontend defaults use 8080 while APOLLO runtime defaults 8081, so defaults are not deployment wiring | Unchanged |
| Node `ORIGIN` / proxy | Exact external HTTPS origin for each frontend; approve forwarded host/protocol and ingress/TLS handling; verify form-origin/CSRF behaviour | Bridge remains private |
| Themis trusted proof | `THEMIS_APOLLO_TRUSTED_SURFACE` and `THEMIS_APOLLO_TRUSTED_SURFACE_TOKEN` from an approved server-only secret reference; matching `APOLLO_TRUSTED_SURFACES` mapping | Not a substitute for bridge auth |
| APOLLO session | Approved reference for `APOLLO_SESSION_COOKIE_SECRET`, correct cookie name, `APOLLO_SESSION_COOKIE_SECURE=true`; HttpOnly/SameSite Strict; verify actual external login/logout and host-local cookie mirroring | Unchanged |
| Staff provisioning/login | Approve verified manager/owner and supervisor accounts plus a working verification/login channel. Runtime defaults to `NoopEmailSender`; optional token logging is not delivered mail; no staff role provisioning CLI was found | Unchanged |
| Resources and dates | Approve synthetic active/bookable/public-listed resource, relationships/exclusivity, facility timezone, hours, closure dates and bounded canary request window | Approve separate nonempty ATHENA evidence fixture only if bridge smoke is chosen |
| Bridge base URL | Booking has no required ATHENA setting | `APOLLO_ATHENA_BASE_URL=http://athena.athena.svc.cluster.local`, timeout at most 5s |
| Bridge secrets | None needed for direct booking | Approved paired secret references for `ATHENA_INTERNAL_READ_TOKEN` and `APOLLO_ATHENA_INTERNAL_READ_TOKEN`; never expose them through browsers or edge proxy |

The trusted-surface parser accepts comma-separated `surface=token` entries;
Themis defaults to surface `themis`, so the approved APOLLO mapping must bind
that exact key to the server-only token reference. Keep other approved entries.
No secret names or values have been invented. The approval record must name the
existing/new references and who provisions them. Packaging/configuration work
belongs to the next booking packet and requires its normal scope decisions.

## Migration, rollback and abort plan for approval

Current APOLLO/ATHENA Dockerfiles embed migrations. APOLLO's existing deployment
initContainer automatically runs pending migrations before its server. The
source delta from deployed `6b27618` contains APOLLO migration `033` only; it
adds/backfills rating-policy columns, constraints and an index. It is not a
booking or bridge migration. ATHENA requires no new R1 migration.

Before approval, capture a current migration ledger and independently
recoverable database backup, rehearse restore, and measure 033 lock/runtime
against representative disposable data. Prove old APOLLO image compatibility
with schema 033 before relying on image-only rollback. The 033 down migration
deletes wrapper rating events and resets policy metadata: do not run it as a
routine rollback. A database restore/down operation requires its own approved
scope and loss window.

Rollback image references are the exact APOLLO/ATHENA digests in the live
inventory above. Previous desired configuration is Prometheus commit
`80203883c4fbbe4b88889d8ee8c20e048c83a7e1`, with apps last-applied revision
`ad9e83f` and live `apps` reconciliation suspended. Capture live Deployment and
secret-reference metadata immediately before an approved rollout; do not resume
Flux or push GitOps under R1. Named rollback owner, execution window, fixture
IDs and an independently tested restore path remain required approval inputs.

Abort on failed readiness/health, migration error or unacceptable lock time,
login/role/trusted-surface failure, empty synthetic options, wrong public
exposure, duplicate request/reservation, missing linked cancellation,
customer-private field leak or canary scope escape. Stop further POSTs, preserve
redacted evidence, and use the named owner's approved image/config rollback;
restore database data only through the separately approved restore path.

## Smoke procedure for the next approved rollout

1. Read-only: capture ready replicas, actual imageIDs, migration ledger, health,
   metrics, public route allowlists and TLS on both external hostnames. Capture
   the initial options/availability baseline; the current empty inventory is an
   expected baseline, not nonempty canary proof. Verify the private bridge still
   returns public 404 whether or not it is enabled.
2. Approval-required fixture mutations: provision the approved synthetic staff,
   resources, hours/closures and canary window; log only fixture IDs and intended
   cleanup. Do not reuse real customer/member identities or invent availability.
   Then perform read-only checks that public options are nonempty and the chosen
   synthetic option has the expected explicit-offset availability.
3. Approval-required request: open Hestia `/intake`, choose the synthetic option
   and an explicit RFC3339 window. Before submitting, capture the original
   frontend action field values and idempotency key in the approved browser
   harness. Submit once, retain its receipt, then replay that original action
   body/key in the same origin/session context. Do not log cookies or tokens.
   Hestia clears successful fields with `blankFields()` and generates a new key,
   so normal form resubmission is not an idempotency test. Assert the replay
   returns the same receipt and leaves exactly one APOLLO request and no
   reservation; customer status is pending, not confirmed.
4. Approval-required decisions: log in through the actual Themis hostname;
   check supervisor read-only and member denial, then manager/owner review and
   approval with the current `expected_version`. Confirm one linked reservation
   through authenticated APOLLO detail/calendar, customer approved status and a
   separate public-safe message. Stale-version and resource-conflict attempts
   require the same fixture approval and must fail without extra reservations.
5. Approval-required cancellation: cancel the approved booking request using its
   latest version. Confirm the linked reservation is cancelled and customer
   status changes. Keep internal notes, contacts, request IDs, reservation IDs,
   actors, conflict details and tokens out of public responses.
6. Read-only: repeat direct booking/schedule health and reads while the local
   rehearsal has no ATHENA configured. Do not disable deployed ATHENA for this
   proof. Optional bridge smoke uses a separate image/secret/config approval and
   must return nonempty, complete v2 evidence or explicit blocked eligibility.
7. Approval-required cleanup: cancel only the recorded canary requests/blocks,
   retire canary resources/staff by an approved path, and preserve audit records.
   No broad deletes or automatic schema down migration.

The API diagnostic payload shape, if separately approved, is:

```json
{
  "option_id": "the approved synthetic option ID",
  "requested_start_at": "2026-11-20T09:00:00-05:00",
  "requested_end_at": "2026-11-20T10:00:00-05:00",
  "contact_name": "Synthetic Canary",
  "contact_email": "canary@example.invalid",
  "purpose": "Approved closed-test canary"
}
```

POST it only to `/api/v1/public/booking/requests` with the retained
`Idempotency-Key` and `X-Apollo-Public-Intake-Channel: public_web`. The example is
not approved fixture data. Staff decision bodies carry `expected_version` and
optional internal notes; public messages use the separate `public_message`
field. Browser-to-frontend form actions remain the required external journey,
not a direct-API substitute.

## Publication automation audit

APOLLO and ATHENA `.github/workflows/ci-image.yml` run tests and build/publish
GHCR `main` and SHA tags after main pushes. The live Deployments pin immutable
digests. Live Flux has only Prometheus main as its Git source; `apps` is
suspended and image toolkit resources are absent. Thus scoped service-source
pushes publish images without an observed reconciliation path. This does not
authorise any rollout, GitOps push, Flux resume or secret/config change.

Hestia/Themis have no tracked image publication workflows. Platform is docs-only.
Remote source SHA, actual CI outcome and image publication are recorded
separately below; none imply deployed acceptance.

## Frontend executed results and failure classification

Both repositories passed `npm run check`, `npm test` and `npm run build` with
existing dependencies and installed Playwright browsers. No prerequisite was
installed and no lockfile/configuration was changed.

| Exact command / scope | Executed result | Meaning |
| --- | --- | --- |
| Hestia `npm run test:e2e` | 39 passed, 1 failed | All 18 public intake/options/availability/receipt/status assertions passed across desktop/mobile; member lobby mutation failed on desktop. |
| Hestia `npx playwright test tests/e2e/shell.spec.ts --grep 'member shell stays member-safe' --workers=1` | 2 passed | Isolated member rerun is green; initial full-suite failure remains disclosed as unresolved flakiness. |
| Themis `npm run test:e2e` | 77 passed, 6 failed, 1 skipped | Four booking/schedule failures plus two competition selector failures; not demo-ready. |
| Themis `npx playwright test tests/e2e/shell.spec.ts --grep 'manager can create and cancel|manager booking edit form preserves|staff can triage public|competition command denials' --workers=1` | 4 passed, 4 failed | Edit/message cases passed; cancellation and duplicate-text selector failures reproduced in both browser projects. |

Exact failure excerpts from the requested full commands:

```text
Hestia shell.spec.ts:52 desktop:
getByRole('button', { name: 'Leave lobby' }): expected visible; element not found.

Themis shell.spec.ts:237 desktop/mobile:
getByText('trusted surface token is invalid') resolved to 2 elements.

Themis shell.spec.ts:274 desktop/mobile:
getByRole('button', { name: 'Cancel block' }): expected 1; received 0.

Themis shell.spec.ts:957, called from :500 desktop:
Resource key: expected empty string; received "court-101".

Themis shell.spec.ts:388 mobile:
getByText('We are reviewing your updated details.').first(): element not found.
```

The cancellation fixture uses April 24, 2026 and the actual UI compares its end
time to `new Date()`, so the future-only action is correctly absent on October
20. Repair the dated fixture/clock in the next packet. The competition assertion
is an ambiguous selector over two matching messages, not a denied-operation
failure. The member lobby, edit and public-message failures did not reproduce
in isolated serial runs; do not call those initial results passes or assert a
root cause without further evidence. All these e2e tests use mock APOLLO and
prove frontend behaviour only. Raw local outputs remain at
`/tmp/r1-{hestia,themis}-{check,unit,build,e2e}.log`, with isolated logs at
`/tmp/r1-hestia-member-recheck.log` and `/tmp/r1-themis-booking-recheck.log`.
They are not committed artifacts or deployed acceptance.

The next implementation packet must separate these frontend repairs from
read-only backend proof and then test the nonempty external journey with real
APOLLO. R1 does not silently absorb frontend runtime work.

## Reproduce the confirmed frontend failure states

These commands use installed TypeScript, transpile current functions in memory
and inject upstream behaviour without file writes or network requests. Run
from the named frontend checkout. They supplement browser tests and do not
replace real-service proof.

Hestia:

```sh
node --input-type=module <<'NODE'
import fs from 'node:fs';
import ts from 'typescript';
let source = fs.readFileSync('src/lib/server/public-intake.ts', 'utf8')
  .replace(/^import .* from '\$lib\/.*';\n/gm, '');
source = "const apolloOrigin = () => 'http://127.0.0.1:1';\n" + source;
const compiled = ts.transpileModule(source, {
  compilerOptions: { module: ts.ModuleKind.ES2022, target: ts.ScriptTarget.ES2022 }
}).outputText;
const intake = await import('data:text/javascript;base64,' +
  Buffer.from(compiled).toString('base64'));
const fields = {
  ...intake.blankFields(), option_id: 'test-option',
  requested_start_at: '2026-11-20T14:00:00Z',
  requested_end_at: '2026-11-20T15:00:00Z',
  contact_name: 'Synthetic Canary', contact_email: 'canary@example.invalid'
};
for (const [name, fn] of [
  ['receipt', () => intake.submitIntake(fields,
    async () => new Response('{}', { status: 202 }))],
  ['status', () => intake.lookupPublicStatus({ receipt_code: 'BR-TEST' },
    async () => new Response('{}', { status: 200 }))],
  ['network', () => intake.lookupPublicStatus({ receipt_code: 'BR-TEST' },
    async () => { throw new TypeError('fetch failed'); })]
]) {
  try { console.log(name, JSON.stringify(await fn())); }
  catch (error) { console.log(name, 'REJECTED:', error.message); }
}
NODE
```

Expected current failures: receipt `status must be a string`; status
`receipt_code must be a string`; network `fetch failed`.

Themis:

```sh
node --input-type=module <<'NODE'
import fs from 'node:fs';
import ts from 'typescript';
let source = fs.readFileSync('src/routes/ops/bookings/new/+page.server.ts', 'utf8')
  .replace(/^import (?!.*node:crypto).*\n/gm, '');
source = `
class ApiError extends Error { constructor(status) { super('test'); this.status = status; } }
const redirect = (status, location) => ({ status, location });
const fail = (status, value) => ({ status, value });
const createApolloServerClient = () => ({ listBookingRequests: async () => {
  throw new Error('upstream connection refused');
} });
` + source;
const compiled = ts.transpileModule(source, {
  compilerOptions: { module: ts.ModuleKind.ES2022, target: ts.ScriptTarget.ES2022 }
}).outputText;
const route = await import('data:text/javascript;base64,' +
  Buffer.from(compiled).toString('base64'));
const result = await route.load({
  request: new Request('http://localhost/ops/bookings/new')
});
console.log(JSON.stringify({ canManage: result.canManage,
  hasSafetyKey: Boolean(result.idempotencyKey),
  hasErrorState: 'state' in result || 'loadError' in result }));
NODE
```

Observed: `{"canManage":false,"hasSafetyKey":true,"hasErrorState":false}`.

## G1-G3 verification ledger

All commands ran with existing installed dependencies. Final source was
rechecked after review repairs. The initial APOLLO broad race failure is kept
below; the serial diagnostic does not replace its disclosure.

| Repository / command | Result | Local evidence |
| --- | --- | --- |
| ATHENA `go test -count=1 ./internal/edge ./internal/edgehistory ./internal/presence ./internal/publish ./internal/server ./cmd/athena` | PASS; exit 0; 23.78s | `/tmp/r1-athena-final-focused.log` |
| ATHENA `go vet ./...` | PASS; exit 0; 0.46s | `/tmp/r1-athena-final-vet.log` |
| ATHENA `go build ./cmd/athena` | PASS; exit 0; 3.7s | `/tmp/r1-athena-final-build.log` |
| ATHENA `go test -count=1 ./...` | PASS; exit 0; 25.82s | `/tmp/r1-athena-final-full.log` |
| ATHENA `go test -race ./internal/...` | PASS; exit 0; 25.62s | `/tmp/r1-athena-final-race.log` |
| ATHENA `git diff --check` | PASS; exit 0; 0.02s | `/tmp/r1-athena-final-diff.log` |
| APOLLO `go test -count=1 ./internal/athena ./internal/presence ./internal/consumer ./internal/visits ./internal/booking ./internal/schedule ./internal/authz ./internal/server ./cmd/apollo` | PASS; exit 0; 193.47s | `/tmp/r1-apollo-focused.log` |
| APOLLO `go test -race ./internal/...` | FAIL, retained; exit 1; 356.24s | `/tmp/r1-apollo-race.log` |
| APOLLO `go test -count=1 ./internal/server -run Test(PublicBooking|BookingRequest|Schedule)` | PASS; exit 0; 38.48s | `/tmp/r1-apollo-booking-no-athena.log` |
| APOLLO `go test -count=1 -v ./cmd/apollo -run TestPresenceAthenaGateRealServicePostgres` | PASS; exit 0; 8.36s; explicit real-service opt-in enabled | `/tmp/r1-apollo-final-cross-service.log` |
| APOLLO `go test -count=1 ./internal/athena ./internal/presence ./cmd/apollo` | PASS; exit 0; 19.58s | `/tmp/r1-apollo-final-changed-focused.log` |
| APOLLO `go vet ./...` | PASS; exit 0; 0.59s | `/tmp/r1-apollo-final-vet.log` |
| APOLLO `go build ./cmd/apollo` | PASS; exit 0; 1.9s | `/tmp/r1-apollo-final-build.log` |
| APOLLO `go test -count=1 ./...` | PASS; exit 0; 198.21s | `/tmp/r1-apollo-final-full.log` |
| APOLLO `git diff --check` | PASS; exit 0; 0.04s | `/tmp/r1-apollo-final-diff.log` |
| APOLLO `go test -race -p 1 ./internal/...` | PASS; exit 0; 185.43s | `/tmp/r1-apollo-race-serial-diagnostic.log` |
| APOLLO `go test -race ./internal/...` | PASS; exit 0; 0.45s; cached after successful serial run | `/tmp/r1-apollo-race-requested-recheck.log` |

The exact original broad race failure was unrelated to bridge code:

```text
--- FAIL: TestWorkoutRecommendationRuntimeFollowsDeterministicPrecedence (154.91s)
recommendation_runtime_integration_test.go:18: StartPostgres() error =
postgres did not become ready within 2m0s: failed to connect to
user=apollo database=apollo at 127.0.0.1:5432: connect: connection refused
FAIL github.com/ixxet/apollo/internal/server
```

No data-race diagnostic was emitted. The full serial race diagnostic passed;
the exact requested command then passed with cached successful results. A
fixture-startup/concurrency issue is the supported classification, not a proven
bridge regression or a reason to change shared infrastructure under R1. The
raw initial failure remains `/tmp/r1-apollo-race.log`.

Hestia check/unit/build pass (27 unit tests); Themis check/unit/build pass
(38 unit tests). Their exact e2e failures and isolated reruns are recorded above.
Platform and changed Markdown passed `git diff --check`; added local links were
resolved, 20 shell blocks parsed with `bash -n`, and both saved
no-network frontend reproduction commands executed successfully. Neither
frontend full e2e command is claimed green.

The final APOLLO text-only follow-up keeps incomplete context visible even when
there are zero evidence rows. `go test -count=1 ./cmd/apollo`, the opted-in real
cross-service rehearsal, `go vet ./...`, `go build ./cmd/apollo` and
`git diff --check` all passed after that addition. Logs are
`/tmp/r1-apollo-text-{cli,cross-service,vet,build,diff}.log`. No internal runtime
or shared analytics code changed in this follow-up.

## G4 GitHub publication and current closeout

Source and repo documentation pushes are verified independently with
`git ls-remote origin refs/heads/main`. No PR/issue tracker was invented; G0's
issue/PR searches found no overlapping entries. Only scoped source, tests and
Markdown were staged. No test artifacts, unrelated dirty docs, GitOps manifests
or secrets were committed.

| Repo | Implementation / docs commits | Verified remote main | CI / image state at this capture |
| --- | --- | --- | --- |
| ATHENA | [`43bcfb6`](https://github.com/ixxet/athena/commit/43bcfb6958a68945d789558d57129eabd8501aa6) runtime; [`b52376b`](https://github.com/ixxet/athena/commit/b52376b1551deb5ca810d99598aab1ceb9954325) contract/runbook/README | `b52376b1551deb5ca810d99598aab1ceb9954325` | Both observed ci-image runs passed tests and image publication. |
| APOLLO | [`7fab80e`](https://github.com/ixxet/apollo/commit/7fab80e0373417a8832cde9c7e425bac213448c3) runtime/proof; [`43b52f9`](https://github.com/ixxet/apollo/commit/43b52f9bee6d01be77e73345c31ba9608c6b08af) docs; [`c64b349`](https://github.com/ixxet/apollo/commit/c64b34928f090f48f7ff5ead73d5fbceaf29eb33) explicit text completeness | `c64b34928f090f48f7ff5ead73d5fbceaf29eb33` | Final-head test and image jobs succeeded; published registry digest verified below. |
| Hestia | [`13f5cbc`](https://github.com/ixxet/hestia/commit/13f5cbcaf41fc907fb911f41abd41f647e034c97) scoped README readiness note | `13f5cbcaf41fc907fb911f41abd41f647e034c97` | No configured workflow/image publication; local frontend results remain above. |
| Themis | [`15ca377`](https://github.com/ixxet/themis/commit/15ca377f27f2edb0b1528b1d270c120032911319) scoped README readiness note | `15ca377f27f2edb0b1528b1d270c120032911319` | No configured workflow/image publication; local frontend results remain above. |
| Platform | [`802ac0a`](https://github.com/ixxet/ashton-platform/commit/802ac0a4f338f269f0ae8a944961e7b416c3d6f0) canonical readiness record plus SAIL, CORE-ONE-PART-TWO, IMPLEMENTATION-BOARD, bridge preflight, route/API matrix, FLOWS, DEFERMENTS and compatibility overlay; this file's follow-up records final CI and smoke corrections | Initial publication `802ac0a4f338f269f0ae8a944961e7b416c3d6f0` verified; final closeout SHA is this file's latest Git history entry | Docs-only; no runtime/image workflow. |

ATHENA CI: [run 39445588763](https://github.com/ixxet/athena/actions/runs/39445588763)
and [run 39445587824](https://github.com/ixxet/athena/actions/runs/39445587824)
both succeeded. They published the same source SHA in two observed push runs;
only one configured workflow exists and the reason for duplicate runs was not
established. Their image-index digests differ. A registry read confirms the
SHA tag currently resolves to:

```text
ghcr.io/ixxet/athena:sha-b52376b@sha256:6d0165f85f184406e8a4d056c9290ed6be4301102945f3ca60c922efdf452df0
```

The other run's pushed digest was
`sha256:996278887888c74ffe9a1788bb6c29a17c8e02d1443ae6871dbc2f2abbaae18c`.
Approval must choose an immutable digest, never assume a mutable `main` or SHA
name is immutable. The registry index includes linux/amd64 and linux/arm64.
APOLLO CI for the [initial docs head](https://github.com/ixxet/apollo/actions/runs/39445594339)
and [final text-completeness head](https://github.com/ixxet/apollo/actions/runs/39446321522)
both succeeded. The final head's test job completed at 15:20:14 UTC and image
job at 15:21:58 UTC on 2026-10-20. A registry read confirms the final SHA tag
resolves to the following linux/amd64 and linux/arm64 image index:

```text
ghcr.io/ixxet/apollo:sha-c64b349@sha256:6a70af9d891accd3252ff68754fac6440d5cf87300c234420b7563fcf6fa8819
```

The earlier APOLLO docs head published
`sha-43b52f9@sha256:55d8bb9730d3802272605ff2006d1e7c526a5caef19a2868735ae3581a9367b0`;
it does not contain the final text-completeness follow-up. Registry verification
used `docker buildx imagetools inspect ghcr.io/ixxet/apollo:sha-c64b349 --format '{{json .Manifest}}'`
and the equivalent ATHENA SHA-tag inspection. Published
image indices are candidates for a separately approved rollout, not deployed
acceptance.

| Gate | Verdict |
| --- | --- |
| G0 current source / overlap | Passed; all six actual remotes verified, dirty work preserved and old Hestia/Themis access gaps resolved. |
| G1 bridge runtime | Repaired and locally verified; explicit incomplete/unknown evidence fails closed. |
| G2 real local proof | Passed actual ATHENA HTTP/service/PostgreSQL -> APOLLO client/classifier/CLI, with unchanged APOLLO product counts. |
| G3 booking / frontend / ops | Audit executed; external closed-test readiness is blocked by the concrete frontend, packaging, staff/login, inventory, migration/restore and approval gaps above. |
| G4 documentation / publication | Scoped source and canonical/repo docs published; all five ASHTON remote heads verified. Final backend tests and image publication succeeded; registry digests verified. |

The external booking launch is not closed. Next packet: Booking/Scheduling
Closed-Test Implementation + Deployment, centred on Hestia, Themis and APOLLO.
Resolve the listed gaps and prove external request -> staff approval -> real
reservation -> customer status/cancellation after fresh live approval. Do not
substitute daily credits, public tournaments or OpenSkill work.

Unrelated working-tree state is preserved: APOLLO's six pre-existing dirty docs
and two generated artifacts; ATHENA's two generated artifacts; platform's nine
dirty docs; Prometheus's seven dirty files. Hestia/Themis are clean after their
scoped README commits. No runtime/configuration file was changed in either
frontend or Prometheus.

Live state remains unchanged: the deployed APOLLO and ATHENA digests are the
historical pinned digests in G3, not the newly published images. A bounded
read-only safety refresh after final image publication reconfirmed both desired
image pins, one ready replica each, `apps` suspended and no image automation
resources. No rollout, deployment
manifest write, secret operation, live migration, fixture mutation, restart,
kill or destructive probe was performed. Live execution still requires the
fresh target/images/diff/secrets/migrations/fixtures/window/rollback/abort
approval described above.
