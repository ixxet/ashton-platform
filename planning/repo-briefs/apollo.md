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
over the Tracer 20 container model. Tracer 23 adds bounded planner /
exercise-library / template-loadout / richer-profile truth on `main`, Tracer 24
adds bounded deterministic coaching truth on the tagged `v0.15.0` line, and
`v0.15.1` remains the narrow post-closeout hardening patch line for that same
coaching substrate. Tracer 25 now also adds bounded conservative nutrition
truth on `main` through typed `nutrition_profile` inputs, owner-scoped
meal-template and meal-log truth, and conservative read-only nutrition
recommendation ranges over explicit profile/history truth, still backend-first
and authenticated/internal-only. Tracer 26 now also adds authenticated
internal helper reads, bounded `why` flows, and read-only coaching/nutrition
variation previews over those deterministic cores. Tracer 27 now also adds
authenticated facility-scoped presence reads, explicit tap-link truth per
member-visible visit, and facility-scoped streak state/events over explicit
linked visit days only. Tracer 28 now also adds explicit role/authz,
trusted-surface-gated competition staff mutations, and durable actor
attribution over that same competition control surface. It now also owns the
`Phase 3B.1` read-only ops overview over APOLLO schedule truth plus ATHENA
occupancy and bounded aggregate analytics truth, the `Phase 3B.4`
request-first booking runtime over APOLLO schedule/resource conflict truth, the
`Phase 3B.5` approved booking lifecycle over linked reservation truth, the
`Phase 3B.6` public request intake API with source/channel and idempotency
truth, the `Phase 3B.7` opaque public receipt/status/message lookup truth, and
the `Phase 3B.8` staff-side pending edit/approved replacement truth, and the
`Phase 3B.9` public-safe availability/request calendar read, and the
`Phase 3B.10` bounded staff schedule-control guardrails, and the
`Phase 3B.11` competition command/readiness/CLI foundation, `Phase 3B.12`
competition lifecycle/result trust, `Phase 3B.13` rating foundation,
`Phase 3B.14` OpenSkill dual-run comparison, `Phase 3B.15` ARES v2 proposal
foundation, `Phase 3B.16` competition analytics foundation, `Phase 3B.17`
internal tournament runtime, and `Phase 3B.18` social safety/reliability
foundation.
It does not own raw presence truth, broad staff product workflows, instant
booking, public self-edit/rebook, payments, quotes,
in-place approved mutation, broad schedule policy, OpenSkill read-path switch,
dashboard-first analytics, public profiles/stats/scouting, carry coefficient,
public tournaments, public competition surfaces, public/member safety UI,
messaging/chat, CP/badges/rivalry/squads, game identity, diagnosis, or opaque
helper-owned logic.

For APOLLO's post-current competition/rating/tournament/social expansion,
the operating source of truth is
[`apollo/docs/launch-expansion-audit.md`](../../../apollo/docs/launch-expansion-audit.md)
in the local ASHTON workspace. That audit governs future sequence and gates; it
does not change the shipped runtime truth summarized in this brief.

## Current Role

The active APOLLO slice now spans the following narrow runtime boundaries:

- identified-arrival consume -> visit record through the shared parser only
- identified-departure consume -> visit close through the shared parser only
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
- authenticated finished-workout effort feedback writes -> deterministic coaching input truth
- authenticated finished-workout recovery feedback writes -> deterministic coaching input truth
- authenticated coaching recommendation reads -> structured deterministic coaching proposal/explanation truth
- authenticated coaching helper reads -> thin agent/operator helper truth
- authenticated coaching `why` reads -> bounded deterministic explanation slices
- authenticated coaching variation reads -> read-only plan-diff preview variants
- authenticated profile nutrition writes -> typed non-clinical nutrition input truth
- authenticated meal-template routes -> owner-scoped reusable meal-template truth
- authenticated meal-log routes -> owner-scoped explicit nutrition-history truth
- authenticated nutrition recommendation reads -> conservative read-only nutrition range truth
- authenticated nutrition helper reads -> thin agent/operator helper truth
- authenticated nutrition `why` reads -> bounded deterministic explanation slices
- authenticated nutrition variation reads -> read-only guidance proposal variants
- authenticated facility-scoped presence reads -> explicit tap-link and facility streak truth over linked visit rows only
- authenticated ops overview reads -> APOLLO schedule summary plus ATHENA current
  occupancy and bounded aggregate analytics for supervisor/manager/owner only
- authenticated booking request reads -> APOLLO request state and availability
  truth for supervisor/manager/owner only
- trusted-surface-gated booking request creates and transitions -> expected-version
  state-machine truth for manager/owner only
- approved booking request -> linked internal reservation schedule block through
  APOLLO schedule/resource conflict truth
- unauthenticated public booking option availability read -> public-safe
  requestable windows plus generic unavailable hints only
- unauthenticated public booking request intake -> idempotency-keyed requested
  booking request with public source/channel and no schedule block creation
