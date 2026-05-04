# Live Destructive Probe Plan

Status: Packet 8 planning artifact
Date: 2026-05-04
Scope: APOLLO and ATHENA deploy/ops safety planning

This artifact is a go/no-go runbook for a later operator-approved execution
packet. Packet 8 did not execute any live destructive action, live mutating API
request, rollout restart, pod kill, `kubectl delete`, DB write, or production
SIGTERM proof.

## Ruling

ASHTON has a controlled plan for future bounded live destructive mutation and
in-flight SIGTERM proof.

This plan closes planning ambiguity only. It does not close the live destructive
probe residual, does not change runtime or deployed truth, and does not authorize
execution without a separate user/operator-approved execution packet.

## Gate 0 Findings

### Repo State

| Repo or path | Gate 0 state | Packet 8 handling |
| --- | --- | --- |
| `ASHTON/ashton-platform` | On `main...origin/main`; dirty unrelated docs existed before Packet 8. | Touch only Packet 8 planning docs; do not revert unrelated work. |
| `ASHTON/apollo` | On `main...origin/main`; dirty unrelated docs plus untracked `gotest.json` and `junit.xml` existed before Packet 8. | Touch only launch-expansion planning docs if needed; do not stage unrelated files. |
| `ASHTON/athena` | On `main...origin/main`; untracked `gotest.json` and `junit.xml` existed before Packet 8. | Touch only roadmap planning truth if needed; do not stage untracked artifacts. |
| `ASHTON/hestia` | On `main...origin/main`; clean at Gate 0. | Untouched. |
| `ASHTON/themis` | On `main...origin/main`; clean at Gate 0. | Untouched. |
| `Computers/Talos/homelab-gitops` | Present locally, but not a Git repo in this workspace. | Read-only inventory only; no edits. |
| `Computers/Prometheus/homelab-gitops` | Present locally, but not a Git repo in this workspace. | Read-only inventory only; no edits. |
| `Computers/ops` and `Computers/ops/talos` | Present locally, but not Git repos in this workspace. | Read-only inventory only; no edits. |

### Existing Residual Risk References

Live destructive probe and SIGTERM residual risk is already recorded in:

- `planning/milestones/milestone-3.0-evidence/README.md`
- `planning/FLOWS.md`
- `planning/DEFERMENTS.md`
- `planning/IMPLEMENTATION-BOARD.md`
- `apollo/docs/launch-expansion-audit.md`
- `apollo/docs/roadmap.md`
- `athena/docs/roadmap.md`

The consistent current truth is:

- Milestone 3.0 closed bounded APOLLO/ATHENA deploy smoke, APOLLO `/metrics`,
  Prometheus scrape proof, telemetry export, and compatibility matrix truth.
- Milestone 3.0 did not run production-destructive APOLLO mutation probes.
- Milestone 3.0 accepted graceful shutdown with residual risk: rollout completed
  and health/metrics recovered, but direct in-flight production SIGTERM proof
  was not run.
- Hestia, Themis, and gateway live cluster deployment proof remains absent.
- Gate 8 scale ceilings are locally/runtime-proved only, not full production
  load validation.

### Deployed Smoke Truth

Previously smoke-proved APOLLO deployed routes and surfaces:

- `GET /api/v1/health`
- `GET /api/v1/public/competition/readiness`
- `GET /metrics`

Previously smoke-proved ATHENA deployed routes and surfaces:

- `GET /api/v1/health`
- `GET /metrics`
- facility-scoped `GET /api/v1/presence/count`

Prometheus evidence already exists for:

- APOLLO ServiceMonitor scrape.
- `up{job="apollo"}` returning `1` during Milestone 3.0 evidence capture.
- APOLLO competition/rating/ARES/booking/safety/reliability/game-identity
  metrics exported from `/metrics`.
- Prometheus GitOps `v0.0.4` carrying APOLLO ServiceMonitor, APOLLO competition
  PrometheusRule, and APOLLO image proof.
- ATHENA ServiceMonitor manifest present in the inspected Prometheus inventory.

### Deploy/Ops Inventory

Read-only local deploy inventory shows Kubernetes manifests for APOLLO, ATHENA,
Prometheus scraping, alert rules, and a shared Postgres StatefulSet with a
local-path PVC.

