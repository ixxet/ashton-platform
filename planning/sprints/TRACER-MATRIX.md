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

## Tracer 3: APOLLO Member Auth To Profile State

Goal:

- APOLLO member auth -> profile -> privacy and availability state

Repos:

- `apollo`

Exit criteria:

- student ID + email verification path is real
- signed session cookie flow exists
- profile state can persist `visibility_mode` and `availability_mode`
- tests cover auth boundary and invalid-state edges

## Tracer 4: Explicit Lobby Eligibility

Goal:

- APOLLO lobby eligibility from explicit availability, not tap-in

Repos:

- `apollo`
- `ashton-proto` only if a contract change is truly needed

Exit criteria:

- presence alone never creates matchmaking intent
- explicit availability gates lobby entry
- ghost mode and with-team mode remain distinct

## Chat Model

- this control-plane chat stays the source-of-truth and arbitration thread
- each real tracer gets a fresh implementation chat
- research stays in a separate research chat
- tracer chats can touch multiple repos only when the tracer itself crosses those repo boundaries