- authenticated staff schedule block reads/creates/cancels -> supervisor
  read-only plus manager/owner trusted-surface-gated single-instance controls,
  with generic schedule cancellation refusing booking-linked reservations
- authenticated competition command readiness/execution plus service-backed CLI
  parity -> APOLLO-owned command/outcome/readiness truth over existing
  competition services, with dry-run plans and trusted-surface-gated live apply
- internal competition analytics rebuilds -> APOLLO-owned derived stat events
  and projections over finalized/corrected canonical results plus legacy active
  rating facts only, with explicit projection version, confidence, sample size,
  computed time, source match/result, and deterministic watermarks
- manager/internal competition safety/reliability reads and commands ->
  APOLLO-owned report, block, reliability, and audit facts with capability and
  trusted-surface gates, without mutating canonical competition truth

That is enough to prove member ownership, state persistence, and the first real
intent-behavior, explicit lobby-membership, workout-history, deterministic
workout recommendation, bounded planner substrate, bounded deterministic
coaching substrate, bounded conservative nutrition substrate, thin
agent-facing helper reads, one facility-scoped presence substrate, one bounded
competition execution line, one first ops-read foundation, and one internal
request-first booking foundation plus public request intake without widening
into dashboard-first analytics, public profiles/stats/scouting, carry
coefficient, OpenSkill read-path switch, public tournaments, public competition
surfaces, public/member safety UI, messaging/chat, game identity, generated
apply paths, instant booking, payments, quotes, recurrence, broad
operating-hours policy editing, or deploy claims.

## Current Real Slice