The inventory is useful for future command skeletons, but it is not enough to
authorize live destructive execution because this packet found no concrete
operator-approved canary target or DB restore runbook.

### Fixture And Rollback Gaps

No documented staging/canary environment was found.

No disposable live fixture identities were found for:

- APOLLO users
- APOLLO facilities
- APOLLO competitions
- APOLLO sessions, matches, results, tournaments, bookings, or requests
- ATHENA facilities
- ATHENA nodes
- ATHENA accounts or identity subjects

No concrete production DB backup/restore path was found for APOLLO or ATHENA.
APOLLO launch-expansion docs include rating rollback/cutover discipline, but
that is not a complete production database restore plan. The inspected deploy
inventory shows shared Postgres storage, so later destructive execution remains
blocked until backup, restore, and rollback ownership are explicit.

### Gate 0 Verdict

Packet 8 should remain docs-only.

Later execution can be safely split into separate APOLLO and ATHENA execution
packets. Splitting is preferred because APOLLO proves member/competition/booking
mutation safety, while ATHENA proves physical ingress/restart/replay behavior.

## Truth Boundaries

| Truth layer | Packet 8 result |
| --- | --- |
| Repo/docs truth | ASHTON now has a controlled plan for future live destructive mutation and SIGTERM proof. |
| Repo/runtime truth | Unchanged. No APOLLO or ATHENA runtime code changed. |
| Deployed truth | Unchanged. No live destructive probe was executed. |
| Product truth | Unchanged. Public tournaments, OpenSkill read-path cutover, public/member safety UI, messaging/chat, broad social graph, and booking/commercial/proposal workflows remain blocked. |
| Frontend truth | Hestia and Themis remain consumers only and were not touched. |

## Operator Approval Checklist

A later execution packet may start only when every item is true:

- The user explicitly approves live execution for one service scope: APOLLO or
  ATHENA.
- The execution packet names the operator of record.
- The target environment is named.
- Current deployed image digests are captured.
- Current pod names, deployment generations, service endpoints, and readiness
  states are captured.
- Metrics baseline and log baseline are captured before mutation.
- Disposable fixture identities are documented with redacted aliases.
- The fixture is isolated from real member, customer, competition, booking,
  safety, and facility truth.
- DB backup or restore strategy is documented for the exact target data store.
- Rollback owner and rollback command path are named.
- Abort gates are reviewed before the first mutating command.
- Evidence redaction rules are accepted before logs or command output are
  copied into committed docs.

If any item is false, execution is blocked.

## Hard Abort Gates

Abort before execution if:

- A probe would mutate real member, customer, competition, booking, safety, or
  physical facility truth.
- A fixture identity cannot be proven disposable.
- The target points at an unknown environment.
- The deployed image digest cannot be captured.
- Health/readiness baseline fails before mutation.
- Prometheus scrape baseline fails before mutation.
- Log access is unavailable.
- DB backup/restore or rollback ownership is missing.
- The operator cannot distinguish repo/local proof from deployed proof.
- A command requires raw credentials, private tokens, or unredacted production
  IDs in a committed artifact.
- The execution packet attempts to change deploy/GitOps manifests.
- The execution packet widens into frontend, dashboards, public tournaments,
  OpenSkill cutover, messaging/chat, social graph, public/member safety detail,
  or booking/commercial/proposal workflow.

Abort during execution if:

- Any non-fixture row changes.
- A rejected request creates durable accepted truth.
- A conflict/version probe mutates the target despite stale input.
- APOLLO health or readiness remains degraded after the configured recovery
  window.
- ATHENA projection/readiness fails to recover after restart.
- Prometheus scrape does not return to baseline.
- Logs show panic, data race symptoms, migration failure, token leak, or raw
  PII.
- Rollback becomes ambiguous.

## Evidence Ledger Format

Later execution packets must write an evidence ledger using redacted aliases,
not raw production identifiers.

```md
## Probe Evidence: <probe-id>

Service:
Environment:
Operator:
Approval reference:
Start time UTC:
End time UTC:
Current image digest:
Target pod/deployment/service:
Fixture alias:
Fixture isolation proof:
Preflight health:
Preflight readiness:
Preflight metrics:
Preflight logs:
Command class:
Command executed:
Expected behavior:
Observed behavior:
Readback proof:
Metrics proof:
Log proof:
Rollback readiness:
Rollback executed:
Post-probe health:
Post-probe readiness:
Post-probe metrics:
Abort gate triggered:
Residual risk:
Redactions applied:
```

