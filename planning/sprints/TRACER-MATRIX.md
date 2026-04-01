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

- live rollout verification remains blocked by missing local kube context, not by manifest render failures

## Tracer 2: ATHENA Presence Event To APOLLO Visit Record

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