| Surface | Status | Notes |
| --- | --- | --- |
| `apollo serve` | Real | Starts health, auth, profile, eligibility, and optional NATS consumer paths |
| `GET /api/v1/health` | Real | Reports service health and consumer enablement |
| `POST /api/v1/auth/verification/start` | Real | Starts account verification or passwordless sign-in |
| `GET/POST /api/v1/auth/verify` | Real | Consumes a stored token and issues a signed session cookie |
| `POST /api/v1/auth/logout` | Real | Revokes the current session and clears the cookie |
| `GET /api/v1/profile` | Real | Authenticated profile read |
| `PATCH /api/v1/profile` | Real | Authenticated update for `visibility_mode`, `availability_mode`, bounded `coaching_profile`, and bounded non-clinical `nutrition_profile` inputs |
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
| `PUT /api/v1/workouts/{id}/effort-feedback` | Real | Authenticated owner-scoped finished-workout effort feedback write with one bounded enum per workout |
| `PUT /api/v1/workouts/{id}/recovery-feedback` | Real | Authenticated owner-scoped finished-workout recovery feedback write with one bounded enum per workout |
| `GET /api/v1/recommendations/coaching?week_start=...` | Real | Authenticated deterministic coaching read over planner/profile/workout truth with structured proposal/explanation output and no planner mutation |
| `GET /api/v1/helpers/coaching`, `/why`, and `/variation` | Real in repo/runtime | Authenticated helper reads over deterministic coaching truth with bounded explanation topics and read-only variation previews |
| `GET/POST /api/v1/nutrition/meal-templates` plus `PUT /api/v1/nutrition/meal-templates/{id}` | Real in repo/runtime | Authenticated owner-scoped reusable meal-template truth with duplicate-name and bounded-payload validation |
| `GET/POST /api/v1/nutrition/meal-logs` plus `PUT /api/v1/nutrition/meal-logs/{id}` | Real in repo/runtime | Authenticated owner-scoped explicit meal-log truth with deterministic template reuse and explicit update payloads |
| `GET /api/v1/recommendations/nutrition` | Real in repo/runtime | Authenticated conservative nutrition read with calorie/macro ranges, strategy flags, and thin non-clinical limitation output |
| `GET /api/v1/helpers/nutrition`, `/why`, and `/variation` | Real in repo/runtime | Authenticated helper reads over deterministic nutrition truth with bounded explanation topics and read-only cheaper/simpler guidance proposals |
| `GET /api/v1/presence` | Real in repo/runtime | Authenticated facility-scoped presence read over explicit linked visit rows, explicit tap-link state, recent visit context, and facility streak state/events only |
| `GET /` | Real | Session-aware redirect into the member shell |
| `GET /app/login` | Real | Public HTML verification bootstrap page |
| `GET /app` | Real | Protected minimal member shell over the existing APOLLO APIs |
| `apollo visit list` | Real | Member-facing visit history readback |
| NATS identified-arrival/departure consumer | Real | Consumes `athena.identified_presence.arrived` and `athena.identified_presence.departed` using the shared helper |
| `GET /api/v1/competition/sessions` plus session detail | Real in repo/runtime truth | Authenticated competition staff reads now require explicit `competition_read` capability instead of owner-scoped filtering |
| `POST /api/v1/competition/sessions` plus queue, assignment, lifecycle, team, roster, match, and result mutations | Real in repo/runtime truth | Staff competition mutations now require explicit capability plus trusted-surface proof, and successful writes record durable actor attribution |
| `GET /api/v1/competition/commands/readiness`, `POST /api/v1/competition/commands`, and `apollo competition command run` | Real in repo/runtime truth | Shared APOLLO command/outcome/readiness contract plus service-backed CLI parity over existing competition behavior; dry-run plans do not mutate, live commands preserve capability/trusted-surface enforcement, and result lifecycle commands are version-guarded APOLLO truth |
| `GET /api/v1/competition/safety/readiness`, `GET /api/v1/competition/safety/review`, `POST /api/v1/competition/safety/reports`, `POST /api/v1/competition/safety/blocks`, and `POST /api/v1/competition/reliability/events` | Real in repo/runtime truth | Manager-only safety/reliability contracts over APOLLO report, block, reliability, and audit facts; commands require capability and trusted-surface proof and do not mutate result, rating, analytics, ARES, tournament, public, member, or game identity truth |
| `GET /api/v1/ops/facilities/{facilityKey}/overview` | Real in repo/runtime truth | Authenticated supervisor/manager/owner read requiring `ops_read`; composes APOLLO schedule truth with ATHENA current occupancy and bounded aggregate analytics while omitting raw tap hashes, identity-level presence, booking writes, and staff-shell concerns |
| `GET/POST /api/v1/schedule/blocks` plus `POST /api/v1/schedule/blocks/{id}/cancel` | Real in repo/runtime truth | Reads require `schedule_read`; writes require `schedule_manage` plus trusted-surface proof, create only existing APOLLO block types, cancel with `expected_version`, and refuse generic cancellation of booking-linked reservations |
| `GET/POST /api/v1/booking/requests` | Real in repo/runtime truth | `GET` requires `booking_read`; `POST` requires `booking_manage` plus trusted-surface proof and creates an internal request with APOLLO availability decision truth |
| `GET /api/v1/booking/requests/{id}` plus review/needs-changes/reject/cancel/approve transitions | Real in repo/runtime truth | Reads require `booking_read`; mutations require `booking_manage`, trusted-surface proof, and `expected_version`; approval creates a linked internal reservation block through APOLLO schedule conflict truth |
| `POST /api/v1/booking/requests/{id}/edit` | Real in repo/runtime truth | Requires `booking_manage`, trusted-surface proof, and `expected_version`; edits only requested/under-review/needs-changes requests, reruns APOLLO availability truth, increments version, and creates no schedule block |
| `POST /api/v1/booking/requests/{id}/rebook` | Real in repo/runtime truth | Requires `booking_manage`, trusted-surface proof, `expected_version`, and `Idempotency-Key`; creates a new requested replacement linked to the approved original while leaving the original reservation untouched |
| `POST /api/v1/booking/requests/{id}/public-message` | Real in repo/runtime truth | Requires `booking_manage`, trusted-surface proof, and `expected_version`; stores a customer-safe public message separately from `internal_notes` |
| `GET /api/v1/public/booking/options` | Real in repo/runtime truth | Unauthenticated public-safe options read returning active/bookable/public-labeled choices only |
| `GET /api/v1/public/booking/options/{optionID}/availability` | Real in repo/runtime truth | Unauthenticated read-only public-safe availability for one public option; strict RFC3339 window with 14-day max, requestable windows plus generic closed/booked/unavailable blocks only, and no internal IDs, staff metadata, contact data, request counts, raw conflicts, private labels, notes, quote, or payment truth |
| `POST /api/v1/public/booking/requests` | Real in repo/runtime truth | Unauthenticated bounded public request intake with advisory-lock-serialized idempotency, neutral response, public source/channel, and no schedule block creation |
| `GET /api/v1/public/booking/requests/status` | Real in repo/runtime truth | Unauthenticated receipt-code lookup returning only public status, optional public message, safe requested window, and timestamp; no internal IDs, notes, conflicts, staff, trusted-surface, quote, or payment truth |
| recommendation storage | Schema authored | `apollo.recommendations` exists, but Tracer 7 recommendation reads are derived at read time |
| lobby membership runtime | Real | Explicit join and leave are real durable member intent only |
| results/history plus presence/authz runtime | Phase 3B.18 is the current repo/runtime competition trust/rating/ARES/analytics/internal-tournament/safety line on `main` | Tracer 22 competition-history truth, Tracer 23 planner/profile substrate, Tracer 24 deterministic coaching substrate, Tracer 25 bounded nutrition substrate, Tracer 26 helper reads, Tracer 27 facility-scoped presence/tap-link/streak truth, Tracer 28 explicit role/authz plus actor attribution, Phase 3B.11 command/readiness/CLI foundation, Phase 3B.12 lifecycle/result trust, Phase 3B.13 rating foundation, Phase 3B.14 OpenSkill dual-run comparison, Phase 3B.15 ARES v2 proposal foundation, Phase 3B.16 competition analytics foundation, Phase 3B.17 internal tournament runtime, and Phase 3B.18 social safety/reliability foundation are real on `main` while dashboard-first analytics, public profiles/stats/scouting, carry coefficient, OpenSkill read-path switch, public tournaments, public competition reads, public/member safety UI, messaging/chat, broader staff product, and deployment truth remain deferred |