Committed evidence must redact:

- session cookies
- bearer tokens
- trusted-surface proofs
- edge node tokens
- database URLs
- secret names when they reveal private infrastructure
- raw member IDs
- raw customer contacts
- raw facility/node/account IDs unless already public in committed docs
- raw booking, request, competition, session, match, result, or tournament IDs
- private log lines containing PII or credential material

Use stable aliases such as `<fixture_member_a>`, `<canary_facility>`,
`<canary_competition>`, and `<canary_node>`.

## Read-Only Preflight Plan

These are future execution packet skeletons. They are listed here to define
evidence requirements. They were not run by Packet 8.

```sh
# Read-only future preflight only.
kubectl -n <namespace> get deploy,svc,pods

# Read-only future preflight only.
kubectl -n <namespace> get deployment apollo -o jsonpath='<redacted-image-jsonpath>'

# Read-only future preflight only.
kubectl -n <namespace> get deployment athena -o jsonpath='<redacted-image-jsonpath>'

# Read-only future preflight only.
curl -fsS <apollo_base_url>/api/v1/health

# Read-only future preflight only.
curl -fsS <apollo_base_url>/api/v1/public/competition/readiness

# Read-only future preflight only.
curl -fsS <apollo_base_url>/metrics

# Read-only future preflight only.
curl -fsS <athena_base_url>/api/v1/health

# Read-only future preflight only.
curl -fsS '<athena_base_url>/api/v1/presence/count?facility=<canary_facility>'

# Read-only future preflight only.
curl -fsS <athena_base_url>/metrics

# Read-only future preflight only.
kubectl -n <namespace> logs deployment/apollo --since=15m

# Read-only future preflight only.
kubectl -n <namespace> logs deployment/athena --since=15m
```

Prometheus baselines should capture scrape liveness and only the metric names
needed for the target probe. Do not commit raw label values if they contain
private pod, namespace, facility, node, or tenant details.

## APOLLO Future Probe Matrix

Every APOLLO probe is blocked until an isolated disposable fixture and concrete
rollback path exist.

