# Tracer Matrix

This document keeps tracer chats, repo ownership, and exit criteria in one place.
Update it after every tracer so future chats do not have to rediscover the plan.

## Tracer 0: Bootstrap And Alignment

Status: completed in the control-plane thread.

Scope:

- source-of-truth layering
- docs alignment before code
- first contracts in `ashton-proto`
- first executable mock slice in `athena`
- first ATHENA GitOps scaffolding and `0.0.1` activation path

Key outputs:

- shared envelope and first protos
- ATHENA mock API, CLI, metric, tests, and image pipeline
- `v0.0.1` source tags across ASHTON repos

## Tracer 1: Presence Contract To ATHENA Read Path

Status: completed in the `codex/tracer-1-presence-read-path` implementation chat.

Goal:

- `ashton-proto` presence contract -> `athena` mock presence -> API/CLI/metric

Repos:

- `ashton-proto`
- `athena`
- `Prometheus` for app and ServiceMonitor wiring only when needed

Exit criteria:

- contracts stay narrow and versioned
- ATHENA read path remains stable
- tests cover count logic and read surfaces
- container smoke test passes

Key outputs:

- `ashton-proto` contracts moved under the Buf-clean `ashton/...` package layout
- generated contract code is reproducible and importable from Go
- `athena` now has one canonical occupancy read path shared by CLI, HTTP, and Prometheus
- occupancy edge cases are covered with real tests: empty data, unknown facility, negative clamp, multi-facility separation, and deterministic mock inputs
- local container smoke passed for `/api/v1/health`, `/api/v1/presence/count`, and `/metrics`
- Prometheus manifests already rendered cleanly, so no repo changes were required there

Environment note:

- live rollout was later verified against the real Talos cluster using the ATHENA `0.1.0` image pin
- Flux fetched the new Prometheus revision, but the broader apps and infra dependency chain remained unhealthy for unrelated reasons, so direct workload verification still mattered

Tracer 1 retrospective:

- This tracer carried more bootstrap friction than later tracers should. It was the first point where shared contracts, executable service behavior, image publishing, GitOps pinning, and live verification all had to become real at the same time.
- The first proto layout looked reasonable but failed Buf standard lint. Fixing the package path and RPC naming rules was part of turning `ashton-proto` from a placeholder into a reproducible contract repo.
- The first mock slice in `athena` was executable, but not yet durable. Deterministic fixtures, explicit config failures, and shared read-path semantics had to be added before the slice was trustworthy enough to widen.
- The first metrics path updated shared state from HTTP requests, which was too implicit. Moving Prometheus onto the same canonical occupancy read path as CLI and HTTP was a foundational correction.
- The first live rollout taught the difference between manifest render success and GitOps chain health. ATHENA itself rolled forward cleanly, but the wider Flux dependency chain still needs separate cleanup outside the tracer.
- Tracer 1's git history is denser than ideal because it absorbed the last mile from bootstrap to trustworthy baseline. Future tracers should keep the narrower, more granular commit style that became possible only after this foundation existed.

## Tracer 2: ATHENA Presence Event To APOLLO Visit Record

Status: completed in the `codex/tracer-2-closure-hardening` implementation chat.

Goal:

- `ATHENA` presence event -> `APOLLO` visit record

Repos:

- `ashton-proto`
- `athena`
- `apollo`

Exit criteria:

- APOLLO records visits without creating workouts
- event naming and linkage stay explicit
- one end-to-end visit flow is testable without real hardware

Key outputs:

- `ashton-proto` now ships one shared runtime helper for
  `athena.identified_presence.arrived`, including the subject constant,
  schema-backed marshal and parse paths, and reusable fixture bytes
- `athena` publishes identified arrivals through that shared helper and keeps
  the occupancy read path unchanged
- `apollo` consumes the same helper, rejects invalid source, type, enum, and
  timestamp values, and keeps anonymous, duplicate, and unknown-tag handling
  deterministic
- local manual smoke passed for `apollo serve`, `athena presence publish-identified`,
  and `athena serve` with the publisher worker against real NATS and Postgres

Tracer 2 retrospective:

- The first implementation pass proved behavior but still left the producer and
  consumer free to drift because both repos owned private event structs. The
  closure-hardening pass fixed that by making `ashton-proto` the runtime source
  of truth instead of just the documentation source of truth.