## Ownership Rules

| APOLLO owns | APOLLO does not own |
| --- | --- |
| member account ownership and session state | raw facility presence truth |
| profile visibility and availability state | occupancy counting |
| visit history as member context | broad staff workflows outside the bounded competition control boundary |
| explicit workout history runtime | raw workout inference from visits |
| deterministic recommendation runtime | raw recommendation inference from visits or profile state |
| derived lobby eligibility, explicit lobby membership, bounded competition execution runtime, and bounded competition-history truth | invites, parties, public competition reads, or broad social product surfaces |
| bounded planner substrate, exercise-library truth, equipment refs, templates/loadouts, richer profile-input truth, bounded deterministic coaching substrate, bounded conservative nutrition substrate, and the bounded competition staff authz substrate | raw workout inference, diagnosis, meal-plan chatbot logic, opaque helper-owned decisions, broad staff product workflows, or public competition reads |
| read-only ops composition over APOLLO schedule truth and ATHENA occupancy/analytics truth | raw tap identities, identity-level presence search, ATHENA analytics semantics, staff shell UI, or deployment orchestration |
| booking request persistence, state transitions, availability preview, public-safe availability/request calendar read, conflict-aware approval into internal schedule reservation blocks, approved cancellation of those linked blocks, public request intake API truth, public receipt/status/message lookup, pending request edit, approved replacement request lineage, and bounded staff-created schedule blocks | customer self-booking, public self-edit/rebook, in-place approved editing, payment/quote/invoice/deposit flows, owner policy editing, recurrence, broad operating-hours policy editing, Hestia staff controls, or member self-booking UI |
| competition command/outcome/readiness contracts, canonical result identity, lifecycle/result facts, correction supersession, finalized/corrected-only rating consumption, versioned legacy rating projections, internal OpenSkill comparison facts/events, ARES v2 queue intent/match-preview proposal facts, internal competition analytics stat events/projections, and staff/internal tournament bracket/seed/team-snapshot/match-binding/advancement/event facts | Themis-owned competition truth, browser trusted-surface tokens, dashboard-first analytics, public profiles/stats/scouting, carry coefficient, OpenSkill read-path switch, public tournaments, public competition surfaces, CP, badges, rivalry, or squads |
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
- the current Tracer 24 line now adds owner-scoped finished-workout
  effort/recovery feedback writes plus deterministic coaching recommendation
  reads with structured proposal/explanation output over the settled planner,
  profile, and workout runtime substrates without mutating planner truth
- the current Tracer 25 repo/runtime closeout line now also adds typed
  `nutrition_profile` inputs, owner-scoped meal-template and meal-log truth,
  and conservative read-only nutrition recommendation ranges over explicit
  profile/history inputs without mutating planner, workouts, visits, or
  competition state
- the current Tracer 26 repo/runtime closeout line now also adds authenticated
  internal helper reads for coaching and nutrition, bounded `why` topics, and
  read-only variation previews/proposals without mutating planner, nutrition,
  workouts, visits, or competition state
- the current Tracer 27 repo/runtime closeout line now also adds
  authenticated/internal `GET /api/v1/presence`, explicit per-visit tap-link
  truth, and facility-scoped streak state/events over linked visit days only
  without mutating workouts, planner, nutrition, lobby membership, ARES, or
  competition state
- the current Tracer 28 repo/runtime closeout line now also adds explicit principal
  roles (`member`, `supervisor`, `manager`, `owner`), deterministic
  competition capabilities, trusted-surface gating for privileged competition
  mutations, and durable actor attribution rows without widening into a role
  management product, persistent approvals, or ATHENA ingress storage work
- the current Milestone 2.0 hardening follow-up now also adds graceful
  shutdown, bounded HTTP/NATS/request handling, one shared-parser
  identified-lifecycle contract path, batched workout exercise list reads, and
  conservative per-workout exercise count limits without widening APOLLO's
  product surface
- `Phase 3B.1` now adds one read-only ops overview route over APOLLO schedule
  truth plus ATHENA current occupancy and bounded aggregate analytics, with
  `ops_read` limited to supervisor, manager, and owner roles; members are denied
  and deployed truth remains unchanged
- `Phase 3B.4` now adds `booking_read` for supervisor/manager/owner,
  `booking_manage` for manager/owner, booking request persistence with versioned
  state transitions, trusted-surface-gated mutations, availability preview, and
  conflict-aware approval into linked internal `reservation` / `hard_reserve`
  schedule blocks; public request intake, instant booking, payments, quotes,
  customer shell work, owner policy writes, admin role widening, gateway, HERMES,
  and deploy remained deferred in that line