| ID | Route/API category | Mutation type | Fixture requirement | Expected behavior | Readback proof | Metrics/log proof | Rollback requirement | Abort condition | Later execution status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| A-LIVE-01 | Mutating JSON route, preferably `POST /api/v1/public/booking/requests` or `POST /api/v1/competition/commands` against canary-only data | Malformed JSON rejection | Disposable route target; no real customer/member data; no trusted-surface proof committed | HTTP 400-class rejection; no durable request, command, result, booking, or audit row | Pre/post fixture-scoped reads show no new row and no version change | Logs show validation rejection without panic or token/PII leak; metrics show health scrape still healthy | No data rollback should be needed; DB restore still available before probe | Any durable write or non-fixture row change | Safe only after fixture and rollback gates pass |
| A-LIVE-02 | Mutating JSON route with request body limit | Oversized JSON rejection | Disposable target and payload that cannot bind to real data | HTTP 413 or 400-class rejection before service mutation | Pre/post reads show no new row and no version change | Logs show bounded body rejection; `/metrics` remains scrapeable | No data rollback should be needed; DB restore still available before probe | Request reaches mutation handler or creates durable truth | Safe only after fixture and rollback gates pass |
| A-LIVE-03 | Idempotency-keyed public booking or supported staff request route | Duplicate/replay handling | Disposable booking option/request fixture; fixed `Idempotency-Key`; no real customer contact | Same-key same-payload returns the original accepted receipt or equivalent idempotent outcome; same-key different-payload returns conflict/rejection | Readback shows one request/receipt, not two | Logs identify idempotency path; duplicate/replay metrics or booking metrics remain bounded if exported | Fixture cleanup or DB restore path documented | Duplicate durable rows, private receipt leak, or real customer row touched | Blocked until disposable booking fixture exists |
| A-LIVE-04 | Versioned competition result, tournament, booking, or schedule route | Conflict/stale version rejection | Disposable fixture with known current version and stale test version | HTTP 409-class rejection; canonical state remains unchanged | Readback shows unchanged version/status and no superseding lifecycle event | Logs show stale/version rejection; result or booking reject metrics increment only on fixture path if exported | Fixture cleanup or DB restore path documented | Stale request mutates state | Blocked until versioned fixture exists |
| A-LIVE-05 | Result lifecycle APIs or competition command route | Controlled result lifecycle mutation | Isolated disposable competition/session/match/result with canary participants only; trusted-surface proof supplied only at runtime | Record/finalize/dispute/correct/void sequence succeeds only within expected state machine | Readback shows expected lifecycle, canonical result identity, rating eligibility boundaries, and no public/private leak | Logs show expected lifecycle events; result write metrics and health metrics remain healthy | Fixture cleanup or DB restore path documented before probe | Mutation touches real competition, leaks raw private IDs, or leaves invalid lifecycle | Blocked until disposable competition fixture exists |
| A-LIVE-06 | Booking request lifecycle APIs | Controlled booking/request mutation | Disposable booking option/request/customer alias only; no real customer contact; only if current runtime supports isolated fixture | Staff/public request mutation follows request-only and approval/cancel semantics; no public self-booking or commercial workflow | Readback shows fixture request status/version and linked schedule behavior only when expected | Logs show trusted-surface staff mutation or public idempotency path without private leak | Fixture cleanup or DB restore path documented before probe | Any real booking/customer/schedule row touched or commercial workflow opened | Optional and blocked until disposable booking fixture exists |
| A-LIVE-07 | HTTP request path plus deployment/pod lifecycle | In-flight SIGTERM or rollout during bounded fixture request | Canary-only request that can be safely repeated; known target pod; operator-approved SIGTERM method | Request either completes once or fails cleanly with retry-safe outcome; process exits gracefully; replacement pod becomes ready | Readback proves no partial duplicate and no corrupt fixture state | Logs show graceful shutdown path; health/readiness and Prometheus scrape recover | Rollback to previous image or restore path documented; no manifest edit in execution packet | Unknown pod target, non-fixture mutation, readiness does not recover, or duplicate side effect | Blocked until canary, timing method, and rollback are approved |

APOLLO execution packet notes:

- Prefer rejection probes before accepted mutation probes.
- Use public booking only for request-only fixture proof. Do not turn this into
  public self-booking.
- Use competition/result fixtures only if they are fully isolated from real
  members, sessions, tournaments, safety facts, and public projections.
- OpenSkill remains comparison-only. Do not use this packet to switch rating
  read paths.
- Public tournaments remain blocked.

## ATHENA Future Probe Matrix

Every ATHENA probe is blocked until a disposable facility/node/account fixture
and concrete rollback/replay path exist.

