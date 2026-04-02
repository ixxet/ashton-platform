# apollo

APOLLO is the member-intent service in ASHTON.

It owns member auth, profile state, visit history as member-facing context,
visit open/close lifecycle behavior, and the first derived lobby-eligibility
behavior. It does not own raw presence truth, workout runtime,
recommendations, or actual matchmaking behavior yet.

## Current Role

The active APOLLO slice now spans three narrow runtime boundaries:

- identified-arrival consume -> visit record
- identified-departure consume -> visit close
- student ID + email verification -> signed session cookie auth
- persisted `visibility_mode` / `availability_mode` -> derived lobby eligibility

That is enough to prove member ownership, state persistence, and the first real
intent-behavior slice without widening into workouts, recommendations, or lobby
membership.

## Current Real Slice

| Surface | Status | Notes |
| --- | --- | --- |
| `apollo serve` | Real | Starts health, auth, profile, eligibility, and optional NATS consumer paths |
| `GET /api/v1/health` | Real | Reports service health and consumer enablement |
| `POST /api/v1/auth/verification/start` | Real | Starts account verification or passwordless sign-in |
| `GET/POST /api/v1/auth/verify` | Real | Consumes a stored token and issues a signed session cookie |
| `POST /api/v1/auth/logout` | Real | Revokes the current session and clears the cookie |
| `GET /api/v1/profile` | Real | Authenticated profile read |
| `PATCH /api/v1/profile` | Real | Authenticated update for `visibility_mode` and `availability_mode` |
| `GET /api/v1/lobby/eligibility` | Real | Authenticated derived eligibility read from stored profile state only |
| `apollo visit list` | Real | Member-facing visit history readback |
| NATS identified-arrival/departure consumer | Real | Consumes `athena.identified_presence.arrived` and `athena.identified_presence.departed` using the shared helper |
| workout runtime | Deferred | Schema exists, runtime does not |
| recommendation runtime | Deferred | Still outside the active executable slice |
| lobby membership or matchmaking runtime | Deferred | Eligibility is read-only; no join, leave, or team formation path exists |

## Ownership Rules

| APOLLO owns | APOLLO does not own |
| --- | --- |
| member account ownership and session state | raw facility presence truth |
| profile visibility and availability state | occupancy counting |
| visit history as member context | staff workflows |
| derived lobby eligibility | open lobby membership, invites, or match formation |
| future workout and recommendation domains | shared contract authorship |

Key boundaries:

- auth is separate from claimed-tag linkage
- profile state is separate from visits
- visit history is separate from workout history
- eligibility is separate from lobby membership
- tap-in alone must never imply social intent

## Current Milestone Truth

- claimed tags map ATHENA identity hashes to APOLLO users
- visit ingest and visit closing stay strict, idempotent, and side-effect free
  outside visit rows
- sessions are server-side Postgres rows referenced by a signed cookie
- `discoverable + available_now` is eligible for the open lobby
- `unavailable`, `with_team`, and `ghost + available_now` are explicitly
  ineligible
- invalid stored intent enums resolve deterministically as ineligible reasons

## Project Shape

| Path | Purpose |
| --- | --- |
| `cmd/apollo/` | CLI entrypoint and serve command |
| `internal/auth/` | verification token lifecycle, sessions, and signed cookie logic |
| `internal/profile/` | authenticated member profile read and update |
| `internal/eligibility/` | derived lobby-eligibility logic |
| `internal/consumer/` | identified visit-lifecycle consume path |
| `internal/visits/` | visit service and repository boundary |
| `internal/store/` | sqlc-generated query bindings |
| `internal/server/` | HTTP handlers and auth middleware |
| `db/migrations/` | current schema for users, sessions, visits, workouts, ARES, and recommendations |
| `docs/` | roadmap, runbooks, ADRs, diagrams, and growing pains |

## Verification Standard

Treat APOLLO as trustworthy only when:

- full Go tests pass repeatedly
- auth/session-sensitive tests are rerun multiple times
- the binary builds
- migrations work on a fresh Postgres database
- the local smoke path covers auth, profile, and eligibility
- the identified arrival and departure boundary is exercised against real NATS
  and Postgres

## Deployment Boundary

APOLLO owns its runtime, schema, and consumer logic. Deployment and GitOps are
still outside this repo. If APOLLO is not deployed, deployment verification is
explicitly deferred rather than implied.

## Deferred On Purpose

- workout write flows
- recommendation and coaching runtime
- lobby membership persistence, invites, and match formation
- frontend or offline PWA work
- external identity provider widening
