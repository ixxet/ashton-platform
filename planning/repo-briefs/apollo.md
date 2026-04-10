# apollo

APOLLO is the member-intent service in ASHTON.

It owns member auth, profile state, visit history as member-facing context,
visit open/close lifecycle behavior, the first derived lobby-eligibility
behavior, explicit lobby membership as member intent, the first explicit
workout-history runtime, the first deterministic recommendation read, and one
thin member web shell over those same APIs. Tracer 19 now adds a separate
bounded competition substrate slice: sport registry truth, facility-sport
capability mapping, and basic sport rules/config for badminton and basketball.
It now also owns one bounded competition execution-runtime slice: APOLLO-owned
queue state, deterministic assignment, and explicit session lifecycle control
over the Tracer 20 container model. Tracer 23 now adds bounded planner /
exercise-library / template-loadout / richer-profile truth on `main`, still
backend-first and authenticated/internal-only. It does not own raw presence
truth, staff workflows, or public competition surfaces, and it does not widen
into meaningful frontend, coaching, or nutrition logic.

## Current Role

The active APOLLO slice now spans twelve narrow runtime boundaries:

- identified-arrival consume -> visit record
- identified-departure consume -> visit close
- student ID + email verification -> signed session cookie auth
- persisted `visibility_mode` / `availability_mode` -> derived lobby eligibility
- explicit member action -> durable lobby membership join/leave state
- authenticated workout create/update/finish/read -> member-owned workout history
- authenticated workout history -> deterministic recommendation read
- authenticated member shell routes -> existing auth/profile/membership/workout/recommendation APIs
- CLI-only sport registry + facility-sport capability + static rules/config -> bounded competition substrate truth
- authenticated internal HTTP competition containers -> session/team/roster/match substrate truth
- authenticated internal HTTP queue/assignment/start/archive routes -> bounded competition execution truth
- authenticated planner/catalog/template/week routes -> bounded planner substrate truth

That is enough to prove member ownership, state persistence, and the first real
intent-behavior, explicit lobby-membership, workout-history, deterministic
coaching, and planner substrate slices plus one bounded competition execution
line without widening into generated plans, results, or public competition
reads.

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
| `GET /api/v1/lobby/membership` | Real | Authenticated explicit membership read with deterministic `not_joined` default |
| `POST /api/v1/lobby/membership/join` | Real | Authenticated explicit join gated by eligibility |
| `POST /api/v1/lobby/membership/leave` | Real | Authenticated explicit leave from joined state |
| `POST /api/v1/workouts` | Real | Authenticated create for one member-owned `in_progress` workout |
| `GET /api/v1/workouts` | Real | Authenticated history read ordered by newest workout creation first (`started_at DESC, id DESC`) |
| `GET /api/v1/workouts/{id}` | Real | Authenticated owner-scoped workout detail read |
| `PUT /api/v1/workouts/{id}` | Real | Authenticated replacement of workout exercise rows while `in_progress` |
| `POST /api/v1/workouts/{id}/finish` | Real | Authenticated finish for a non-empty `in_progress` workout |
| `GET /api/v1/recommendations/workout` | Real | Authenticated deterministic coaching read derived from explicit workout history only |
| planner catalog / templates / weeks routes | Real | Authenticated internal planner substrate reads and writes over exercise, template, and weekly plan truth |
| `GET /` | Real | Session-aware redirect into the member shell |
| `GET /app/login` | Real | Public HTML verification bootstrap page |
| `GET /app` | Real | Protected minimal member shell over the existing APOLLO APIs |
| `apollo visit list` | Real | Member-facing visit history readback |
| NATS identified-arrival/departure consumer | Real | Consumes `athena.identified_presence.arrived` and `athena.identified_presence.departed` using the shared helper |
| `GET/POST /api/v1/competition/sessions` plus session detail | Real | Authenticated owner-scoped session reads and writes over APOLLO-owned competition containers |
| `POST /api/v1/competition/sessions/{id}/queue/open`, queue join/remove, assignment, and `start` | Shipped | Authenticated owner-scoped queue/assignment/lifecycle runtime over explicit lobby membership plus current eligibility |
| recommendation storage | Schema authored | `apollo.recommendations` exists, but Tracer 7 recommendation reads are derived at read time |
| lobby membership runtime | Real | Explicit join and leave are real durable member intent only |
| results/history/public competition runtime | Current Tracer 23 repo/runtime line on `main` | Tracer 22 competition-history truth and Tracer 23 planner/profile substrate are both real while public competition reads remain deferred |

## Ownership Rules

| APOLLO owns | APOLLO does not own |
| --- | --- |
| member account ownership and session state | raw facility presence truth |
| profile visibility and availability state | occupancy counting |
| visit history as member context | staff workflows |
| explicit workout history runtime | raw workout inference from visits |
| deterministic recommendation runtime | raw recommendation inference from visits or profile state |
| derived lobby eligibility, explicit lobby membership, bounded competition execution runtime, and bounded competition-history truth | invites, parties, public competition reads, or broad social product surfaces |
| bounded planner substrate, exercise-library truth, equipment refs, templates/loadouts, and richer profile-input truth | raw workout inference, coaching auto-magic, or public competition reads |
| future recommendation domains | shared contract authorship |

Key boundaries:

- auth is separate from claimed-tag linkage
- profile state is separate from visits
- visit history is separate from workout history
- workouts are explicit authenticated records, not inferred from visits
- eligibility is separate from lobby membership
- lobby membership is explicit and durable, not inferred from eligibility
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
- lobby membership reads default to `not_joined` when no row exists
- eligible members can join explicitly, joined members can leave explicitly, and
  repeated transitions stay deterministic
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
- membership transitions do not mutate visits, workouts, claimed tags, or
  recommendation state