- `Phase 3B.5` now adds approved booking cancellation through the existing
  trusted-surface-gated, expected-versioned `/cancel` transition. APOLLO
  atomically cancels the linked internal reservation block, retains
  `schedule_block_id` for audit, and refuses stale booking versions or linked
  schedule drift; in-place approved editing, public request intake, instant
  booking, payments, quotes, customer shell work, owner policy writes, admin role
  widening, gateway, HERMES, and deploy remained deferred in that line
- `Phase 3B.8` now adds staff-side pending request edit and approved replacement
  request creation. Edits are limited to requested, under-review, and
  needs-changes requests; rebook creates a new requested replacement linked to
  the original approved booking with idempotency-key semantics; public
  self-edit/rebook, in-place approved mutation, payments, quotes,
  direct staff schedule controls, gateway, HERMES, and deploy remain deferred
- `Phase 3B.9` now adds a read-only unauthenticated public-safe availability
  read for booking options. Customers can see requestable windows and generic
  unavailable time hints, public submit remains request-only, and staff approval
  remains the only confirmed reservation path; public self-booking,
  self-edit/rebook, payments, quotes, direct staff schedule controls, gateway,
  HERMES, and deploy remain deferred
- `Phase 3B.10` now adds bounded staff schedule controls over existing APOLLO
  schedule APIs. Supervisors read one-off blocks; manager/owner users can
  create supported single-instance blocks and cancel only eligible future
  schedule-managed non-reservation blocks. Recurrence, broad hours editing,
  tournament/session controls, supervisor proposals, public self-booking,
  payments, quotes, gateway, HERMES, and deploy remain deferred
- `Phase 3B.11` now adds the APOLLO-owned competition command foundation:
  shared command/outcome DTOs, readiness/capability checks, dry-run plans, and
  service-backed CLI parity over existing competition services
- `Phase 3B.12` now adds APOLLO-owned lifecycle/result trust: canonical result
  identity, recorded/finalized/disputed/corrected/voided facts, correction
  supersession, lifecycle events, and finalized/corrected-only rating
  consumption
- `Phase 3B.13` now adds APOLLO-owned legacy rating foundation: explicit
  engine/policy versions, golden cases, rating audit events, source result
  binding, rating event IDs, and deterministic projection watermarks.
- `Phase 3B.14` now adds APOLLO-owned OpenSkill dual-run comparison: internal
  comparison rows/events, legacy/OpenSkill deltas, accepted delta budgets,
  scenarios, and explicit delta flags while the active read path remains the
  legacy projection.
- `Phase 3B.15` now adds APOLLO-owned ARES v2 proposal/match-preview
  foundation: explicit queue intent facts, internal preview projections/events,
  APOLLO-computed match quality, predicted win probability, and explanation
  codes while ARES remains proposal-only.
- `Phase 3B.16` now adds APOLLO-owned competition analytics foundation:
  internal stat events and analytics projections over finalized/corrected
  canonical result truth plus legacy active rating facts only, with explicit
  projection version, confidence, sample size, computed time, source
  match/result, and deterministic watermarks. OpenSkill read-path switch,
  dashboard-first analytics, public profiles/stats/scouting, carry coefficient,
  tournament runtime to 3B.17, public competition surfaces to 3B.19, and
  CP/badges/rivalry/squads to 3B.20 remain deferred
- `Phase 3B.17` now adds APOLLO-owned internal tournament runtime:
  staff/internal tournament containers, single-elimination bracket and seed
  facts, immutable team snapshots, match bindings, explicit advance reasons,
  audited round advancement facts, and tournament events over trusted APOLLO
  team/match/result truth. Public tournaments remain deferred to 3B.19, and
  CP/badges/rivalry/squads to 3B.20
- `Phase 3B.18` now adds APOLLO-owned internal social safety/reliability
  foundation: report facts, block facts, reliability events, safety audit
  events, manager-only readiness/review contracts, and aligned commands over
  trusted APOLLO competition truth. Public/member safety UI, messaging/chat,
  public profiles/scouting/leaderboards, public tournaments, and
  CP/badges/rivalry/squads remain deferred
- competition provenance columns such as `owner_user_id` and
  `recorded_by_user_id` remain useful domain truth, but they no longer act as
  the sole authorization key
- `ashton-proto` remains untouched because no shared-contract blocker was
  proven, and deployed truth stays unchanged

## Phase 3B.6 Closeout And Next Fork

`Phase 3B.2 ops shell foundation` is now closed in Themis repo/runtime truth.
APOLLO stayed closed for that packet: Themis consumes APOLLO auth/session/profile
and the existing APOLLO ops overview route without backend drift. Deployed truth
is unchanged, and Hestia, ATHENA, HERMES, gateway, and deploy were not touched.

`Phase 3B.4 request-first booking workspace` is now closed in APOLLO and Themis
repo/runtime truth. APOLLO owns booking request runtime truth and linked schedule
reservation approval. Themis owns the internal staff workspace at `/ops/bookings`.
Deployed truth is unchanged.

