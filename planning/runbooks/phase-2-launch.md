# Phase 2 Launch

## Purpose

Lock one canonical control-plane note for the post-audit Phase 2 rebase.

This document does six things:

1. states the organizer contract
2. records the ATHENA edge-deployment drift ruling
3. locks the canonical revised Phase 2 ladder
4. formalizes milestone naming for the current ladder
5. turns `semver-lite` into explicit pre-`1.0.0` semantic versioning policy
6. defines the standing doctrines and CLI-first posture that should govern every tracer

## Canonical Organizer Contract

- `v2` is the standing master/worker organizer contract
- `v1` remains the source map and directory priority reference
- `v3` remains the exact bounded worker brief for `Tracer 14`
- file truth beats chat memory
- worker chats own one bounded line only
- the master chat owns sequencing, arbitration, and closeout truth

## Bootstrap Read Order Vs Conflict Arbitration Order

Use two different orders for two different jobs.

Bootstrap read order for a fresh master chat:

1. `planning/IMPLEMENTATION-BOARD.md`
2. `planning/STARSHOT-VISION.md`
3. `planning/repo-briefs/`
4. `planning/sprints/TRACER-MATRIX.md`
5. repo-local `README.md` and `docs/roadmap.md`
6. `Prometheus` only when deployment truth is in scope

Conflict arbitration order once you are already working a repo or line:

1. `planning/repo-briefs/`
2. repo-local `README.md` and `docs/roadmap.md`
3. repo-local ADRs
4. repo-local migrations and schemas
5. `planning/sprints/TRACER-MATRIX.md`
6. `planning/audits/`
7. `planning/STARSHOT-VISION.md` for strategic horizon only

## ATHENA Edge Drift Consolidation

Primary label:

- ATHENA adapter + live edge deployment workstream closeout

Authoritative ruling:

- this workstream is real
- it layered on top of shipped `athena v0.4.1`
- it did not consume `Tracer 14`
- it did not consume `Tracer 15`
- it did not consume `Tracer 16`
- it created real deployment truth and planning preconditions for `Tracer 16`

What is already real:

- the live TouchNet browser bridge is real
- the current Cloudflare-backed browser-reachable ATHENA ingress path is real
- the current per-node tokens and workstation userscripts are real
- modern Windows and legacy Chromebook Tampermonkey variants are real
- `ms-gym-01` and `ms-gym-02` are proven live node-level posting paths
- direction inference from the TouchNet row text is real
- accepted `pass` rows update live occupancy and identified publish
- append-only durable edge-observation history is now real on `main` in
  `athena` behind that same `POST /api/v1/edge/tap` handler
- immutable replay identity hardening is now real on `main`, so committed
  history binds to one durable observation rather than bare caller-supplied
  `event_id`
- fail-open shadow-write is now real on `main`: durable-write failure does not
  break the live tap accept/publish/projection path
- restart/reload replay of committed `pass` observations is now real as
  local/runtime groundwork when the history path is configured

What is still not real:

- deployed truth for the durable-history branch beyond the already-proven live
  ingress path
- prediction or broader session analytics over that history
- public operator or report surfaces over that history
- rivalry, teammate, or standings truth

Current Tracer 16 closeout note:

- `athena v0.5.0` runtime truth is shipped
- `ashton-platform v0.0.23` control-plane closeout is shipped
- `v0.5.0` and `v0.0.23` are the Tracer 16 release lines
- deployed truth remains unchanged and bounded to the existing live edge path

Current Tracer 17 closeout note:

- `hermes v0.2.0` runtime truth is now shipped
- bounded `athena v0.5.1` support is now shipped as a follow-up on the
  existing durable-history line
- `ashton-platform v0.0.24` control-plane closeout is now shipped
- deployed truth remains unchanged: the live HERMES runner still proves only
  the occupancy slice, and the live ATHENA edge path remains the current
  deployed physical-truth proof

Current Tracer 18 closeout note:

- current `athena` on `main` now exposes validated file-backed facility catalog,
  hours, zones, closure windows, and bounded metadata reads
- the real surfaces are `athena facility list`, `athena facility show`,
  `GET /api/v1/facilities`, and `GET /api/v1/facilities/{facility_id}`
- `ashton-proto` remains untouched because ATHENA-owned reads are sufficient
- deployed truth remains unchanged: the live ATHENA edge path is still the
  current deployed physical-truth proof
- `athena v0.6.0` and `ashton-platform v0.0.25` are the Tracer 18 release
  lines

Current Tracer 19 closeout note:

- `apollo v0.10.0` is now shipped with CLI-only sport registry reads for
  badminton and basketball, facility-sport capability mapping, and static sport
  rules/config
- `ashton-proto` remains untouched because no shared sport/facility contract
  was required
- deployed truth remains unchanged: the APOLLO sport substrate is local/runtime
  truth only
- `apollo v0.10.0` and `ashton-platform v0.0.26` are the Tracer 19 release lines

Current Tracer 20 closeout note:

- the current APOLLO repo/runtime line now carries authenticated internal HTTP
  competition session, team, roster, and match container primitives on
  dedicated APOLLO tables
- session-wide roster exclusivity is schema-backed, the down migration is
  clean, and same-user membership remains allowed across different sessions
- `ashton-proto` remains untouched because no shared competition contract
  blocker was proven
- deployed truth remains unchanged: Tracer 20 is still repo/runtime truth only
- the current Tracer 20 closeout lines are `apollo v0.11.0` and
  `ashton-platform v0.0.27`

Current Tracer 22 closeout note:

- the current APOLLO repo/runtime line now carries immutable result capture,
  real `completed` lifecycle truth, sport-and-mode-separated ratings,
  session-scoped standings, and self-scoped member stats over the settled
  queue/assignment/lifecycle substrate
- competition history stays APOLLO-owned and authenticated/internal-first;
  explicit lobby membership remains intent input only and ARES preview remains
  read-only
- `ashton-proto` remains untouched because no shared competition-history
  contract blocker was proven
- deployed truth remains unchanged: Tracer 22 is published repo/runtime truth,
  not deployed truth
- the intended Tracer 22 closeout lines are `apollo v0.13.0` and
  `ashton-platform v0.0.29`

Current Tracer 23 closeout note:

- the current APOLLO repo/runtime line now carries planner, exercise-library,
  equipment-ref, template/loadout, planner-week/session/item, and typed
  `coaching_profile` truth on `main`
- the planner substrate stays authenticated/internal-only and separate from
  workout history, deterministic recommendation reads, visits, membership,
  competition history, and later coaching logic
- `ashton-proto` remains untouched because no shared-contract blocker was
  proven
- deployed truth remains unchanged: Tracer 23 is repo/runtime truth, not
  deployed truth
- the Tracer 23 closeout lines are `apollo v0.14.0` and
  `ashton-platform v0.0.30`

Current Tracer 24 closeout note:

- the current APOLLO repo/runtime line now carries deterministic coaching
  substrate truth through owner-scoped finished-workout effort/recovery
  feedback writes plus deterministic coaching recommendation reads over
  planner/profile/workout truth with structured proposal/explanation output and
  no silent planner mutation
- `apollo v0.15.1` is the narrow post-closeout hardening patch line for that
  same Tracer 24 coaching substrate, not a product widening
- `ashton-proto` remains untouched because no shared-contract blocker was
  proven
- deployed truth remains unchanged: Tracer 24 is repo/runtime truth, not
  deployed truth
- the Tracer 24 closeout lines are `apollo v0.15.0` and
  `ashton-platform v0.0.31`

Current Tracer 25 closeout note:

- the current APOLLO `main` line now also carries typed non-clinical
  `nutrition_profile` inputs, owner-scoped meal-template and meal-log truth,
  and conservative read-only calorie/macro recommendation ranges
- the nutrition surface stays authenticated/internal, non-clinical, and
  separate from planner mutation, helper-first AI, presence/streak, and
  role/authz widening
- `ashton-proto` remains untouched because no shared-contract blocker was
  proven
- deployed truth remains unchanged: Tracer 25 is current repo/runtime closeout
  truth on `main`, not current deployed truth
- the Tracer 25 closeout line maps to `apollo v0.16.0` and
  `ashton-platform v0.0.32`

Current Tracer 26 closeout note:

- the current APOLLO `main` line now also carries authenticated/internal helper
  reads for coaching and nutrition over the settled deterministic
  planner/profile/workout/meal substrate
- coaching helper reads now expose bounded `why` topics and read-only
  `easier` / `harder` variation previews over the existing deterministic
  `PlanChangeProposal` surface without mutating planner truth
- nutrition helper reads now expose bounded `why` topics and read-only
  `cheaper` / `simpler` guidance proposals while preserving the current
  calorie and macro targets without claiming an apply path
- helper surfaces stay authenticated/internal, read-only, and separate from
  helper persistence, model-backed calls, presence/streak, role/authz, and
  deployment widening
- `ashton-proto` remains untouched because no shared-contract blocker was
  proven
- deployed truth remains unchanged: Tracer 26 is current repo/runtime closeout
  truth on `main`, not current deployed truth