- JSON Schema alone was not sufficient runtime enforcement for the full event
  semantics in this stack. The shared helper had to parse timestamps and source
  values explicitly after schema validation.
- One-shot broker paths need a real smoke test, not just stub publishers. The
  first manual smoke found a no-deadline flush bug in ATHENA that still allowed
  delivery but reported failure. Closing the tracer required fixing the publish
  path and adding a regression test before updating control-plane docs.
- APOLLO still needed one narrow defensive anonymous no-op even though the
  shared identified-arrival contract requires a non-empty identity hash. The
  fix was to keep the main parse path strict and isolate the anonymous exception
  to the correct subject before strict parsing.

Deferred after closure:

No in-scope unresolved bugs remained at close. The remaining items are
intentional feature deferrals:

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Feature | visit closing and departure handling | deferred | Tracer 2 proves arrival -> visit creation only; it does not invent a close rule without a real departure slice | later visit-closing tracer |
| Feature | workout creation from visit events | deferred | visit history must remain separate from workout history | later workout tracer |
| Feature | APOLLO auth and profile state | deferred | this tracer uses tag-to-user linkage only and does not widen into auth | `Tracer 3` |
| Feature | availability, Ghost Mode, and lobby intent | deferred | presence must not be conflated with matchmaking intent | later APOLLO lobby tracer |
| Feature | HERMES, gateway, and deploy widening | deferred | not required to prove physical truth -> member visit history | dedicated staff, gateway, or deploy tracer |

## Tracer 3: APOLLO Member Auth To Profile State

Status: completed in the `codex/tracer-3-auth-profile-foundation`
implementation chat.

Goal:

- APOLLO member auth -> profile -> privacy and availability state

Repos:

- `apollo`

Exit criteria:

- student ID + email verification path is real
- signed session cookie flow exists
- profile state can persist `visibility_mode` and `availability_mode`
- tests cover auth boundary and invalid-state edges

Key outputs:

- `apollo` now stores hashed verification tokens with expiry and single-use
  semantics in Postgres instead of depending on external mail or OAuth
- `apollo` now issues signed `HTTPOnly`, `Secure`, `SameSite=Strict` cookies
  that reference server-side Postgres sessions instead of widening into JWT
  infrastructure
- `apollo` now exposes `POST /api/v1/auth/verification/start`,
  `GET/POST /api/v1/auth/verify`, `GET/PATCH /api/v1/profile`, and
  `POST /api/v1/auth/logout`
- `apollo` now persists `visibility_mode` and `availability_mode` through
  `users.preferences` while keeping tag linkage, visits, workouts, and
  matchmaking behavior separate
- automated coverage now includes token lifecycle, cookie tamper and expiry,
  authenticated profile round-trips, storage-level Postgres tests, and a local
  `apollo serve` smoke against a real Postgres container

Tracer 3 retrospective:

- The auth slice only stayed narrow because the implementation treated account
  ownership, tag linkage, and member state as separate concerns all the way
  through schema, services, and handlers. Reusing `users` while keeping tokens,
  sessions, and claimed tags separate was the right taste decision here.
- Server-side sessions were the practical fit for this tracer. They kept cookie
  handling simple, made logout and revocation real immediately, and avoided a
  premature JWT-first design before APOLLO has broader auth requirements.
- `users.preferences` was the right storage boundary for this tracer because
  only `visibility_mode` and `availability_mode` are real today. Splitting
  those into dedicated columns now would have created migration noise without
  improving the proof.
- The first integration test pass exposed an environment bug in the shared
  dockertest helper: it assumed `host.docker.internal` always resolved. Closing
  the tracer required adding a resolver-based fallback and a regression test
  before trusting the full Postgres-backed suite.
- The local smoke also exposed the real cost of keeping `Secure` cookies strict:
  plain HTTP cookie jars will not replay them. The correct response was to
  document the local smoke procedure, not weaken the runtime cookie policy.

Deferred after closure:

No in-scope unresolved bugs remained at close. The remaining items are
intentional feature deferrals:

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Feature | workout logging runtime | deferred | Tracer 3 proves account ownership and profile state, not training history writes | later workout tracer |
| Feature | lobby entry and Ghost Mode behavior | deferred | this tracer persists settings only; it does not make them drive social behavior | `Tracer 4` |
| Feature | recommendations and coaching runtime | deferred | auth and saved state had to become real before recommendation logic widens | later recommendation tracer |
| Feature | OAuth, SSO, or external identity provider work | deferred | first-party auth is sufficient for the current proof and avoids outside dependencies | later auth tracer if needed |
| Feature | deploy and GitOps widening | deferred | local runtime, tests, and docs were sufficient to close this tracer without cluster changes | dedicated deploy or release pass |

## Tracer 4: Explicit Lobby Eligibility

Status: completed in the implementation chat.

Goal:

- APOLLO lobby eligibility from explicit availability, not tap-in

Repos:

- `apollo`
- `ashton-proto` only if a contract change is truly needed

Exit criteria:

- presence alone never creates matchmaking intent
- explicit availability gates lobby entry
- ghost mode and with-team mode remain distinct

Key outputs:

- `apollo` now exposes authenticated `GET /api/v1/lobby/eligibility` with
  stable `eligible`, `reason`, `visibility_mode`, and `availability_mode`
  fields
- eligibility now derives from persisted member state only:
  `discoverable + available_now` is eligible, while `unavailable`,
  `with_team`, and `ghost + available_now` are all ineligible for the open
  lobby
- invalid persisted eligibility enums now resolve deterministically as explicit
  ineligible reasons instead of being silently treated as lobby intent
- APOLLO test coverage now proves session enforcement, state transitions,
  invalid-state handling, side-effect-free repeated reads, and that visit
  history does not create eligibility by itself

Tracer 4 retrospective:

- This tracer only stayed narrow because it treated lobby eligibility as a
  derived read, not as the start of a lobby-write subsystem. That avoided
  inventing queue state, membership persistence, or match formation too early.
- Reusing the profile read path directly would have hidden corrupted stored enum
  values behind safe display defaults. Closing the tracer required making
  invalid persisted member intent visible as deterministic ineligible reasons.
- The proof only counted once the tests covered the negative case that matters
  most to the architecture: a real visit row must still leave an unavailable
  member out of the lobby. That regression guard is the tracer's core safety
  boundary.

Deferred after closure:

No in-scope unresolved bugs remained at close. The remaining items are
intentional feature deferrals:

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Feature | lobby membership persistence | deferred | the tracer proves derived eligibility only and deliberately avoids join or leave state | later lobby tracer |
| Feature | invitations, notifications, and social workflow | deferred | eligibility must exist before widening into social coordination | later social tracer |
| Feature | match formation and ranking | deferred | this tracer is not ARES or matchmaking logic | later ARES tracer |
| Feature | workout runtime and recommendations | deferred | member intent is still separate from workouts and coaching | later workout and recommendation tracers |

## Tracer 5: Visit Closing / Departure Flow

Status: completed in the implementation chat.

Goal:

- ATHENA departure or visit-closing truth -> APOLLO closes the correct open visit

Repos:

- `ashton-proto`
- `athena`
- `apollo`

Exit criteria:

- departures close visits deterministically by the shared contract path
- no-open and duplicate departure handling stay explicit
- visit lifecycle remains separate from workouts, eligibility, and matchmaking
- existing arrival behavior stays stable

Key outputs:

- `ashton-proto` now ships shared schema and runtime helpers for
  `athena.identified_presence.departed` alongside the existing arrival contract
- `athena` now publishes identified departures through the shared helper, keeps
  the arrival path stable, and can one-shot publish departures locally through
  `athena presence publish-identified-departures`
- `apollo` now closes the matching open visit for the same member and facility,
  persists `departure_source_event_id` for idempotency, and keeps no-open,
  duplicate, anonymous, unknown-tag, and out-of-order departure behavior
  deterministic
- local manual smoke passed for `apollo serve`, `athena presence publish-identified`,
  `athena presence publish-identified-departures`, duplicate departure replay,
  and zero implicit workouts against real NATS and Postgres

Tracer 5 retrospective:

- The visit lifecycle only stayed trustworthy because departure matching stayed
  exact: APOLLO closes by member plus facility and does not guess across
  facilities or closed history.
- A separate `departure_source_event_id` was required to make close replays as
  deterministic as arrival replays. Reusing the arrival idempotency field would
  have collapsed two different event phases into one key.