`Phase 3B.5 approved booking lifecycle` is now closed in APOLLO and Themis
repo/runtime truth. APOLLO owns approved cancellation of linked internal
reservation blocks, and Themis exposes that action only to manager/owner staff.
Deployed truth is unchanged.

`Phase 3B.6 public request entrypoint` is now closed in APOLLO, Themis, and
Hestia repo/runtime truth. APOLLO owns public request intake API truth with
advisory-lock-serialized idempotency, source/channel attribution, validation,
and no reservation creation on submit. Hestia owns public booking-request intake
at `/intake` plus the authenticated member app under `/app/**`. Themis remains
privileged ops only and blocks `/api/v1/public/*` through its broad APOLLO proxy.
`ashton-booking-intake` was folded into Hestia and removed from active repo
inventory. Deployed truth is unchanged.

`Phase 3B.7 customer status and communication` is now closed in APOLLO, Hestia,
and Themis repo/runtime truth. APOLLO owns opaque public receipt/status/message
lookup; Hestia owns `/intake/status` as a customer-safe status surface; Themis
can save only APOLLO's separate public-safe message field for manager/owner
staff while supervisors stay read-only. Deployed truth is unchanged.

`Phase 3B.8 booking edit and replacement` is now closed in APOLLO and Themis
repo/runtime truth. APOLLO owns staff-side pending request edit, approved
replacement request lineage, rebook idempotency, and schedule availability truth;
Themis owns the internal manager/owner workflow while supervisors remain
read-only. Hestia remains customer-facing and unchanged. Deployed truth is
unchanged.

`Phase 3B.9 public availability/request calendar` is now closed in APOLLO and
Hestia repo/runtime truth. APOLLO owns the unauthenticated read-only public-safe
availability endpoint for public booking options; Hestia owns the `/intake`
calendar hints. Customers can see requestable windows and unavailable time hints,
but submit is still request-only and staff approval still creates the only
confirmed reservation. Deployed truth is unchanged.

`Phase 3B.10 bounded staff schedule controls` is now closed in APOLLO and
Themis repo/runtime truth. APOLLO remains the schedule authority, keeps public
availability public-safe, and refuses generic schedule cancellation of
booking-linked reservations. Themis owns the internal schedule page, where
supervisors are read-only and manager/owner users can create supported
single-instance blocks and cancel only eligible future schedule-managed
non-reservation blocks. Deployed truth is unchanged.

`Phase 3B.12 competition lifecycle/result trust` is now closed in APOLLO and
Themis repo/runtime truth. APOLLO owns command/outcome/readiness DTOs, dry-run
plans, capability checks, service-backed CLI parity, canonical result identity,
explicit lifecycle/result facts, correction supersession, and finalized/
corrected-only rating consumption. Themis owns only the internal
`/ops/competition` shell that consumes APOLLO contracts and renders unavailable,
denied, dry-run, accepted, and result-state views without fake competition
state. Hestia and deployed truth are unchanged.

| Topic | Locked statement |
| --- | --- |
| current shell owners | Hestia is the customer-facing shell for `/intake` plus `/app/**`; Themis is the privileged internal ops shell |
| current APOLLO truth | `Phase 3B.1` read-only ops overview, `Phase 3B.4` booking request runtime, `Phase 3B.5` approved booking lifecycle, `Phase 3B.6` public request intake API, `Phase 3B.7` public receipt/status/message lookup, `Phase 3B.8` booking edit/replacement, `Phase 3B.9` public availability/request calendar, `Phase 3B.10` bounded staff schedule controls, `Phase 3B.11` competition command foundation, `Phase 3B.12` lifecycle/result trust, `Phase 3B.13` rating foundation, `Phase 3B.14` OpenSkill dual-run comparison, `Phase 3B.15` ARES v2 proposal foundation, `Phase 3B.16` competition analytics foundation, `Phase 3B.17` internal tournament runtime, and `Phase 3B.18` social safety/reliability foundation are closed on `main` |
| Themis consumption | APOLLO auth/session/profile, ops overview, booking request APIs, pending edit, approved replacement request, trusted-surface public-message update path, schedule APIs, and competition command/readiness/session/result/safety/reliability APIs only; `/api/v1/public/*` remains blocked through Themis |
| next fork | 3B.19 public competition readiness if continuing launch expansion; otherwise a separately scoped booking/schedule/commercial packet only if explicitly reopened |
| APOLLO reopen rule | APOLLO should reopen only if the next packet proves a narrow result-trust, recurrence/policy, payment/quote planning, public self-edit/rebook, self-booking, or in-place approved mutation contract gap |
| hard stops | no customer self-booking, public self-edit/rebook by implication, payment processor integration, checkout UI, quote/deposit/invoice runtime, owner policy writes, broad admin role work, broad schedule editor, gateway widening, deploy work, prediction, AI summaries, Hestia staff controls, or HERMES widening by implication |

## Project Shape

