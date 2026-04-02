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
| Feature | deploy and GitOps widening | deferred | local runtime, tests, and docs were sufficient to close this tracer without cluster changes | dedicated deploy or release pass |

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

Deferred after hardening:

| Type | Item | Status | Why It Was Not Done Here | Future Owner |
| --- | --- | --- | --- | --- |
| Deploy | live `ATHENA -> NATS -> APOLLO` cluster integration | deferred | Milestone 1 closes on the read-path deployment truth only; the event boundary needs its own bounded deployment workstream | Deployment Workstream A / Milestone 1.5 |