- the Tracer 26 closeout line maps to `apollo v0.17.0` and
  `ashton-platform v0.0.33`

Current Tracer 27 closeout note:

- the current APOLLO `main` line now also carries authenticated/internal
  `GET /api/v1/presence` for facility-scoped member-visible presence reads over
  explicit linked visit truth
- tap-link state is now explicit per visit, and streak state plus streak
  events are now explicit and facility-scoped instead of being inferred on read
- arrival/departure truth still flows through the existing visit ingest/close
  lifecycle; no hidden visit inference, fake streak counters, or
  cross-facility merge layer was added
- `ashton-proto` remains untouched because no shared-contract blocker was
  proven
- deployed truth remains unchanged: Tracer 27 is current repo/runtime closeout
  truth on `main`, not current deployed truth
- the Tracer 27 closeout line maps to `apollo v0.18.0` and
  `ashton-platform v0.0.34`

Current Tracer 28 closeout note:

- the current APOLLO local/runtime line now also carries explicit principal
  roles (`member`, `supervisor`, `manager`, `owner`) plus deterministic
  competition capabilities derived from that role
- competition staff reads now require explicit capability truth, and
  privileged competition mutations now require both capability and
  trusted-surface proof instead of inheriting authority from
  `competition_sessions.owner_user_id`
- successful staff-sensitive competition mutations now write durable actor
  attribution rows carrying actor user/session/role, capability,
  trusted-surface key, action, and relevant competition target ids
- `ashton-proto` remains untouched because no shared-contract blocker was
  proven, and no persistent approval object or ATHENA ingress-storage widening
  was added
- the Tracer 28 closeout lines map to `apollo v0.19.0` and
  `ashton-platform v0.0.35`
- deployed truth remains unchanged: Tracer 28 is current repo/runtime truth on
  `main`, while tags, deployment claims, and Milestone 2.0 reconciliation stay
  separate

Current Milestone 2.0 closeout note:

- `apollo v0.19.1` is the current local/runtime hardening follow-up on top of
  the Tracer 28 line
- `athena v0.6.1` is the current local/runtime hardening follow-up on top of
  the Tracer 18 line
- `ashton-mcp-gateway v0.2.1` is the current local/runtime hardening follow-up
  on top of the Tracer 15 line
- `ashton-platform v0.0.36` is the control-plane ledger line for this plateau
- deployed truth remains unchanged: live ATHENA is still the bounded `v0.4.1`
  edge deployment with quick-tunnel ingress and no proved durable history path
- the canonical Milestone 2.0 ledger is
  `planning/audits/2026-04-10-milestone-2.0-reconciliation.md`

Phase 2 rule for `Tracer 16`:

- keep the same tunnel, token, and userscript contract
- add persistence behind the same `POST /api/v1/edge/tap` handler
- start with fail-open shadow-write posture
- do not let the first durable rollout break live tap collection

ATHENA-only signal boundary:

- co-presence, dwell, regularity, and routine signals may become internal analytics after durable history exists
- rivalry, teammate, faced-most, badge, and public/social product signals stay later in APOLLO or later phases

## Canonical Phase 2 Posture

Phase 2 is no longer demo-first.

Phase 2 is:

- backend-first
- CLI-first and internal-API-first
- tracer-modular
- repo-bounded
- anti-frontend-sprawl

Phase 2 is not:

- a broad frontend push
- a design-system phase
- a public demo phase
- a justification for widening public social surfaces before the underlying truth exists

Working rule:

- a tracer is allowed to be real with only CLI, internal HTTP, operator, or agent-facing surfaces
- frontend may stay thin or absent through all of Phase 2
- meaningful front-end and demo work starts in Phase 3, after the backend/base ladder is closed cleanly

## Canonical Revised Phase 2 Ladder

### Phase 2A: Structural Pillars

| Line | Lead repo(s) | Release line | Purpose | Hard stop |
| --- | --- | --- | --- | --- |
| `Tracer 14` | `hermes` | `hermes v0.1.1`, `ashton-platform v0.0.20` | structured request / result / outcome observability for the current occupancy slice | no richer question, no writes |
| `Milestone 1.7` | `hermes` + `Prometheus` | `hermes v0.1.2` only if runtime changes, otherwise deploy closeout only, `ashton-platform v0.0.21` | bounded live HERMES deployment proof | no write authority |
| `Tracer 15` | `ashton-mcp-gateway` + optional `ashton-proto` | `ashton-mcp-gateway v0.2.0`, optional `ashton-proto v0.4.0`, `ashton-platform v0.0.22` | caller identity, persisted audit, and second routed read | no approvals or writes |
| `Tracer 16` | `athena` + optional `ashton-proto` | `athena v0.5.0`, `ashton-platform v0.0.23` | durable edge history, session-inference groundwork, and fail-open shadow-write rollout | no prediction or broad rollout claims |
| `Tracer 17` | `hermes` with bounded `athena` support | `hermes v0.2.0`, `athena v0.5.1`, `ashton-platform v0.0.24` | operator / reconciliation read surface, occupancy reports, and heat-map-style reads over stable history | no overrides or writes |