| Path | Purpose |
| --- | --- |
| `cmd/apollo/` | CLI entrypoint and serve command |
| `internal/auth/` | verification token lifecycle, sessions, and signed cookie logic |
| `internal/authz/` | role, capability, and trusted-surface boundary helpers |
| `internal/profile/` | authenticated member profile read and update |
| `internal/eligibility/` | derived lobby-eligibility logic |
| `internal/membership/` | explicit lobby-membership repository and service |
| `internal/consumer/` | identified visit-lifecycle consume path |
| `internal/visits/` | visit service and repository boundary |
| `internal/workouts/` | workout service and repository boundary |
| `internal/recommendations/` | deterministic recommendation service and repository boundary |
| `internal/exercises/` | exercise-library and equipment-ref service and repository boundary |
| `internal/planner/` | planner, template, and weekly-substrate service and repository boundary |
| `internal/coaching/` | deterministic coaching recommendation and feedback boundary |
| `internal/nutrition/` | conservative nutrition recommendation plus meal-template and meal-log boundary |
| `internal/presence/` | facility-scoped presence read, tap-link, and streak boundary over explicit visit truth |
| `internal/competition/` | competition queue, assignment, lifecycle, session, team, roster, and match container repository/service boundary |
| `internal/booking/` | request-first booking state, availability, and approval service boundary over APOLLO schedule truth |
| `internal/athena/` | strict APOLLO-side ATHENA HTTP reader for current occupancy and bounded analytics |
| `internal/ops/` | read-only facility overview composition over APOLLO schedule truth and ATHENA occupancy/analytics truth |
| `internal/sports/` | CLI-only sport registry, facility-sport capability, and static rules/config boundary |
| `internal/server/web/` | embedded member-shell templates, assets, and browser-side tests |
| `internal/store/` | sqlc-generated query bindings |
| `internal/server/` | HTTP handlers, auth middleware, and embedded member-shell wiring |
| `db/migrations/` | current schema for users, sessions, visits, tap-link/streak presence truth, lobby membership, workouts, ARES, recommendations, sport substrate truth, competition execution/runtime tables, schedule truth, and booking requests |
| `docs/` | roadmap, runbooks, ADRs, diagrams, and growing pains |

## Verification Standard

Treat APOLLO as trustworthy only when:

- full Go tests pass repeatedly
- auth/session-sensitive tests are rerun multiple times
- the binary builds
- migrations work on a fresh Postgres database
- the local smoke path covers auth, profile, eligibility, workout runtime,
  explicit lobby membership, deterministic recommendation reads, the thin
  member shell, authenticated competition session/team/roster/match container
  plus queue/assignment/lifecycle/history reads and writes, and the bounded
  planner/template/profile/coaching/nutrition substrate plus the
  trusted-surface-gated competition staff boundary plus the read-only ops
  overview boundary plus the booking request state/approval boundary
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
plus competition-history runtime, the planner/template/profile/coaching/nutrition
substrate, the `Phase 3B.1` ops overview route, and the `Phase 3B.4` booking
request runtime remain locally proven only
unless a separate deployment workstream verifies them live.

## Versioning Discipline

- `PATCH` lines cover hardening, docs sync, deployment closeout, observability,
  and bounded non-widening fixes
- `MINOR` lines cover new bounded member capabilities or intentional contract
  changes

## Release Ladder