| ID | Route/CLI/API category | Mutation type | Fixture requirement | Expected behavior | Readback proof | Metrics/log proof | Rollback requirement | Abort condition | Later execution status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| T-LIVE-01 | `POST /api/v1/edge/tap` | Controlled accepted edge tap ingress | Disposable facility, node token, and account/subject fixture | Accepted fixture tap appends source observation and updates accepted presence/projection according to policy | Facility-scoped count/history/analytics or `athena edge ingress-bridge` readback shows only fixture effect | Logs show accepted fixture path; metrics/readiness remain healthy | Replay/restore plan documented; append-only fixture rows must be isolated | Real facility/account truth changes or accepted source truth cannot be scoped to fixture | Blocked until disposable edge fixture exists |
| T-LIVE-02 | `POST /api/v1/edge/tap` denied path | Rejected bad token, node mismatch, invalid direction, or invalid result | Disposable or intentionally invalid canary node/account; no real node token committed | HTTP 401/403/400-class rejection; no false accepted presence or source-pass session created | Readback shows no accepted presence/session for the rejected fixture | Logs show bounded rejection without token leak; metrics/readiness remain healthy | No data rollback should be needed; restore still available before probe | Rejected tap creates accepted truth or logs token material | Safe only after log redaction and rollback gates pass |
| T-LIVE-03 | ATHENA policy/identity CLI plus tap path | Recognized-denied policy path | Disposable policy, identity subject, facility, and node fixture; explicit operator approval | Source `fail` truth remains immutable; accepted-presence truth records only the intended canary policy outcome | `athena edge ingress-bridge` shows source truth, accepted-presence truth, and source-pass session separation | Logs show policy decision without private identity hash leak | Fixture cleanup or DB restore/replay path documented | Source fail is rewritten, accepted session cutover happens, or real account affected | Blocked; Milestone 3.0 intentionally did not inject synthetic production recognized-denied tap |
| T-LIVE-04 | Deployment restart plus read path | Restart/reload projection recovery | Canary environment or approved production canary window; known current image and pod target | Restart completes; projection reloads from durable observations; readiness returns healthy | Pre/post count/history/analytics reads match expected fixture truth | Logs show replay/reload without panic; Prometheus scrape returns to baseline | Image rollback and DB restore/replay path documented | Projection loses durable fixture truth or readiness remains degraded | Blocked until operator-approved restart window exists |
| T-LIVE-05 | HTTP ingress/publish path plus pod lifecycle | In-flight SIGTERM during tap ingress or publish | Disposable tap fixture, known target pod, repeat-safe command, and operator-approved SIGTERM method | Request completes once or fails cleanly; no duplicate accepted presence; replacement pod recovers | Readback shows one fixture observation/outcome or a clean rejection with no partial truth | Logs show graceful shutdown path; metrics/readiness recover | Restore/replay and image rollback path documented | Duplicate observation, partial accepted presence, or readiness does not recover | Blocked until canary, timing method, and rollback are approved |
| T-LIVE-06 | Health/readiness/metrics after restart | Metrics/readiness recovery proof | Same canary restart context as T-LIVE-04 or T-LIVE-05 | `/api/v1/health`, `/metrics`, and Prometheus scrape return to baseline | Readback confirms service-level and fixture-level recovery | Logs show stable post-restart state | Image rollback path documented | Scrape or readiness remains degraded after recovery window | Blocked until restart proof is approved |

ATHENA execution packet notes:

- Source-pass truth and accepted-presence truth must remain separate.
- Accepted-presence session cutover remains deferred.
- Policy and identity management stay internal CLI-only.
- No operator UI, public report/export surface, XP ledger, teams, or reliability
  scoring is opened by this plan.

## Future Command Skeletons

The commands below are examples for a later execution packet. They are not
complete runbooks by themselves. Mutating examples are explicitly marked and
must not be run from Packet 8.

### APOLLO Rejection Probe Skeletons

```sh
# DO NOT RUN IN THIS PACKET.
# Future malformed JSON rejection probe against a disposable target only.
curl -fsS -X POST '<apollo_base_url>/api/v1/public/booking/requests' \
  -H 'Content-Type: application/json' \
  -H 'Idempotency-Key: <canary_idempotency_key>' \
  --data-binary '{"invalid":'

# DO NOT RUN IN THIS PACKET.
# Future oversized JSON rejection probe against a disposable target only.
perl -e 'print "{\"payload\":\"" . ("x" x <oversized_byte_count>) . "\"}"' \
  | curl -fsS -X POST '<apollo_base_url>/api/v1/public/booking/requests' \
      -H 'Content-Type: application/json' \
      -H 'Idempotency-Key: <canary_idempotency_key>' \
      --data-binary @-
```

### APOLLO Controlled Mutation Skeleton

```sh
# DO NOT RUN IN THIS PACKET.
# Future stale-version rejection probe against a disposable fixture only.
curl -fsS -X POST '<apollo_base_url>/api/v1/competition/matches/<canary_match>/result' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer <redacted_operator_token>' \
  -H 'X-Apollo-Trusted-Surface-Proof: <redacted_runtime_only_proof>' \
  --data-binary @<redacted_fixture_payload_file>

# DO NOT RUN IN THIS PACKET.
# Future readback after fixture mutation or rejection.
curl -fsS '<apollo_base_url>/api/v1/competition/sessions/<canary_session>'
```

### ATHENA Tap Skeletons