### Phase 2B: Operational And Competition Base

| Line | Lead repo(s) | Release line | Purpose | Hard stop |
| --- | --- | --- | --- | --- |
| `Tracer 18` | `athena` | `athena v0.6.0`, `ashton-platform v0.0.25` | facility catalog, hours, zones, closure windows, and per-facility metadata read surfaces | no social or product logic |
| `Tracer 19` | `apollo` | `apollo v0.10.0`, `ashton-platform v0.0.26` | sport registry for at least two sports, facility-sport capability mapping, and basic sport rules/config | no full matchmaking yet |
| `Tracer 20` | `apollo` | `apollo v0.11.0`, `ashton-platform v0.0.27` | team, roster, session, and match container primitives | no public standings |
| `Tracer 21` | `apollo` | `apollo v0.12.0`, `ashton-platform v0.0.28` | matchmaking / queue / assignment flow and session lifecycle | no rivalry or badge logic |
| `Tracer 22` | `apollo` + optional `ashton-proto` | `apollo v0.13.0`, `ashton-platform v0.0.29` | result capture, ratings, rudimentary standings, and member profile stats | no broad public social layer |

### Phase 2C: Backend Product Expansion

| Line | Lead repo(s) | Release line | Purpose | Hard stop |
| --- | --- | --- | --- | --- |
| `Tracer 23` | `apollo` | `apollo v0.14.0`, `ashton-platform v0.0.30` | planner, exercise library, templates/loadouts, and richer profile inputs as backend/CLI-first truth | no meaningful frontend widening |
| `Tracer 24` | `apollo` | `apollo v0.15.0`, `ashton-platform v0.0.31` | deterministic coaching substrate over planner, profile, and workout history | no diagnosis, no opaque core |
| `Tracer 25` | `apollo` | `apollo v0.16.0`, `ashton-platform v0.0.32` | conservative nutrition substrate with meal logging and calorie/macro ranges | no diagnosis, no obsessive nutrition sprawl |
| `Tracer 26` | `apollo` | `apollo v0.17.0`, `ashton-platform v0.0.33` | explanation, summarization, bounded AI helper flows, and thin agent-facing helper surfaces over stable deterministic logic | no public social feed, no frontend-first product pivot |
| `Tracer 27` | `apollo` | `apollo v0.18.0`, `ashton-platform v0.0.34` | member presence, tap-link, and streak substrate over explicit visit truth | no fake streak counters or silent visit inference |
| `Tracer 28` | `apollo` | `apollo v0.19.0`, `ashton-platform v0.0.35` | role/authz, actor attribution, trusted-surface primitives, and staff runtime boundary substrate | no polished ops suite or speculative contract widening |
| `Milestone 2.0` | cross-repo closeout | `ashton-platform v0.0.36`, plus `apollo v0.19.1`, `athena v0.6.1`, and `ashton-mcp-gateway v0.2.1` where real hardening lands | Phase 2 backend/base plateau: structural pillars, competition base, planner/coaching/nutrition/presence/authz substrate, CLI/internal surfaces, proposal/apply rails, and docs/deploy truth aligned | not a broad demo milestone |

## Milestone Naming Rule

Historical names stay as history:

- `Milestone 1`
- `Milestone 1.5`
- `Milestone 1.6`

Current interpretation:

- whole-number milestone = a broader platform proof plateau
- decimal milestone = a bounded deployment or hardening deepening pass inside the same milestone family
- `Milestone 1.5` and `Milestone 1.6` may keep their historical aliases `Deployment Workstream A` and `Deployment Workstream B`
- `Milestone 1.7` is the next bounded deployment deepening pass in the Milestone 1 family
- `Milestone 2.0` is now the backend/base closeout plateau for the full Phase 2 ladder
- do not renumber historical artifacts retroactively

Create a new decimal milestone only when all of the following are true:

- the pass deepens already-earned truth
- the pass stays narrower than a new tracer or new platform plateau
- the pass primarily proves deployment, hardening, or closure quality
- the pass does not invent a broader feature claim