- Out-of-order handling had to stay explicit. A departure timestamp older than
  the open visit arrival is treated as a non-mutating lifecycle error, not as a
  reason to backdate or corrupt visit history.
- The local end-to-end proof only counted once the same smoke run showed both
  that the visit closed and that workouts still stayed at zero. Closing a visit
  must not invent training semantics.

Deferred after closure:

No in-scope unresolved bugs remained at close. The remaining items are
intentional feature deferrals. The deployment deferral later closed in
Milestone 1.5 below.

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Feature | workout completion or workout creation from departure | deferred | visit lifecycle must remain separate from workout history | later workout tracer |
| Feature | lobby exit, availability changes, or eligibility changes from departure | deferred | physical departure must not imply social or intent-state mutation | later lobby or intent tracer |
| Feature | broader presence refactors or real-time presence layers | deferred | the tracer only proves visit close behavior, not a full presence subsystem | later presence tracer if needed |

## Tracer 6: APOLLO Workout Runtime

Status: closure-clean after the closure-hardening pass.

Goal:

- APOLLO workout runtime with explicit authenticated create, update, finish,
  and history reads

Repos:

- `apollo`

Exit criteria:

- workout records are created, updated, finished, and read through
  authenticated APOLLO surfaces
- ownership and state transitions stay deterministic
- workouts remain separate from visits, eligibility, and profile intent
- local smoke proves the real auth/session-backed runtime against Postgres

Key outputs:

- `apollo` now exposes authenticated `POST /api/v1/workouts`,
  `GET /api/v1/workouts`, `GET /api/v1/workouts/{id}`,
  `PUT /api/v1/workouts/{id}`, and
  `POST /api/v1/workouts/{id}/finish`
- APOLLO now persists member-owned workout sessions and ordered exercise rows
  in Postgres with explicit `in_progress` and `finished` states
- APOLLO now enforces one `in_progress` workout per member, owner-scoped reads
  and writes, non-empty finish rules, and immutable finished workouts
- workout history now has an explicit stable rule: newest workout created first
  using DB-owned `started_at DESC, id DESC`
- local manual smoke passed for the real `apollo serve` auth flow, workout
  create, workout update, workout finish, workout readback, and side-effect
  checks against disposable Postgres

Tracer 6 retrospective:

- This tracer only stayed trustworthy because it treated workouts as explicit
  member-owned runtime records instead of trying to infer exercise activity from
  arrivals, departures, or visit history.
- One `in_progress` workout per member was the simplest durable state-machine
  rule that made ownership and finish behavior testable without inventing a
  full training ontology.
- The first implementation pass looked green in handler and server tests but
  still failed under the real `apollo serve` entrypoint because the runtime
  dependency assembly omitted the workout service. Closing the tracer required a
  manual smoke, a serve-path fix, and a regression test around dependency
  assembly before the docs could be trusted.
- The local proof only counted once the same smoke run showed both that the
  workout finished correctly and that visits, claimed tags, and member
  preferences remained unchanged.
- The first closeout still overclaimed workout-history ordering. Closure only
  became clean after hardening changed the list rule to newest workout created
  first, removed mixed app-clock and DB-clock ordering, and added regression
  coverage for skew and tie-break behavior.

Deferred after closure:

No in-scope unresolved bugs remained at close. The remaining items are
intentional feature deferrals:

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Deploy | live in-cluster workout runtime proof | deferred | this tracer proves workout runtime locally and deliberately does not widen the Milestone 1.5 deployment claim | later APOLLO deployment workstream |
| Feature | workout inference from visits or departure | deferred | workouts must stay explicit member-owned records, not physical-truth side effects | later only if product rules change |
| Feature | recommendations, coaching, and generated plans | deferred | explicit workout history had to become real before higher-level training logic widens | later recommendation or coaching tracer |
| Feature | movement ontology or shared exercise catalog | deferred | free-text exercise rows are sufficient for the first workout-runtime slice | later workout modeling tracer |

## Tracer 7: APOLLO Deterministic Recommendation Runtime

Status: completed in the implementation chat.

Goal:

- APOLLO serves one deterministic member-scoped workout recommendation derived
  from explicit workout history only

Repos:

- `apollo`

Exit criteria:

- authenticated recommendation read is real
- precedence is explicit and deterministic
- recommendation reads stay side-effect free
- recommendations remain separate from visits, eligibility, and matchmaking
- local smoke proves the real auth/session-backed recommendation runtime

Key outputs:

- `apollo` now exposes authenticated `GET /api/v1/recommendations/workout`
- APOLLO now derives one deterministic recommendation from explicit workout
  history with this precedence:
  `resume_in_progress_workout`, `start_first_workout`, `recovery_day` for a
  workout finished within `24h`, then `repeat_last_finished_workout`
- recommendation responses are structured and member-scoped with stable
  `type`, `reason`, `evidence`, `workout_id`, and `generated_at` fields
- recommendation reads remain side-effect free and do not create, update, or
  finish workouts and do not mutate visits, preferences, claimed tags, or
  eligibility state
- local manual smoke passed for the real `apollo serve` auth flow, cold-start,
  in-progress, recovery-window, aged-finished, and side-effect checks against
  disposable Postgres

Tracer 7 retrospective:

- This tracer only stayed trustworthy because it treated recommendations as a
  derived read over explicit APOLLO-owned workout data instead of widening into
  generated plans, freeform coaching text, or visit-derived inference.
- The first executable slice needed one explicit precedence order. Leaving the
  recommendation types unordered would have hidden product behavior behind
  handler wiring and made edge cases untestable.
- The authored `apollo.recommendations` table did not need to become active
  runtime state yet. Closure stayed clean by keeping Tracer 7 as a read-time
  derivation and deferring persistence until a later tracer proves a real need.
- The smoke path found two apparent failures that were actually operator races:
  recommendation reads were run in parallel with finish and manual aging
  commands. Closing the tracer required rerunning those checks sequentially and
  documenting the exact smoke order instead of weakening the product rule.

Deferred after closure:

No in-scope unresolved bugs remained at close. The remaining items are
intentional feature deferrals:

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Deploy | live in-cluster recommendation runtime proof | deferred | this tracer proves recommendation runtime locally and deliberately does not widen Milestone 1.5 deployment truth | later APOLLO deployment workstream |
| Feature | recommendation persistence | deferred | the first recommendation slice proves deterministic read behavior without needing stored outputs | later recommendation tracer |
| Feature | generated plans, LLM coaching, and broader program building | deferred | Tracer 7 is a deterministic coaching read, not an AI planning system | later coaching or planner tracer |
| Feature | recommendation inference from visits, departures, or profile state | deferred | recommendation intent must stay grounded in explicit workout data only | later only if product rules change |

## Tracer 8: HERMES Read-Only Staff Occupancy Path

Status: completed in the implementation chat.

Goal:

- HERMES answers one bounded staff operational question from ATHENA public
  truth without write authority

Repos:

- `hermes`

Exit criteria:

- one real read-only staff flow exists
- the answer is sourced from upstream public service truth
- error handling is explicit for missing input and unavailable or malformed
  upstream responses
- no write behavior or private truth ownership is introduced
- local smoke proves the real HERMES runtime against a real ATHENA runtime

Key outputs:

- `hermes` now exposes `hermes ask occupancy --facility <id>` with `json` and
  `text` output modes
- HERMES now consumes ATHENA's public
  `GET /api/v1/presence/count?facility=<id>` surface through a strict upstream
  client instead of reading private storage or inventing its own occupancy
  model
- HERMES responses are structured and source-backed with stable
  `facility_id`, `current_count`, `observed_at`, `source_service`, and
  optional `notes` fields
- missing facility input, invalid ATHENA base URLs or timeout overrides,
  upstream non-200s, malformed JSON, invalid timestamps, and unavailable
  upstream connections all fail clearly
- local manual smoke passed for `athena serve`, successful HERMES occupancy
  reads, an unknown-facility zero-count read, and an unavailable-upstream
  failure against the real CLI runtime

Tracer 8 retrospective:

- This tracer only stayed trustworthy because it narrowed the staff question to
  occupancy count instead of overpromising identity-level answers that ATHENA's
  public read path does not expose.
- HERMES stayed architecture-safe by using ATHENA's public occupancy endpoint
  directly. Pulling from private storage or inventing a staff-side occupancy
  model would have broken the platform ownership rules on the first slice.
- The first CLI pass exposed a small but real config bug: negative timeout
  overrides were silently ignored instead of being rejected. Closing the tracer
  required making timeout overrides explicit and adding regression coverage
  before trusting the runtime.