```sh
# DO NOT RUN IN THIS PACKET.
# Future accepted canary tap only.
curl -fsS -X POST '<athena_base_url>/api/v1/edge/tap' \
  -H 'Content-Type: application/json' \
  -H 'X-Athena-Node-Token: <redacted_runtime_only_node_token>' \
  --data-binary @<redacted_canary_tap_payload_file>

# DO NOT RUN IN THIS PACKET.
# Future denied tap path. Never commit the real or invalid token value.
curl -fsS -X POST '<athena_base_url>/api/v1/edge/tap' \
  -H 'Content-Type: application/json' \
  -H 'X-Athena-Node-Token: <redacted_invalid_canary_token>' \
  --data-binary @<redacted_denied_tap_payload_file>
```

### Future SIGTERM Or Rollout Skeletons

Prefer a canary deployment or isolated target before production. Do not use
`kubectl delete pod` as the default SIGTERM mechanism.

```sh
# DO NOT RUN IN THIS PACKET.
# Future APOLLO rollout proof only after operator approval.
kubectl -n <namespace> rollout restart deployment/apollo

# DO NOT RUN IN THIS PACKET.
# Future ATHENA rollout proof only after operator approval.
kubectl -n <namespace> rollout restart deployment/athena

# DO NOT RUN IN THIS PACKET.
# Future direct SIGTERM proof only against an approved canary pod.
kubectl -n <namespace> exec <canary_pod> -- sh -c 'kill -TERM 1'
```

## Rollback Expectations

APOLLO rollback expectations before accepted mutation:

- Current image digest and deployment generation captured.
- DB backup or point-in-time restore strategy documented for APOLLO tables
  touched by the fixture.
- Fixture cleanup path documented, preferably through already-supported APIs.
- Rating/projector rebuild path documented if result lifecycle probes affect
  rating or public projection state.
- Rollback validation includes health, readiness, metrics, and fixture readback.

ATHENA rollback expectations before accepted mutation:

- Current image digest and deployment generation captured.
- DB backup, point-in-time restore, or replay strategy documented for
  observations, accepted presence, and projection state.
- Fixture rows are isolated by canary facility/node/account aliases.
- Recovery validation includes health, readiness, metrics, facility-scoped
  count/history/analytics, and ingress-bridge readback when available.
- Source-pass sessions remain source-pass-only unless a future accepted-session
  cutover packet explicitly changes that boundary.

If rollback cannot be described before execution, the probe is blocked.

## Split Execution Guidance

The later execution packet should not combine APOLLO and ATHENA unless both
services share the same disposable canary environment and rollback owner.

Preferred split:

1. APOLLO Live Mutation Execution Gate:
   rejection probes, idempotency/conflict probes, controlled competition/result
   or booking fixture mutation, and APOLLO SIGTERM proof.
2. ATHENA Live Ingress/SIGTERM Execution Gate:
   denied tap probe, accepted canary tap probe, restart/replay proof, and
   ATHENA SIGTERM proof.

Each execution gate must re-run Gate 0 and re-capture current deployed truth.

## Closeout Criteria For Later Execution

A later execution packet may claim a probe closed only when:

- The exact command was approved and executed.
- The fixture was disposable and isolated.
- Expected behavior matched observed behavior.
- Readback proof confirms bounded effect.
- Metrics and logs confirm recovery and no private leakage.
- Rollback was either unnecessary and justified, or ready/executed and
  verified.
- Evidence was redacted before commit.
- Deployed truth was updated only to the extent actually proven.

If a probe aborts, the closeout must say so directly and keep the residual risk
open.

## Packet 8 Closeout Truth

Packet 8 closes planning only.

No live destructive probe was executed. No deployed truth changed. No APOLLO or
ATHENA runtime code changed. No deploy/GitOps manifests changed. No Hestia or
Themis frontend files were touched.

Still deferred:

- actual live destructive probe execution
- actual in-flight production SIGTERM proof
- disposable fixture creation
- concrete APOLLO/ATHENA production DB backup and restore proof
- public tournament readiness
- OpenSkill read-path cutover
- public/member safety detail UI
- frontend/product widening
- messaging/chat
- broad social graph
- booking/commercial/proposal workflows

## Next Packet

Do not automatically execute this plan.

The next packet should be either:

- Live Destructive Probe Execution Gate, only with explicit user/operator
  approval, disposable target identities, and concrete rollback proof.
- A bounded feature-specific gate for co-presence, private daily presence, or
  reliability, if product substrate work is chosen instead.

Public Tournament Readiness and OpenSkill Read-Path Cutover remain blocked.
