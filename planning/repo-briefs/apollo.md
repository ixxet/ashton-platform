# apollo

APOLLO is the member-intent service in ASHTON.

It owns member auth, profile state, visit history as member-facing context,
visit open/close lifecycle behavior, the first derived lobby-eligibility
behavior, the first explicit workout-history runtime, and the first
deterministic recommendation read. It does not own raw presence truth,
generated planning, or actual matchmaking behavior.

## Current Role

The active APOLLO slice now spans six narrow runtime boundaries:

- identified-arrival consume -> visit record
- identified-departure consume -> visit close
- student ID + email verification -> signed session cookie auth
- persisted `visibility_mode` / `availability_mode` -> derived lobby eligibility
- authenticated workout create/update/finish/read -> member-owned workout history
- authenticated workout history -> deterministic recommendation read

That is enough to prove member ownership, state persistence, and the first real
intent-behavior, workout-history, and deterministic coaching slices without
widening into generated plans or lobby membership.

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
| `POST /api/v1/workouts` | Real | Authenticated create for one member-owned `in_progress` workout |
| `GET /api/v1/workouts` | Real | Authenticated history read ordered by newest workout creation first (`started_at DESC, id DESC`) |
| `GET /api/v1/workouts/{id}` | Real | Authenticated owner-scoped workout detail read |
| `PUT /api/v1/workouts/{id}` | Real | Authenticated replacement of workout exercise rows while `in_progress` |
| `POST /api/v1/workouts/{id}/finish` | Real | Authenticated finish for a non-empty `in_progress` workout |
| `GET /api/v1/recommendations/workout` | Real | Authenticated deterministic coaching read derived from explicit workout history only |
| `apollo visit list` | Real | Member-facing visit history readback |
| NATS identified-arrival/departure consumer | Real | Consumes `athena.identified_presence.arrived` and `athena.identified_presence.departed` using the shared helper |
| recommendation storage | Schema authored | `apollo.recommendations` exists, but Tracer 7 recommendation reads are derived at read time |
| lobby membership or matchmaking runtime | Deferred | Eligibility is read-only; no join, leave, or team formation path exists |

## Ownership Rules

| APOLLO owns | APOLLO does not own |
| --- | --- |
| member account ownership and session state | raw facility presence truth |
| profile visibility and availability state | occupancy counting |
| visit history as member context | staff workflows |
| explicit workout history runtime | raw workout inference from visits |
| deterministic recommendation runtime | raw recommendation inference from visits or profile state |
| derived lobby eligibility | open lobby membership, invites, or match formation |
| future recommendation domains | shared contract authorship |

Key boundaries:

- auth is separate from claimed-tag linkage
- profile state is separate from visits
- visit history is separate from workout history
- workouts are explicit authenticated records, not inferred from visits
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
- workouts are member-owned, allow only one `in_progress` workout per member,
  and become immutable once finished
- workout history is ordered by newest workout creation first using DB-owned
  `started_at DESC, id DESC`
- workout writes do not mutate visits, claimed tags, or eligibility state
- recommendation precedence is explicit:
  `resume_in_progress_workout`, `start_first_workout`, `recovery_day` for
  workouts finished within `24h`, then `repeat_last_finished_workout`
- recommendation reads are side-effect free and do not mutate workouts, visits,
  preferences, claimed tags, or eligibility state

## Project Shape

| Path | Purpose |
| --- | --- |
| `cmd/apollo/` | CLI entrypoint and serve command |
| `internal/auth/` | verification token lifecycle, sessions, and signed cookie logic |
| `internal/profile/` | authenticated member profile read and update |
| `internal/eligibility/` | derived lobby-eligibility logic |
| `internal/consumer/` | identified visit-lifecycle consume path |
| `internal/visits/` | visit service and repository boundary |
| `internal/workouts/` | workout service and repository boundary |
| `internal/recommendations/` | deterministic recommendation service and repository boundary |
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
- the local smoke path covers auth, profile, eligibility, workout runtime, and
  deterministic recommendation reads
- the identified arrival and departure boundary is exercised against real NATS
  and Postgres

## Deployment Boundary

APOLLO owns its runtime, schema, and consumer logic. Milestone 1.5 proves one
bounded in-cluster APOLLO slice for arrival ingest. That still does not imply a
broad APOLLO product deployment; auth, eligibility, workout runtime, and
recommendation runtime remain locally proven only unless a separate deployment
workstream verifies them live.

## Deferred On Purpose

- recommendation persistence and generated planning
- lobby membership persistence, invites, and match formation
- frontend or offline PWA work
- external identity provider widening