- The smoke path also exposed two shell mistakes that could have been mistaken
  for runtime bugs: unquoted `?facility=` URLs under `zsh`, and reusing the
  read-only `status` variable name in shell wrappers. The correct response was
  to fix the operator commands and record the exact verified sequence in the
  runbook, not to weaken the product boundary.
- The hardening audit output looked noisier than the happy-path proof because
  it intentionally exercised missing-input, invalid-config, malformed-upstream,
  and unavailable-upstream cases. Those failures are part of the tracer's
  proof that HERMES fails clearly instead of fabricating answers.

Deferred after closure:

No in-scope unresolved bugs remained at close. The remaining items are
intentional feature or deployment deferrals:

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Deploy | live in-cluster HERMES runtime proof | deferred | this tracer proves the HERMES read path locally and deliberately does not widen Milestone 1.5 deployment truth | later HERMES deployment workstream |
| Ops | low-noise structured HERMES request and result logs | deferred | the first slice is inspectable through CLI output and runbook commands, but richer operational observability is still thin | later HERMES hardening or widening tracer |
| Feature | identity-level facility roster answers | deferred | ATHENA's public occupancy surface proves count truth, not current-member identity truth | later tracer only if a stable public upstream surface exists |
| Feature | write actions, approvals, and workflow execution | deferred | Tracer 8 is read-only by design and does not justify operational write authority yet | later HERMES action tracer |
| Feature | gateway, chat UX, and agent orchestration | deferred | the first HERMES proof is a narrow executable CLI, not a broad assistant runtime | later gateway or assistant tracer |

## Tracer 9: Gateway First Routed Read-Only Tool Call

Status: completed in the implementation chat.

Goal:

- `gateway -> manifest -> ATHENA occupancy read -> structured result`

Repos:

- `ashton-mcp-gateway`
- `ashton-proto`
- `athena` only as the upstream public surface; no repo changes required

Exit criteria:

- one real gateway runtime exists
- one real manifest exists
- one real routed ATHENA occupancy call works
- logs make the path inspectable
- the slice stays read-only and source-backed

Key outputs:

- `ashton-proto` now ships one real manifest:
  `mcp/athena.get_current_occupancy.json`
- `ashton-mcp-gateway` now loads manifests from disk and exposes:
  - `GET /health`
  - `POST /mcp/v1/tools/list`
  - `POST /mcp/v1/tools/call`
- `ashton-mcp-gateway` now routes `athena.get_current_occupancy` to ATHENA's
  public `GET /api/v1/presence/count` surface only
- routed results are structured and source-backed:
  `facility_id`, `current_count`, `observed_at`, `source_service`, `latency_ms`
- routed success and upstream failure now emit inspectable logs with tool,
  source, facility, latency, and outcome
- local smoke passed with real ATHENA and gateway runtimes for both success and
  unavailable-upstream failure

Tracer 9 retrospective:

- The first gateway line only stayed honest because it refused to become a
  generic future control plane. One manifest, one route, and one log path were
  enough to prove discovery and routing.
- The manifest needed to stay service-owned. Putting the first real tool
  definition in `ashton-proto/mcp` kept the gateway from hardcoding a private
  route catalog.
- The first route had to go directly to ATHENA, not through HERMES, so the
  routing boundary stayed small enough to debug.
- The first smoke left a repo-root build artifact behind. Closing the tracer
  required fixing `.gitignore` so local verification did not pollute repo
  state.

Deferred after closure:

No in-scope unresolved bugs remained at close. The remaining items are
intentional feature or deployment deferrals:

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Feature | caller identity and trust policy | deferred | the first route proves discovery and routing before auth complexity | later gateway tracer |
| Feature | persisted audit storage | deferred | inspectable logs are sufficient for the first read-only route | later gateway tracer |
| Feature | write approvals and HITL | deferred | Tracer 9 is read-only and should not invent write governance early | later gateway tracer |
| Feature | HERMES or APOLLO routing | deferred | one ATHENA route is enough to prove the pattern | later gateway tracer |
| Deploy | live gateway deployment proof | deferred | Tracer 9 proves local runtime only | later deployment workstream |

## Tracer 10: First Source-Backed ATHENA Ingress Adapter