## Standing Doctrines And Trigger Markers

These are standing doctrines, not separate optional phases.

You may track them in separate chats, but they should apply throughout Phase 2.

### Release Discipline

Always-on doctrine:

- formal pre-`1.0.0` semantic versioning
- tags
- release notes
- repo/doc alignment

Trigger markers:

- the moment a tracer claims a new runtime capability
- the moment a public boundary changes
- before any tag
- before any closure claim

### Deployment Discipline

Always-on doctrine:

- split deploy truth honestly
- keep ATHENA core vs edge boundaries legible
- add HERMES deploy slice
- digest-pin critical internet-facing workloads

Trigger markers:

- whenever runtime truth changes in a deployed repo
- whenever a new bounded deploy slice is needed
- whenever live truth and repo truth might diverge

### Contract Discipline

Always-on doctrine:

- widen `ashton-proto` only when a tracer truly needs shared contract growth
- add compatibility checks when a shared surface becomes real platform truth

Trigger markers:

- second producer or consumer appears
- a shared manifest, event, or schema changes
- a CLI, HTTP, or event boundary becomes a depended-on public surface

### Hardening Discipline

Always-on doctrine:

- repeated destructive tests
- smoke reruns
- `race` / `vet` where appropriate
- explicit local / deployed / deferred truth split

Trigger markers:

- every tracer closeout
- every deploy-affecting line
- every persistence, auth, identity, or audit line
- any flaky or non-repeatable proof

## CLI And Agent-First Surface Frames

Phase 2 should leave behind surfaces that agents and operators can actually work with.

Default rule:

- first truthful surface may be CLI, internal HTTP, or bounded operator tooling
- do not wait for a front end to make a tracer real

Target surface posture by repo:

| Repo | Phase 2 surface posture |
| --- | --- |
| `athena` | CLI and internal read surfaces for occupancy, edge observations, sessions, facilities, hours, and metadata |
| `hermes` | CLI and operator-facing read surfaces for occupancy plus one richer reconciliation answer that includes reports and heat-map-style buckets |
| `apollo` | API/CLI-first member, sport, team, session, result, planner, and coaching primitives |
| `ashton-mcp-gateway` | actor-aware routed reads and audit-first control-plane tooling |
| `ashton-proto` | narrow shared contract growth only when a tracer creates a real cross-repo dependency |
| `Prometheus` | deploy/runbook truth, not product logic |

CLI examples that fit the ladder:

- `athena edge observations list ...`
- `athena edge sessions list ...`
- `athena facility list ...`
- `athena facility show ...`
- `hermes ask occupancy ...`
- `hermes ask reconciliation ...`
- `apollo sport list ...`
- `apollo team list ...`
- `apollo match create ...`
- `apollo result record ...`
- `apollo planner show ...`

The exact command shapes can change by repo, but the frame should not:

- agents should be able to inspect, verify, and harden Phase 2 truth without needing a thick UI

## Pre-1.0 Semantic Versioning Policy

ASHTON now treats `semver-lite` as historical shorthand only.

Effective policy: ASHTON uses formal pre-`1.0.0` semantic versioning.

Release rules:

- `MAJOR` stays `0` until a repo has earned `1.0.0`
- `MINOR` = a new bounded runtime capability or an intentional pre-`1.0.0` breaking surface change
- `PATCH` = hardening, docs sync, deployment closeout, observability, bug fixes, or other bounded follow-up on an existing capability line
- control-plane and deployment repos may use patch bumps for ledger or closeout truth even when service repos use minor bumps for runtime growth

Breaking-change rule before `1.0.0`:

- a breaking change still requires at least a `MINOR` bump
- the release note must call out the broken surface explicitly
- the affected surface must name the boundary that changed: CLI, HTTP, event contract, manifest, schema, or migration behavior

## Graduation To 1.0.0

Do not move a repo to `1.0.0` because it feels important.

A repo graduates only when it has all of the following:

- at least one stable public surface that is expected to stay compatible
- explicit compatibility promises for that surface
- reproducible release artifacts
- destructive and smoke proof for the surface
- a current runbook and release note path
- a migration or rollback story when storage is involved
- current docs that agree with shipped tags and deployed truth

## Phase 3 Boundary

Phase 3 begins after `Milestone 2.0`, not before.

Phase 3 is where these become allowed:

- meaningful frontend widening
- demo workstreams
- design-system work
- broader member-facing shells
- public or semi-public product presentation

Phase 2 should not be back-solved around a UI.

It should be finished around modular, trustworthy, agent-friendly backend pillars.