| Line | Focus | Hard stop |
| --- | --- | --- |
| `v0.10.0` / `Tracer 19` | sport registry, facility-sport capability mapping, and basic sport rules/config for at least two sports | do not widen into matchmaking runtime yet |
| `v0.11.0` / `Tracer 20` | team, roster, session, and match container primitives | do not widen into public standings |
| `v0.12.0` / `Tracer 21` | matchmaking / queue / assignment flow and session lifecycle | do not widen into rivalry or badge logic |
| `v0.13.0` / `Tracer 22` | result capture, ratings, rudimentary standings, and member profile stats | do not widen into a broad public social layer |
| `v0.14.0` / `Tracer 23` | planner, exercise library, templates / loadouts, and richer profile inputs as backend/CLI-first truth | do not widen into meaningful frontend work yet |
| `v0.15.0` / `Tracer 24` | deterministic coaching substrate over planner, profile, and workout history | do not widen into diagnosis or opaque black-box logic |
| `v0.16.0` / `Tracer 25` | conservative nutrition substrate with meal logging and calorie / macro ranges | do not widen into diagnosis or obsessive nutrition sprawl |
| `v0.17.0` / `Tracer 26` | explanation, summarization, and thin agent-facing helper surfaces | do not let explanation become the core engine |
| `v0.18.0` / `Tracer 27` | member presence / tap-link / streak substrate over explicit visit truth | do not invent fake streak counters or silent visit inference |
| `v0.19.0` / `Tracer 28` | role/authz, actor attribution, trusted-surface primitives, and staff runtime boundary substrate | do not widen into polished ops product or speculative contracts |
| `v0.19.1` / `Milestone 2.0` hardening follow-up | graceful shutdown, bounded ingest/runtime edges, and workout safety on the existing APOLLO line | do not widen into new member/staff product capability or deploy claims |
| `Phase 3B.1` | read-only facility ops overview over APOLLO schedule truth plus ATHENA occupancy and bounded aggregate analytics truth | do not widen into booking, public entrypoints, staff shell UI, policy writes, HERMES widening, gateway work, or deploy claims |
| `Phase 3B.4` | request-first booking runtime with versioned internal requests, APOLLO availability/conflict truth, and linked internal reservation approval | do not widen into public request intake, instant booking, payments, quotes, customer self-service, owner policy writes, broad admin role work, gateway work, HERMES, or deploy claims |
| `Phase 3B.5` | approved booking cancellation with atomic linked reservation cancellation and retained audit linkage | do not widen into public request intake, instant booking, in-place approved editing, payments, quotes, customer self-service, owner policy writes, broad admin role work, gateway work, HERMES, or deploy claims |
| `Phase 3B.8` | staff-side pending request edit plus approved replacement request lineage | do not widen into public self-edit/rebook, in-place approved mutation, payments, quotes, direct staff schedule controls, owner policy writes, broad admin role work, gateway work, HERMES, or deploy claims |
| `Phase 3B.9` | public-safe availability/request calendar read for booking options | do not widen into public self-booking, public self-edit/rebook, in-place approved mutation, payments, quotes, direct staff schedule controls, owner policy writes, broad admin role work, gateway work, HERMES, or deploy claims |
| `Phase 3B.10` | bounded internal staff schedule controls for single-instance APOLLO blocks | do not widen into recurrence, broad operating-hours editing, tournament/session controls, supervisor proposals, public self-booking, payments, quotes, owner policy writes, broad admin role work, gateway work, HERMES, or deploy claims |
| `Phase 3B.11` | competition command/readiness foundation over existing APOLLO competition services | closed by 3B.12 result trust; do not widen into OpenSkill, analytics, tournament runtime, public competition surfaces, Hestia competition expansion, CP, badges, rivalry, squads, browser trusted-surface tokens, proposal workflow, booking/commercial work, or deploy claims |
| `Phase 3B.12` | competition lifecycle/result trust over APOLLO canonical result services | do not widen into OpenSkill, ARES v2, analytics, tournament runtime, public competition surfaces, Hestia competition expansion, CP, badges, rivalry, squads, browser trusted-surface tokens, proposal workflow, booking/commercial work, or deploy claims |
| `Phase 3B.13` | legacy rating foundation over finalized/corrected canonical result truth | do not widen into OpenSkill, ARES v2, analytics, tournament runtime, public competition surfaces, Hestia competition expansion, CP, badges, rivalry, squads, proposal workflow, booking/commercial work, or deploy claims |
| `Phase 3B.14` | OpenSkill dual-run comparison over finalized/corrected canonical result truth | do not widen into OpenSkill read-path switch, ARES v2, analytics, tournament runtime, public competition surfaces, Hestia competition expansion, CP, badges, rivalry, squads, proposal workflow, booking/commercial work, or deploy claims |
| `Phase 3B.15` | ARES v2 proposal foundation over trusted APOLLO projections | do not widen into OpenSkill read-path switch, analytics dashboards, tournament runtime, public/member matchmaking, Hestia competition expansion, CP, badges, rivalry, squads, proposal workflow, booking/commercial work, or deploy claims |
| `Phase 3B.16` | competition analytics foundation over finalized/corrected canonical results plus legacy active rating facts | do not widen into dashboard-first analytics, public profiles/stats/scouting, carry coefficient, OpenSkill read-path switch, tournament runtime, public/member competition surfaces, Hestia expansion, CP, badges, rivalry, squads, proposal workflow, booking/commercial work, or deploy claims |
| `Phase 3B.17` | internal tournament runtime over trusted APOLLO team/match/result truth | do not widen into public tournaments, Hestia member/public expansion, booking/commercial/proposal workflow, OpenSkill read-path switch, ARES behavior changes, dashboard-first analytics, CP, badges, rivalry, squads, or deploy claims |
| `Phase 3B.18` | internal social safety/reliability foundation over trusted APOLLO competition truth | do not widen into public/member safety UI, messaging/chat, public profiles/scouting/leaderboards/tournaments, Hestia expansion, CP, badges, rivalry, squads, OpenSkill read-path switch, ARES behavior changes, analytics dashboards, booking/commercial/proposal workflow, SemVer governance, or deploy claims |
| launch expansion audit | APOLLO competition/rating/tournament/social launch plan | do not skip docs truth, command/CLI/capability substrate, rating extraction/audit, consensus, disputes, privacy, scale, frontend contract, or compatibility gates |

## Deferred On Purpose

- broad frontend widening beyond the thin member shell, including offline or PWA work
- public social/feed surfaces, DMs, and achievement showcase work
- external identity provider widening