Status: completed in the implementation chat.

Goal:

- `csv export -> ATHENA adapter -> canonical occupancy read path`

Repos:

- `athena`
- `ashton-platform`

Exit criteria:

- one real source-backed ATHENA adapter exists
- the adapter feeds the existing occupancy read path honestly
- the public occupancy read surface stays stable
- error handling is explicit and inspectable
- the slice stays physical-truth only

Key outputs:

- `athena` now supports `ATHENA_ADAPTER=csv` with `ATHENA_CSV_PATH`
- the first source-backed adapter consumes a bounded CSV presence-event export
  with required columns:
  - `event_id`
  - `facility_id`
  - `direction`
  - `recorded_at`
- optional columns `zone_id` and `external_identity_hash` remain narrow and
  physical-truth only
- parsed CSV events are normalized deterministically by `recorded_at` and
  `event_id`
- duplicate `event_id` rows are rejected explicitly
- the existing `GET /api/v1/presence/count` surface now reflects CSV-fed state
  without changing its response shape
- the adapter logs startup, refresh success, and refresh failure clearly
- local smoke passed for:
  - successful CSV-backed startup
  - repeated stable occupancy reads
  - explicit runtime failure after corrupting the live source file
  - startup failure with a missing CSV source file
- identified publish code remained unchanged and its test suite stayed green

Tracer 10 retrospective:

- The first real ingress line only stayed narrow because it chose a bounded
  physical-truth event export instead of inventing a broader connector model.
- Reusing the canonical occupancy path mattered more than designing a new
  storage layer. Tracer 10 proved the ingress edge without widening the rest of
  ATHENA.
- The first source-backed adapter needed adapter-agnostic default facility
  config. Keeping read defaults tied to mock-only settings would have made the
  runtime harder to operate honestly once more than one adapter existed.

Deferred after closure:

No in-scope unresolved bugs remained at close. The remaining items are
intentional feature or deployment deferrals:

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Deploy | live source-backed ATHENA ingress proof in-cluster | deferred | Tracer 10 proves the adapter locally and deliberately does not widen the deployment claim | later bounded deployment workstream |
| Feature | additional ingress adapters beyond CSV | deferred | one real source-backed ingress line is enough to prove the edge | later ATHENA tracer |
| Feature | persistence-backed ingress state | deferred | the existing occupancy model was sufficient for the first source-backed proof | later ATHENA tracer |
| Feature | prediction or capacity modeling | deferred | ingress had to become more real before forecasting logic widens | later ATHENA tracer |
| Feature | member, social, or recommendation inference | deferred | ATHENA still owns physical truth only | never in ATHENA without an explicit product change |

## Chat Model

- this control-plane chat stays the source-of-truth and arbitration thread
- each real tracer gets a fresh implementation chat
- research stays in a separate research chat
- tracer chats can touch multiple repos only when the tracer itself crosses those repo boundaries

## Milestone 1 Hardening Closeout

Status: completed on Option A.

Deployment truth for Milestone 1 is intentionally narrow:

- live cluster verification covers ATHENA's read-path slice
- Flux reconciliation, rollout status, live pod image, live health, live
  occupancy count, and live metrics were all verified for ATHENA
- the live cluster does not yet claim the full `ATHENA -> NATS -> APOLLO`
  boundary

That deferred boundary was later closed in Milestone 1.5 below.

## Milestone 1.5: Deployment Workstream A

Status: completed.

Deployment truth for Milestone 1.5 is bounded but real:

- live cluster verification now covers a narrow `ATHENA -> NATS -> APOLLO`
  arrival path
- `ATHENA` publishes identified arrivals to live in-cluster NATS
- `APOLLO` now runs in-cluster with migration bootstrap and live NATS consumer
  wiring
- one live identified arrival `mock-in-001` created the expected APOLLO visit
- replay of that same live identified arrival resolved as `duplicate` and did
  not create a second visit

Still intentionally deferred:

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Deploy | live departure-close proof in-cluster | deferred | the bounded workstream used the smallest safe end-to-end arrival path; live departure remains locally proven only | later bounded deployment workstream |
| Deploy | broad APOLLO product deployment | deferred | this workstream proves the visit-ingest boundary only and does not widen APOLLO into auth, eligibility, workouts, or broader product runtime | later APOLLO ops workstream |