- planner/template/profile writes do not mutate visits, workouts,
  recommendations, claimed tags, membership, or ARES preview state
- the first member shell is local repo truth only and stays thin over the
  existing authenticated APIs, now including one narrow lobby-membership panel
- Tracer 19 is now shipped: APOLLO owns badminton and basketball sport
  registry truth, facility-sport capability mapping, and static sport
  rules/config through a CLI-only surface
- Tracer 20 is now shipped: APOLLO owns authenticated internal HTTP
  competition session, team, roster, and match container truth through
  dedicated session-rooted tables
- the current Tracer 21 line now adds authenticated internal
  HTTP queue state, deterministic assignment into those containers, and
  explicit session lifecycle transitions
- the current Tracer 22 line now adds immutable result capture, real
  `completed` lifecycle truth, sport-and-mode-separated ratings, session-scoped
  standings, and self-scoped member stats over the settled Tracer 21 execution
  substrate
- session-wide roster exclusivity remains schema-backed, `ashton-proto`
  remains untouched, and deployed truth stays unchanged
- the current Tracer 23 line now adds planner weeks, planner sessions, planner
  session items, exercise and equipment refs, templates/loadouts, and typed
  `coaching_profile` inputs over the settled workout and competition runtime
  substrates
- `ashton-proto` remains untouched because no shared-contract blocker was
  proven, and deployed truth stays unchanged

## Project Shape

| Path | Purpose |
| --- | --- |
| `cmd/apollo/` | CLI entrypoint and serve command |
| `internal/auth/` | verification token lifecycle, sessions, and signed cookie logic |
| `internal/profile/` | authenticated member profile read and update |
| `internal/eligibility/` | derived lobby-eligibility logic |
| `internal/membership/` | explicit lobby-membership repository and service |
| `internal/consumer/` | identified visit-lifecycle consume path |
| `internal/visits/` | visit service and repository boundary |
| `internal/workouts/` | workout service and repository boundary |
| `internal/recommendations/` | deterministic recommendation service and repository boundary |
| `internal/exercises/` | exercise-library and equipment-ref service and repository boundary |
| `internal/planner/` | planner, template, and weekly-substrate service and repository boundary |
| `internal/competition/` | competition queue, assignment, lifecycle, session, team, roster, and match container repository/service boundary |
| `internal/sports/` | CLI-only sport registry, facility-sport capability, and static rules/config boundary |
| `internal/server/web/` | embedded member-shell templates, assets, and browser-side tests |
| `internal/store/` | sqlc-generated query bindings |
| `internal/server/` | HTTP handlers, auth middleware, and embedded member-shell wiring |
| `db/migrations/` | current schema for users, sessions, visits, lobby membership, workouts, ARES, recommendations, sport substrate truth, and competition execution/runtime tables |
| `docs/` | roadmap, runbooks, ADRs, diagrams, and growing pains |

## Verification Standard

Treat APOLLO as trustworthy only when:

- full Go tests pass repeatedly
- auth/session-sensitive tests are rerun multiple times
- the binary builds
- migrations work on a fresh Postgres database
- the local smoke path covers auth, profile, eligibility, workout runtime,
  explicit lobby membership, deterministic recommendation reads, the thin
  member shell, authenticated competition session/team/roster/match
  container plus queue/assignment/lifecycle/history reads and writes, and the
  bounded planner/template/profile substrate
- the sport substrate is deterministic, bounded, and separate from matchmaking,
  public competition reads, and public sports surfaces
- competition roster exclusivity is schema-backed at the session level and
  repeated session detail reads stay deterministic
- the identified arrival and departure boundary is exercised against real NATS
  and Postgres

## Deployment Boundary

APOLLO owns its runtime, schema, and consumer logic. Milestone 1.6 proves one
bounded in-cluster APOLLO slice for departure-close truth. That still does not
imply a broad APOLLO product deployment; auth, eligibility, workout runtime,
recommendation runtime, the member shell, the competition execution runtime
plus competition-history runtime, and the planner/template/profile substrate
remain locally proven only unless a separate deployment workstream verifies
them live.

## Versioning Discipline

- `PATCH` lines cover hardening, docs sync, deployment closeout, observability,
  and bounded non-widening fixes
- `MINOR` lines cover new bounded member capabilities or intentional contract
  changes

## Next Ladder

| Line | Focus | Hard stop |
| --- | --- | --- |
| `v0.10.0` / `Tracer 19` | sport registry, facility-sport capability mapping, and basic sport rules/config for at least two sports | do not widen into matchmaking runtime yet |
| `v0.11.0` / `Tracer 20` | team, roster, session, and match container primitives | do not widen into public standings |
| `v0.12.0` / `Tracer 21` | matchmaking / queue / assignment flow and session lifecycle | do not widen into rivalry or badge logic |
| `v0.13.0` / `Tracer 22` | result capture, ratings, rudimentary standings, and member profile stats | do not widen into a broad public social layer |
| `v0.14.0` / `Tracer 23` | planner, exercise library, templates / loadouts, and richer profile inputs as backend/CLI-first truth | do not widen into meaningful frontend work yet |
| `v0.15.0` / `Tracer 24` | conservative deterministic fitness coaching plus calorie / macro ranges and low-friction meal logging | do not widen into diagnosis or opaque black-box logic |
| `v0.16.0` / `Tracer 25` | explanation, summarization, and thin agent-facing helper surfaces | do not let explanation become the core engine |

## Deferred On Purpose

- broad frontend widening beyond the thin member shell, including offline or PWA work
- public social/feed surfaces, DMs, and achievement showcase work
- external identity provider widening
