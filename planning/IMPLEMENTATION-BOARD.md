# ASHTON Implementation Board

This document ties the platform together operationally. It tracks what is locked,
what is deferred, and which documents are authoritative when files disagree.

## Source Of Truth Order

This is conflict-arbitration order, not fresh-chat bootstrap order.

For a fresh master chat, first read:

1. `planning/IMPLEMENTATION-BOARD.md`
2. `planning/STARSHOT-VISION.md`
3. `planning/repo-briefs/`
4. `planning/sprints/TRACER-MATRIX.md`
5. repo-local `README.md` and `docs/roadmap.md`
6. for APOLLO launch expansion specifically:
   `../apollo/docs/launch-expansion-audit.md`

Use this order when planning or implementing:

1. `planning/repo-briefs/`
2. repo-local `README.md` and `docs/roadmap.md`
3. repo-local ADRs
4. repo-local migrations and schemas
5. `planning/sprints/TRACER-MATRIX.md`
6. `planning/audits/`
7. `../apollo/docs/launch-expansion-audit.md` for APOLLO post-current competition/rating/tournament/social expansion sequencing only
8. `planning/STARSHOT-VISION.md` for strategic horizon and future-ladder context only
9. `planning/sprints/BUILD-ORDER.md` as historical planning context only
10. `planning/architecture/` as background reference only

## Current Scan Overlay

For deployed truth and cross-repo compatibility, the current arbiter is
`planning/compatibility-matrix.md` plus
`planning/milestones/milestone-3.0-evidence/README.md`. Where older board,
tracer, audit, or runbook wording conflicts with those files, Milestone 3.0
evidence wins for deployed truth.

- Last verified: `2026-04-29T17:06:16Z`.
- APOLLO is live at `v0.19.2` on image `sha-6b27618`, with health, public
  readiness, metrics, and Prometheus scrape proven.
- ATHENA live truth is `v0.8.2`; older `v0.7.0` deploy wording is historical
  drift unless the compatibility matrix confirms it.
- Prometheus GitOps `v0.0.4` carries APOLLO ServiceMonitor, APOLLO competition
  PrometheusRule, and APOLLO image proof.
- Hestia, Themis, and `ashton-mcp-gateway` have Milestone 3.0 repo-local test
  truth, but no live cluster deployment proof.
- `ashton-proto v0.4.0` is the shipped two-manifest contract line; current HEAD
  in the matrix is `v0.4.0-2-ge49e627`.
- Remaining risks: no live destructive APOLLO mutation probes,
  Hestia/Themis/gateway deploy proof is absent, Themis full-suite fixture debt
  remains, and Gate 8 scale ceilings are locally/runtime-proved rather than
  fully production-load validated.

## Locked Architectural Decisions

- `ATHENA` owns physical truth: presence, occupancy, ingress source, and later zone occupancy.
- `APOLLO` owns member intent: profile, privacy, availability, workout history, coaching, and ARES.
- `HERMES` is staff-only and does not manage student social state.
- Tap-in updates presence, not matchmaking intent.
- Visit history is distinct from workout history.
- APOLLO uses a minimal profile-first model with flexible state storage early.
- Member auth starts with student ID + email verification + signed session cookie.
- `ashton-mcp-gateway` starts in Go and only earns a Rust rewrite after real bottlenecks exist.

## Current Coding Blockers

Milestone 2.0 is closed. ATHENA repo/runtime closeout is also closed on
`main`: `v0.7.1` landed the bounded projector absent-state retention patch, and
the later `main` line added a compact durable projector-miss guardrail without
changing replay authority. `Phase 3 shared substrate B`, `Phase 3B.1 ops read
foundation`, `Phase 3B.4 request-first booking workspace`, `Phase 3B.5
approved booking lifecycle`, `Phase 3B.6 public request entrypoint`,
`Phase 3B.7 customer status/communication`, `Phase 3B.8 booking
edit/replacement`, `Phase 3B.9 public availability/request calendar`, and
`Phase 3B.10 bounded staff schedule controls`, `Phase 3B.11 competition
command foundation`, `Phase 3B.12 competition lifecycle/result trust`,
`Phase 3B.13 rating foundation`, `Phase 3B.14 OpenSkill dual-run`,
`Phase 3B.15 ARES v2 proposal foundation`, `Phase 3B.16 competition
analytics foundation`, `Phase 3B.17 internal tournament runtime`,
	`Phase 3B.18 social safety/reliability foundation`, `Phase 3B.19 public
	competition readiness`, `Phase 3B.20 game identity layer`, `Phase 3B.20.1
	cohesion hardening`, Rating Policy Wrapper, and Rating Policy Simulation /
	Golden Expansion are now also closed in
APOLLO repo/runtime truth on `main`, and `Phase 3B.2 ops shell
foundation` plus the Themis sides of `Phase 3B.4 request-first booking
workspace`, `Phase 3B.5 approved booking lifecycle`, and `Phase 3B.6`
source/proxy boundary hardening, plus `Phase 3B.7` public-safe message triage
and `Phase 3B.8` staff edit/replacement workflow, plus `Phase 3B.10` bounded
staff schedule controls, plus `Phase 3B.11` APOLLO-backed competition ops and
`Phase 3B.12` APOLLO-backed lifecycle/result states, plus `Phase 3B.18`
manager safety/reliability visibility, are now closed in Themis repo/runtime
truth on `main`.
Hestia also carries the `Phase 3B.6` public
intake consolidation at `/intake`, `Phase 3B.7` status lookup at
`/intake/status`, and `Phase 3B.9` public availability/request calendar hints
at `/intake`, plus `Phase 3B.19` public competition readiness at
	`/competition`, plus `Phase 3B.20` APOLLO-provided game identity at
	`/competition` and the member competition surface, with `Phase 3B.20.1` docs
	cohesion hardening applied, while keeping `/app/**` as the authenticated
member app.
`3B-LC Competition Trust Spine Closeout` is also closed as a verification-only
milestone pass over `Phase 3B.11` through `Phase 3B.20.1`. The standard
handoff matrix now starts with clean `main...origin/main` status across APOLLO,
Themis, Hestia, platform, and Prometheus, then reruns APOLLO command/result/
rating/OpenSkill/ARES/analytics/tournament/safety/public/game-identity checks,
Hestia public competition/game-identity/proxy checks, and Themis internal
competition/safety smoke checks before any future agent changes trust-spine
behavior. The closeout did not change deployed truth and does not start the
next strategic line.
Prometheus/live remains on immutable `athena v0.7.0`, and that deploy truth
should stay separate from the active implementation ladder.

The active blockers before the next implementation legs are now:

0. APOLLO launch expansion fork, if chosen next
   - the canonical operating doc is
     `../apollo/docs/launch-expansion-audit.md`
   - 3B.20 has closed game identity only over public-safe competition
     projections
   - Gate 8 numeric ceilings are now declared and locally/runtime-proved for
     APOLLO rating recompute, public readiness, public leaderboard, game
     identity, and CLI/API smoke paths; this is not full production load
     validation and does not change deployed truth
   - Rating Policy Wrapper is closed locally/runtime in APOLLO: the active
     legacy-engine rating projection uses `apollo_rating_policy_wrapper_v1`,
     records calibration/provisional/ranked status, fifth-match ranked
     transition, inactivity sigma-inflation metadata, and climbing-cap
     metadata; OpenSkill remains comparison-only and deployed truth is
     unchanged
   - Rating Policy Simulation / Golden Expansion is closed locally/runtime in
     APOLLO: deterministic fixtures, local CLI JSON output, legacy baseline
     deltas, OpenSkill sidecar deltas, accepted/rejected classification,
     cutover blockers, and policy risks are real while deployed truth is
     unchanged
   - Frontend Route/API Contract Matrix is closed as docs truth only; runtime
     and deployed truth are unchanged
   - the next launch-expansion packet is Game Identity Policy Tuning Loop
   - keep match tiers, consensus workflow, and public tournaments behind
     separate gates
   - internal Themis competition ops must stay a staff/internal shell over real
     APOLLO contracts with no browser-delivered trusted-surface credential
   - approval/proposal, match lifecycle, notifications, schedule policy, and
     court/resource splitting are reusable substrate packets, not public
     surfaces
   - do not jump straight to public tournaments, messaging/chat, broad public
     social surfaces, or OpenSkill public stakes

1. next Phase 3B planning fork after closed `Phase 3B.20`
   - APOLLO now owns staff and public booking request persistence, state
     transitions, public intake idempotency, availability preview,
     conflict-aware approval into linked internal reservation blocks, and
     approved cancellation of those linked blocks, opaque public receipt/status
     lookup, separate public-safe customer message truth, public-safe
     requestable/unavailable availability hints, pending request edit, and
     approved replacement request lineage, plus bounded staff-created schedule
     block truth
   - Hestia now owns public booking-request intake with public-safe availability
     hints at `/intake`, receipt status lookup at `/intake/status`, public
     competition readiness plus APOLLO-provided game identity at `/competition`,
     and keeps authenticated member routes under `/app/**`
   - APOLLO now also owns competition command/outcome/readiness contracts,
     service-backed competition CLI parity, canonical result identity,
     correction supersession, lifecycle/result facts, finalized/corrected-only
     rating consumption, the versioned legacy rating foundation with golden
     cases, audit events, source result IDs, rating event IDs, deterministic
     projection watermarks, internal OpenSkill comparison rows/events with
     delta budgets and flags, ARES v2 queue intent/match-preview proposal
     facts with match quality, predicted win probability, and explanation
     codes, internal derived analytics stat events/projections with explicit
     projection version, confidence, sample size, computed time, source
     match/result, deterministic watermarks, active rating policy wrapper
     metadata for calibration, inactivity sigma inflation, and climbing caps,
     staff/internal tournament containers, single-elimination
     bracket/seed/team-snapshot/match-binding/
     advancement facts, explicit advance reasons, tournament event facts,
     manager/internal safety report facts, block facts, reliability events,
     safety audit events, public-safe competition readiness/leaderboard
     projection contracts over finalized/corrected canonical results plus
     legacy active rating fields, and public/member-safe CP, badge award,
     rivalry state, and squad identity projections over public-safe competition
     projection rows only
   - Themis now owns the internal `/ops/bookings` staff workspace and
     `/ops/facilities/:facilityKey/schedule` schedule workspace over those
     APOLLO contracts, including manager/owner approved cancellation,
     pending/open request edit, approved replacement request creation, and still
     can save APOLLO public-safe customer messages, create supported one-off
     schedule blocks, cancel eligible future schedule-managed non-reservation
     blocks, and render APOLLO-backed competition readiness/dry-run/command/
     result/safety/reliability states without fake competition or safety state;
     it still denies `/api/v1/public/*`
     through its broad APOLLO proxy
	- 3B-LC smoke/stress closeout, Scale Gate Numeric Ceilings, CLI Demo
	  Spine, Rating Policy Wrapper, and Rating Policy Simulation / Golden
	  Expansion are closed locally/runtime; Frontend Route/API Contract Matrix
	  is closed as docs truth only. The next launch-expansion line should be
	  Game Identity Policy Tuning Loop unless a hardening packet is needed, and
	  should keep dashboard-first analytics, public
	  profiles/stats/scouting, carry
     coefficient, OpenSkill read-path switch, public tournaments,
     messaging/chat, broad public social graph, proposal workflow,
     booking/commercial work, SemVer governance, and broader Hestia
     competition expansion out unless a later packet earns those boundaries
2. `apollo` role/authz widening only if later work truly needs it
   - the current planning stance still says migrations plus owner/admin CLI for
     first-line graph authoring, but APOLLO runtime still has no distinct
     `admin` role
   - today that policy maps to owner-only graph authoring in runtime truth; any
     future admin parity with owner must be an explicit APOLLO authz/runtime
     widening, not an accidental side effect of the next ops-shell line
3. `athena` deploy truth
   - keep any later `v0.7.x` deploy repin or smoke closeout separate from
     repo/runtime truth
   - do not let a deploy-only ATHENA packet block the next bounded runtime line
4. `hermes` and `ashton-mcp-gateway`
   - keep carry-forward hardening deferred until those surfaces are about to be
     used by an explicit later Phase 3B packet
   - do not let sidecar hardening reopen the ladder out of order
5. terminology cleanup
   - keep using `presence` instead of `check-in` in current working docs
   - keep using `lobby` for product-facing matchmaking terminology in APOLLO

## Deferred But Planned

These are documented and expected, but they are not active blockers. The durable
register is [`DEFERMENTS.md`](DEFERMENTS.md); update it whenever a future packet
defers, closes, deprecates, replaces, or moves work post-launch.

Current watch-later themes include Redis/cache/rate-limit utility work,
AlertManager and Loki observability, gateway rollout/performance choices,
cluster expansion, NATS clustering, read replicas, PgBouncer, projector
partitioning, OpenAPI/RFC 7807 contract stabilization, and AI/school-integration
ideas. None of those reopen the active ladder without an explicit packet.

Expected UI and backend behavior for current flows is tracked in
[`FLOWS.md`](FLOWS.md). Update it whenever a packet adds or changes button
behavior, background system steps, success states, or failure classifications.

## Redis Guidance

Redis is useful when you need low-latency derived state that should not force a
fresh Postgres query every time:

- `ATHENA`
  - current occupancy counters
  - short-lived zone occupancy caches
- `APOLLO`
  - expensive training history aggregation caches
  - later lobby state or invite expiration if needed
- `ashton-mcp-gateway`
  - token-bucket rate limiting

The first tracer bullet should still work without Redis.

## Go First, Rust Later

Write the first gateway version in Go.

Reasoning:

- Go matches the rest of the platform and your current execution speed
- the first gateway slice is small: discover one tool and route one read-only call
- there is no measured bottleneck yet
- rewriting a proven hot path is defensible; speculative Rust is not

Rust remains a later optimization path, not a first-wave dependency.

## Versioning Policy

This is the current repo tag discipline for bounded pre-`1.0.0` work. It is
not a project-wide SemVer governance program; that governance work remains
deferred until an explicit packet opens it.

| Versioning piece | Meaning in ASHTON now |
| --- | --- |
| Tag shape | All repos keep `vMAJOR.MINOR.PATCH` tags so release lines are readable and comparable |
| `MAJOR` | Stay at `0` during the current foundation-building era; move to `1` only after the system-proof phase makes a repo meaningfully stable |
| `MINOR` | Use for a new bounded runtime capability or tracer-sized widening in that repo |
| `PATCH` | Use for hardening, docs sync, deployment closeout, observability, bug fixes, or other bounded follow-up on an existing capability line |
| Pre-`1.0.0` rule | Use bounded pre-`1.0.0` tag discipline: `MINOR` may widen or intentionally break a pre-`1.0.0` surface; `PATCH` must not add a new capability or a breaking change |
| Breaking-change rule | Before `1.0.0`, breaking changes to public HTTP surfaces, CLI contracts, shared schemas, manifests, or migrations require a `MINOR` bump, never a `PATCH` bump |
| Control-plane and deploy repos | `ashton-platform` and deployment repos may use patch bumps as ledger / closeout lines even when service repos use minor bumps for runtime growth |
| `1.0.0` graduation | A repo graduates only after stable public surfaces, explicit compatibility rules, repeatable hardening evidence, and release automation exist; see `planning/runbooks/phase-2-launch.md` |
| History rule | Existing tags stand as history; stricter version discipline applies forward only |

## Repo Release Lines

| Repo | Current line | Next planned line | Why it is next |
| --- | --- | --- | --- |
| `ashton-proto` | `v0.3.0` shipped; current Tracer 15 contract line `v0.4.0` | later than `v0.4.0` | the second routed manifest line is now real in the current repo line; further widening should stay tracer-driven |
| `athena` | `v0.5.1` shipped; the Tracer 18 facility-truth line plus the `v0.6.1` hardening follow-up are on `main`; `v0.7.0` is shipped and live as the bounded Postgres-backed storage/analytics deployment line; `v0.8.2` is now shipped and live as the bounded policy-backed accepted-presence hardening line; broader session cutover and operator/report surfaces remain deferred | no active ladder line; separate deploy packet only if needed | ATHENA repo/runtime and bounded deployment closeout are done for the current line, and any later deploy repin or broader ATHENA widening must stay separate from the next bounded Phase 3B packet |
| `apollo` | current Tracer 28 repo/runtime line plus the current `v0.19.1` hardening follow-up, the later `Phase 3 shared substrate B` repo/runtime line, the later `Phase 3A.1` member shell foundation line, the later `Phase 3A.3` member truth completion line, the later `Phase 3A.4` member-safe schedule calendar line, the later `Phase 3B.1` ops read foundation line, the later `Phase 3B.4` booking request runtime, the later `Phase 3B.5` approved booking lifecycle, the later `Phase 3B.6` public request intake API, `Phase 3B.7` customer status/message lookup, `Phase 3B.8` booking edit/replacement, `Phase 3B.9` public availability/request calendar, `Phase 3B.10` bounded staff schedule controls, `Phase 3B.11` competition command foundation, `Phase 3B.12` lifecycle/result trust, `Phase 3B.13` rating foundation, `Phase 3B.14` OpenSkill dual-run comparison, `Phase 3B.15` ARES v2 proposal foundation, `Phase 3B.16` competition analytics foundation, `Phase 3B.17` internal tournament runtime, `Phase 3B.18` social safety/reliability foundation, `Phase 3B.19` public competition readiness, `Phase 3B.20` game identity layer, `Phase 3B.20.1` cohesion hardening, Rating Policy Wrapper, and Rating Policy Simulation / Golden Expansion are on `main`; Frontend Route/API Contract Matrix is closed as docs truth only; Tracer 24 remains tagged on `v0.15.0`; `v0.15.1` remains the narrow historical hardening patch line; deployed truth unchanged | Game Identity Policy Tuning Loop if continuing launch-expansion route proof | APOLLO now owns staff and public booking request runtime truth, public idempotency, source/channel, public-safe availability hints, public receipt/status/message lookup, linked reservation approval, approved cancellation of linked reservations, pending request edit, approved replacement requests, bounded staff-created schedule blocks, competition command/outcome/readiness/CLI foundation, canonical result identity, correction supersession, finalized/corrected-only rating consumption, a versioned legacy rating foundation with golden cases, rating events, source result IDs, rating event IDs, projection watermarks, active rating policy wrapper metadata for calibration, inactivity sigma inflation, and climbing caps, deterministic rating simulation proof with CLI JSON output, accepted/rejected classifications, blockers, risks, legacy deltas, and OpenSkill sidecar deltas, internal OpenSkill comparison rows/events with delta budgets and flags, ARES v2 queue intent/match-preview proposal facts, internal competition analytics stat events/projections, staff/internal tournament bracket/seed/team-snapshot/match-binding/advancement/event facts, manager/internal safety report/block/reliability/audit facts, public-safe competition readiness/leaderboard projection contracts, and public/member-safe CP/badge/rivalry/squad game identity projection contracts over trusted competition truth; 3B.20.1 hardened rivalry context, row-scoped labels, docs truth, Rating Policy Wrapper and Rating Policy Simulation closed locally/runtime only, and Frontend Route/API Contract Matrix closed docs truth only. OpenSkill read-path switch, production historical rating backtesting, dashboard-first analytics, public profiles/stats/scouting, carry coefficient, public tournaments, broad public social graph, public/member safety UI, messaging/chat, instant booking, public self-edit/rebook, payments, quotes, in-place approved mutation, recurring schedule policy, broad operating-hours editing, gateway, HERMES, AI/LLM negotiation, SemVer governance, and deploy remain deferred unless a gated packet explicitly opens them |
| `hestia` | `Phase 3A.2` standalone member frontend bootstrap is on `main`; pushed repo truth now names `Hestia`, keeps APOLLO as auth/session authority, records future privileged split to `Themis`, and now also carries the closed `Phase 3A.3` thin member-truth consumption line, the closed `Phase 3A.4` Home schedule-outlook consumption line, closed `Phase 3B.6` public intake consolidation at `/intake`, closed `Phase 3B.7` receipt status lookup at `/intake/status`, closed `Phase 3B.9` public availability/request calendar hints at `/intake`, closed `Phase 3B.19` public competition readiness at `/competition`, closed `Phase 3B.20` APOLLO-provided game identity on `/competition` and the member competition surface, and `Phase 3B.20.1` docs cohesion hardening | post-`Phase 3B.20.1` fork | `Hestia` is the customer-facing shell with public booking-request intake and public-safe availability hints at `/intake`, public-safe status lookup at `/intake/status`, public competition readiness and APOLLO-provided game identity at `/competition`, and authenticated member app under `/app/**`; it must not widen into staff controls, payment/quote flows, CP/badge/rivalry/squad formulas, public tournaments, messaging/chat, broad public social graph, public self-edit/rebook, self-booking, or deployment claims |
| `themis` | `Phase 3B.2` standalone privileged ops shell foundation, `Phase 3B.4` internal booking request workspace, `Phase 3B.5` approved booking cancellation workflow, `Phase 3B.6` public-source/proxy-boundary hardening, `Phase 3B.7` public-safe customer message triage, `Phase 3B.8` booking edit/replacement, `Phase 3B.10` bounded staff schedule controls, `Phase 3B.11` competition ops foundation, `Phase 3B.12` lifecycle/result states, and `Phase 3B.18` manager safety/reliability visibility are on `main`; pushed repo truth names Themis, keeps APOLLO as auth/session/API authority, and consumes APOLLO ops overview plus booking request, schedule, competition command/readiness/session, result, and safety/reliability APIs | post-`Phase 3B.18` fork | Themis is the privileged internal ops shell; it remains blocked from proxying `/api/v1/public/*`, can save only APOLLO's separate public-safe message field, edit pending/open requests, create approved replacement requests, create supported one-off schedule blocks, cancel eligible future schedule-managed non-reservation blocks, and render APOLLO-backed competition readiness/dry-run/command/result/safety/reliability states without fake competition or safety state; deployed truth is unchanged, and Hestia, ATHENA, HERMES, gateway, and deploy were not widened |
| `hermes` | `v0.2.0` shipped | `v0.3.0` | the richer read-only reconciliation line is now shipped and the first write/approval boundary is the next true widening |
| `ashton-mcp-gateway` | `v0.0.1` shipped; current Tracer 15 line plus the current `v0.2.1` hardening follow-up are on `main` | `v0.3.0` | the caller-aware audited read-only control-plane slice is now harder at the boundary; write governance is still the next true widening |
| `ashton-platform` | current Milestone 2.0 control-plane closeout line on `main`; deployed truth unchanged | later than `v0.0.36` | `v0.0.36` is the Phase 2 plateau ledger line; later work should move to system-proof or Phase 3 instead of reopening deploy claims casually |

## 2026-04-13 Backend Logic Audit Ledger

This ledger captures the verified backend logic audit after the ATHENA
`v0.7.0` deploy widening closed. It narrows what actually needs code work next
instead of letting chat-only findings drift into fake urgency.

### Verification Narrative

| Repo | Command rerun | Result | Why it matters |
| --- | --- | --- | --- |
| `apollo` | `go test -count=1 ./...` | pass | reran presence, workouts, competition, server, and runtime bootstrap under the committed `v0.19.1` line |
| `athena` | `go test -count=1 ./...` | pass | reran projector, edge, edgehistory, server, and Postgres-backed storage/analytics paths under the committed `v0.7.0` line |
| `hermes` | `go test -count=1 ./...` | pass | reran occupancy and reconciliation reads against the shipped `v0.2.0` line |
| `ashton-mcp-gateway` | `go test -count=1 ./...` | pass | reran routed read, caller identity, audit, manifest, and server paths under the committed `v0.2.1` line |

### Audit Outcome

| Finding | Repo | Severity | Ruling | Meaning |
| --- | --- | --- | --- | --- |
| audit persistence still blocks a successful routed response | `ashton-mcp-gateway` | High | verified | this is the only verified item in the audit that can turn a successful upstream read into a user-facing `500` |
| projector identity retention is still unbounded | `athena` | Medium | verified | this is the only confirmed scale-risk item carried forward from the audit |
| reconciliation bin boundary semantics are asymmetric | `hermes` | Medium | verified | current bins are half-open except the last bin, and boundary tests do not pin the intended rule yet |
| NATS identified-presence handlers still use `context.Background()` | `apollo` | Low | verified | shutdown cancellation still does not flow into in-flight message handlers |
| metrics occupancy callback still uses `context.Background()` | `athena` | Low | verified | cleanup only, not a capability issue |
| projector clock constructor is more confusing than it needs to be | `athena` | Low | verified | readability cleanup only |
| query-instantiation cleanup is broader than workouts alone | `apollo` | Low | verified with narrowed scope | the pattern still appears in multiple APOLLO repositories, so it should be treated as a broader mechanical cleanup rather than a workouts-only bug |

### Disproved Or Narrowed Claims

| Claim | Ruling | Why it narrowed |
| --- | --- | --- |
| APOLLO streak active-status off-by-one | disproved | the existing logic keeps the full next UTC day active; the proposed `+2` change would have introduced a real bug |
| APOLLO streak reset failed to update `currentStartDay` | disproved | `currentStartDay` already initializes to `creditDay`, and the reset path preserves that value correctly |
| APOLLO competition tiebreak could flip after team deletion/recreation | narrowed | `SideIndex` remains the final deterministic tiebreak, but the stated failure mode is blocked because team removal is draft-only and referenced teams cannot be removed once matches exist |
| workouts was the only remaining `store.New(...)` per-call repository | disproved | the pattern is still present in multiple APOLLO repositories |

### Commit Ladder For This Docs Pass

| Order | Repo | Commit target | Purpose |
| --- | --- | --- | --- |
| 1 | `ashton-mcp-gateway` | `docs: record gateway audit carry-forward after v0.2.1` | lock the current fail-closed audit behavior as an explicit next policy decision instead of a silent runtime quirk |
| 2 | `athena` | `docs: record ATHENA audit carry-forward after v0.7.0` | lock projector retention as the first real post-storage hardening target and keep prediction/dashboard pressure deferred |
| 3 | `hermes` | `docs: record HERMES audit carry-forward after v0.2.0` | make the reconciliation boundary rule explicit before any later HERMES widening |
| 4 | `apollo` | `docs: record APOLLO audit carry-forward after v0.19.1` | stop false streak fixes from reopening correct code and carry forward only the real low-severity cleanup items |
| 5 | `ashton-platform` | `docs: add backend logic audit ledger` | keep the cross-repo result visible in the control-plane source of truth after repo-local docs are aligned |

### Next Code Ladder

| Order | Repo | Likely line | Why it is next |
| --- | --- | --- | --- |
| 1 | `apollo`, `hestia`, or `themis` only if a contract gap or write surface is proven | public self-edit/rebook, payment/quote planning, recurrence, or in-place approved mutation fork | choose the next narrow booking/schedule line based on the next proven operational gap, without backsliding into broad scheduling, payment runtime, gateway, deploy, or Hestia staff controls |
| 2 | `apollo` plus `hestia`, with Themis review context later | public self-edit/rebook planning or runtime only if earned | let customers amend requests only after a separate privacy and conflict-boundary proof |
| 3 | planning-first unless runtime contracts are explicitly earned | payment/quote planning | map deposits, quotes, invoices, cancellation policy, and checkout implications without claiming payment readiness |
| 4 | `hermes` / `ashton-mcp-gateway` | bounded follow-ups only when their surfaces are about to be used | keep sidecar carry-forward items from distorting the primary ladder |

Current closeout note:

- `athena` now carries Tracer 16 runtime truth on `main` at
  `50d50cb6b164fd58896d799bafa2e39138765620`
- `athena` now also carries one bounded privacy-safe history read on `main`
  for Tracer 17 support, and that support follow-up is released as `v0.5.1`
- `athena` now also carries Tracer 18 facility-truth runtime on `main` through
  validated file-backed CLI/internal reads for facility catalog, hours, zones,
  closure windows, and bounded metadata
- `hermes` now carries Tracer 17 runtime truth on `main` at
  `2a01b61646b3ef985cbbfd4609f7731e5eb13eb3`
- this repo now carries the aligned Tracer 17 control-plane closeout on `main`,
  and the Tracer 18 closeout line is now also on `main` with deployed truth
  still unchanged
- `apollo` now carries Tracer 20 runtime truth in the current repo line through
  authenticated internal HTTP competition session, team, roster, and match
  containers backed by dedicated APOLLO tables
- session-wide roster exclusivity is now schema-backed, `ashton-proto` remains
  untouched, and deployed truth is still unchanged
- `v0.11.0` and `v0.0.27` are the Tracer 20 repo/control-plane closeout lines
- `apollo` now also carries the tagged Tracer 21 runtime line through
  authenticated internal HTTP queue state, deterministic assignment into
  session/team/roster/match containers, and explicit session lifecycle
  transitions
- `ashton-proto` remains untouched because no shared execution contract blocker
  was proven
- the Tracer 21 release lines are `apollo v0.12.0` and
  `ashton-platform v0.0.28`
- `apollo` now also carries Tracer 22 competition-history runtime truth on
  `main` through immutable result capture, real `completed` lifecycle truth,
  sport-and-mode-separated ratings, session-scoped standings, and self-scoped
  member stats
- this repo now carries the aligned Tracer 22 control-plane closeout on `main`
  while deployed truth remains unchanged
- `ashton-proto` remains untouched because no shared competition-history
  contract blocker was proven
- the intended Tracer 22 release lines are `apollo v0.13.0` and
  `ashton-platform v0.0.29`

Current Tracer 23 closeout note:

- `apollo` now also carries planner, exercise-library, equipment-ref,
  template/loadout, planner-week/session/item, and typed `coaching_profile`
  truth on `main`
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

- `apollo` now also carries deterministic coaching substrate truth on `main`
  through owner-scoped finished-workout effort/recovery feedback writes and
  deterministic coaching recommendation reads over planner/profile/workout
  truth with structured proposal/explanation output and no silent planner
  mutation
- `apollo v0.15.1` is the narrow post-closeout hardening patch line for that
  same Tracer 24 coaching substrate, not a product widening
- `ashton-proto` remains untouched because no shared-contract blocker was
  proven
- deployed truth remains unchanged: Tracer 24 is repo/runtime truth, not
  deployed truth
- the Tracer 24 closeout lines are `apollo v0.15.0` and
  `ashton-platform v0.0.31`

Current Tracer 25 closeout note:

- `apollo` now also carries typed non-clinical `nutrition_profile` inputs,
  owner-scoped meal-template and meal-log truth, and conservative read-only
  nutrition recommendation ranges on `main`
- the Tracer 25 nutrition surface stays authenticated/internal, non-clinical,
  and separate from planner mutation, helper-first AI, presence/streak, and
  role/authz widening
- `ashton-proto` remains untouched because no shared-contract blocker was
  proven
- deployed truth remains unchanged: Tracer 25 is current repo/runtime closeout
  truth on `main`, not current deployed truth
- the Tracer 25 closeout line maps to `apollo v0.16.0` and
  `ashton-platform v0.0.32`

Current Tracer 26 closeout note:

- `apollo` now also carries authenticated internal helper reads on `main` for
  coaching and nutrition over the settled deterministic planner/profile/
  workout/meal substrate
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

- `apollo` now also carries authenticated/internal `GET /api/v1/presence` on
  `main` for facility-scoped member-visible presence reads over explicit linked
  visit truth
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
  roles (`member`, `supervisor`, `manager`, `owner`) plus one deterministic
  competition capability set derived from that role
- competition staff reads now require explicit capability truth and privileged
  competition mutations now require both the declared capability and
  trusted-surface proof instead of inferring authority from
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

Current Milestone 2.0 reconciliation note:

- `apollo v0.19.1` is now the current local/runtime hardening follow-up on the
  Tracer 28 line: graceful shutdown, HTTP/NATS/request bounds, single
  shared-parser identified-lifecycle truth, and workout safety hardening are
  now real without widening APOLLO capability
- `athena v0.6.1` is now the current local/runtime hardening follow-up on the
  Tracer 18 line: graceful shutdown, bounded server timeouts, bounded publish
  retry/backoff, and bounded dedupe memory are now real without widening live
  semantics
- `ashton-mcp-gateway v0.2.1` is now the current local/runtime hardening
  follow-up on the Tracer 15 line: constant-time caller-secret comparison,
  declared-argument enforcement, manifest path hardening, and bounded request
  decoding are now real without widening routing scope
- the canonical ledger for this ruling is
  `planning/audits/2026-04-10-milestone-2.0-reconciliation.md`
- at Milestone 2.0 closeout time, deployed ATHENA truth was still the bounded
  `v0.4.1` edge deployment with quick-tunnel ingress and no proved durable
  history path
- that historical deploy ruling is now superseded by the later bounded
  `athena v0.7.0` Prometheus rollout: Postgres-backed append-only
  observations, derived session facts, and bounded internal history/analytics
  reads are now live while the external surface remains `/api/v1/edge/tap` and
  `/api/v1/health`

- `v0.5.1`, `v0.2.0`, and `v0.0.24` are the Tracer 17 release lines for
  bounded ATHENA support, HERMES runtime truth, and platform closeout truth
- `v0.6.0` and `v0.0.25` are the Tracer 18 release lines for ATHENA facility
  truth and platform control-plane closeout
- `v0.7.0` is the later Phase 3 shared substrate A line for Postgres-backed
  observations, derived sessions, bounded internal analytics, and the bounded
  live storage/analytics deploy closeout over the existing edge path
- `athena main` now also carries the post-`v0.7.0` Substrate A hardening tail:
  `v0.7.1` bounded projector absent-state retention, and a later compact
  durable projector-miss guardrail narrowed the old evicted-miss risk without
  changing replay authority or deployed truth

Current Phase 3 shared substrate B closeout note:

- `apollo main` now also carries APOLLO-owned schedule resources, resource
  edges, typed schedule blocks, bounded weekly recurrence with explicit
  block-timezone truth, RFC3339-only calendar windows, explicit date
  exceptions, active-plus-bookable inventory-claim gating, staff-gated reads,
  and staff-gated block writes
- deployed truth remains separate and unchanged: live ATHENA is still bounded
  `v0.7.0`, and no deploy packet was opened just to unblock APOLLO substrate
  work
- the current planning phrase `owner/admin CLI first` is still runtime-mapped
  to owner-only graph authoring because APOLLO has no distinct `admin` role
  yet; later admin parity with owner must be an explicit APOLLO authz widening
- `Phase 3A.1` is now also closed in APOLLO repo/runtime truth: routed
  embedded member-shell posture, truthful section boundaries, and member-safe
  API composition are on `main`
- `Phase 3A.2` frontend repo bootstrap is now closed in `Hestia` repo/runtime truth
- `Phase 3A.3` member truth completion is now closed in APOLLO and `Hestia`
  repo/runtime truth
- `Phase 3A.4` member-safe schedule calendar is now closed in APOLLO and
  `Hestia` repo/runtime truth
- `Phase 3B.1` ops read foundation is now closed in APOLLO repo/runtime truth:
  `GET /api/v1/ops/facilities/{facilityKey}/overview` composes APOLLO schedule
  truth with ATHENA current occupancy and bounded aggregate analytics behind
  `ops_read` for supervisor, manager, and owner users only
- deployed truth remains unchanged for `Phase 3B.1`; the route is runtime-useful
  only when APOLLO is configured with `APOLLO_ATHENA_BASE_URL`, and a missing
  ATHENA dependency fails clearly instead of fabricating ops data

Current Tracer 19 closeout note:

- `apollo v0.10.0` is now shipped with CLI-only sport registry reads for
  badminton and basketball, facility-sport capability mapping, and static sport
  rules/config
- `ashton-proto` remains untouched because no shared sport/facility contract was
  required
- deployed truth remains unchanged: the APOLLO sport substrate is local/runtime
  truth only
- `apollo v0.10.0` and `ashton-platform v0.0.26` are the Tracer 19 release lines

## Urgency Snapshot

| Area | State now | Meaning | Urgency |
| --- | --- | --- | --- |
| ATHENA deploy truth | done | physical-truth pillar is live and credible | done |
| APOLLO auth / membership / preview | done | member intent pillar exists | done |
| HERMES | observability-hardened and live-deployed | published local/runtime truth plus bounded deployment truth | done |
| Gateway | caller-aware and auditable, still narrow | the Tracer 15 runtime proof is real, while broader control-plane maturity remains deferred | medium |
| ATHENA durability | shipped and live | Postgres-backed append-only observations, derived session facts, and bounded internal analytics are now real on the bounded ATHENA deploy path | done |
| HERMES operator surface | shipped | one richer reconciliation question, occupancy reports, and heat-map-style reads are now shipped while deployed truth stays unchanged | done |
| Facility catalog / hours | closure-clean on `main` | ATHENA now exposes config-gated facility catalog, hours, zones, closure windows, and bounded metadata reads while deployed truth stays unchanged | done |
| Competition execution runtime | closure-clean in repo/runtime; deployed truth unchanged | APOLLO now owns sport registry plus queue/assignment/lifecycle truth over team/roster/session/match containers as the settled execution substrate for later competition history | done |
| Ratings / standings / profile stats | closure-clean in repo/runtime; deployed truth unchanged | APOLLO now owns immutable result capture, sport-and-mode-separated ratings, session-scoped standings, and self-scoped member stats without widening into public/social competition reads | done |
| Scheduling substrate | closure-clean in repo/runtime; deployed truth unchanged | APOLLO now owns schedule resources, resource graph truth, typed blocks, RFC3339-windowed calendar reads, block-timezone weekly recurrence, and active-plus-bookable inventory claims without widening into public request intake or UI work | done |
| Planner / coaching / nutrition / presence backend | current Tracer 28 repo/runtime closeout truth on `main`; deployed truth unchanged | current repo/runtime truth | APOLLO now owns the planner, deterministic coaching substrate, exercise-library, template/loadout, richer profile inputs, finished-workout feedback capture, typed nutrition inputs, meal-template/log truth, conservative nutrition ranges, authenticated helper reads, facility-scoped presence/tap-link/streak truth, and the bounded competition authz substrate without a deployment claim |
| APOLLO authz / staff boundary | closure-clean in repo/runtime; deployed truth unchanged | current repo/runtime truth | APOLLO now carries explicit roles, deterministic competition capability checks, trusted-surface-gated staff mutations, and durable actor attribution over the existing competition control boundary without widening into a broader staff product or ATHENA work |
| Public/demo/frontend work | intentionally deferred | Phase 3 concern, not a Phase 2 driver | gated |

## Phase 2 Execution Posture

| Topic | Rule |
| --- | --- |
| Frontend | keep it thin or absent through Phase 2 |
| First truthful surface | CLI, internal HTTP, or bounded operator tooling is enough |
| Tracer design | each tracer should widen one bounded backend capability and leave behind a usable operator/agent surface |
| Demo pressure | defer it to Phase 3; do not back-solve the ladder around a UI |
| Modularity | keep ATHENA physical truth, APOLLO member/competition truth, HERMES staff truth, gateway control truth, and deploy truth separate |
| `ashton-proto` | widen only when a tracer creates a real shared dependency |
| Docs | repo-local docs first, then `ashton-platform` as the control-plane ledger |

## Active Post-Milestone 2.0 Ladder

`Phase 3 shared substrate A` is closed in repo/runtime truth on the later
`athena v0.7.x` line: `v0.7.0` established the substrate, `v0.7.1` closed the
bounded absent-state retention patch, and the later `main` line added a compact
durable projector-miss guardrail without changing replay authority. Deployed
truth remains on immutable `athena v0.7.0`, and that deploy closeout is
separate from the active implementation ladder.

`Phase 3 shared substrate B` is also closed in APOLLO repo/runtime truth on
`main`: schedule resources, resource-graph truth, typed blocks, RFC3339-only
calendar windows, block-timezone recurrence, explicit exceptions, and
active-plus-bookable inventory-claim semantics are now real without widening
deployed truth, public request intake, or UI claims.

`Phase 3A.2` is now closed in `Hestia` repo/runtime truth: standalone member
frontend runtime, same-origin APOLLO bridge, member-only IA, explicit
schedule-out boundary, and repo-local docs/test proof are all on `main`
without reopening APOLLO.

`Phase 3B.2` is now closed in `Themis` repo/runtime truth: standalone
privileged read-only ops shell, same-origin APOLLO bridge, supervisor/manager/
owner overview entry, unsupported-role state for members, degraded ATHENA config
state, and repo-local docs/test proof are all on `main` without reopening
APOLLO, Hestia, ATHENA, HERMES, gateway, deploy, or ashton-platform runtime
truth. Deployed truth is unchanged.

`Phase 3B.4` is now closed in APOLLO and Themis repo/runtime truth: APOLLO owns
booking request persistence, `booking_read` / `booking_manage`, the
requested/under_review/needs_changes/approved/rejected/cancelled state machine,
trusted-surface-gated mutations, expected-version transitions, availability
preview, and conflict-aware approval into linked internal `reservation` /
`hard_reserve` schedule blocks. Themis owns the internal `/ops/bookings`
list/detail/create/decision workspace for supervisor read-only and manager/owner
management. Deployed truth is unchanged.

`Phase 3B.5 approved booking lifecycle` is now closed in APOLLO and Themis
repo/runtime truth: APOLLO can cancel approved booking requests through the
existing trusted-surface-gated, expected-versioned `/cancel` transition,
atomically cancels the linked internal `reservation` / `hard_reserve` schedule
block, retains `schedule_block_id` for audit, and refuses stale booking versions
or linked schedule drift. Themis exposes approved cancellation only to
manager/owner sessions and keeps supervisors read-only. Deployed truth is
unchanged.

`Phase 3B.6 public request entrypoint` is now closed in APOLLO, Themis, and
Hestia repo/runtime truth: APOLLO owns public intake API truth, validation,
source/channel, idempotency, and no-reservation-on-submit behavior; Hestia owns
public booking-request intake at `/intake` plus authenticated member app routes
under `/app/**`; Themis remains privileged ops only and blocks `/api/v1/public/*`
from its broad APOLLO proxy. `ashton-booking-intake` was folded into Hestia and
removed from active repo inventory. Deployed truth is unchanged.

`Phase 3B.7 customer status and communication` is now closed in APOLLO,
Themis, and Hestia repo/runtime truth: APOLLO owns opaque public receipt codes,
public-safe status mapping, unauthenticated status lookup, and separate
public-message storage; Hestia owns `/intake/status` and keeps public APOLLO
calls server-mediated while denying `/api/v1/public/*` through the browser
proxy; Themis remains internal ops only and lets manager/owner users save only
the public-safe message while supervisors stay read-only. Deployed truth is
unchanged.

`Phase 3B.8 booking edit and replacement` is now closed in APOLLO and Themis
repo/runtime truth: APOLLO owns staff-side pending request edit, approved
replacement request lineage, rebook idempotency, and schedule availability
truth; Themis owns the internal manager/owner workflow while supervisors remain
read-only. Hestia remains customer-facing and unchanged. Deployed truth is
unchanged.

`Phase 3B.9 public availability/request calendar` is now closed in APOLLO and
Hestia repo/runtime truth: APOLLO owns the unauthenticated read-only public-safe
availability endpoint for booking options, and Hestia `/intake` shows
requestable windows plus unavailable time hints while submit remains
request-only. Staff approval still creates the only confirmed reservation.
Themis remains untouched. Deployed truth is unchanged.

`Phase 3B.10 bounded staff schedule controls` is now closed in APOLLO and
Themis repo/runtime truth: APOLLO remains the schedule authority, keeps public
availability private-safe, and refuses generic schedule cancellation of
booking-linked reservations. Themis owns the internal schedule page; supervisors
are read-only, and manager/owner users can create supported single-instance
blocks and cancel only eligible future schedule-managed non-reservation blocks.
Hestia remains customer-facing and unchanged. Deployed truth is unchanged.

`Phase 3B.15 ARES v2` is now closed in APOLLO repo/runtime truth:
APOLLO owns canonical result identity, recorded/finalized/disputed/corrected/
voided facts, correction supersession, lifecycle events, finalized/corrected-
only rating consumption, a versioned legacy rating foundation, and internal
OpenSkill comparison rows/events with accepted delta budgets and explicit
delta flags. APOLLO now also owns explicit queue intent facts and internal
ARES match-preview proposal facts with server-computed match quality,
predicted win probability, and explanation codes. ARES remains proposal-only,
and the active rating read path remains the legacy projection. Themis owns the
internal `/ops/competition` shell over APOLLO contracts and does not own
competition result, rating, or preview math state. Hestia remains unchanged.
Deployed truth is unchanged.

`Phase 3B.16 Competition Analytics Foundation` is now closed in APOLLO
repo/runtime truth: APOLLO owns internal derived competition stat events and
analytics projections over finalized/corrected canonical results plus legacy
active rating facts only. Projection version, confidence, sample size, computed
time, source match/result, and deterministic watermarks are explicit. Themis,
Hestia, Prometheus, and deployed truth are unchanged. Dashboard-first
analytics, public profiles/stats/scouting, carry coefficient, OpenSkill
read-path switch, tournament runtime, public competition surfaces,
CP/badges/rivalry/squads, and game identity were deferred at that closure.

`Phase 3B.17 Internal Tournament Runtime` is now closed in APOLLO repo/runtime
truth: APOLLO owns staff/internal tournament containers, single-elimination
bracket and seed facts, immutable team snapshots, match bindings, explicit
advance reasons, audited round advancement facts, and tournament event facts.
Advancement consumes finalized/corrected canonical result truth only and does
not own result, rating, analytics, ARES, public, or game identity truth.
Themis, Hestia, Prometheus, deployed truth, and public tournament surfaces stay
unchanged.

`Phase 3B.19 Public Competition Readiness` is now closed in APOLLO and Hestia
repo/runtime truth: APOLLO owns public-safe readiness and leaderboard projection
contracts over finalized/corrected canonical result truth plus legacy active
rating fields; Hestia renders `/competition` from those APOLLO public-safe
contracts only. Private/internal safety, manager, command, OpenSkill
comparison, ARES proposal, tournament ops, source-result, projection-watermark,
and operational truth remain non-public. Themis stays internal. Prometheus,
deployed truth, public/member safety UI, messaging/chat, public social graph,
CP/badges/rivalry/squads, OpenSkill read-path switch, and SemVer governance
stay unchanged.

`Phase 3B.20 Game Identity Layer` is now closed in APOLLO and Hestia
repo/runtime truth: APOLLO owns public/member-safe CP, badge award, rivalry
state, and squad identity projections over public-safe competition projection
rows only; Hestia renders APOLLO-provided game identity contracts only.
Messaging/chat, broad public social graph, OpenSkill read-path switch, public
safety detail exposure, public tournaments, booking/commercial/proposal
workflows, SemVer governance, Prometheus, and deployed truth stay unchanged.

`Rating Policy Wrapper` is now closed in APOLLO repo/runtime truth: APOLLO
keeps the legacy engine active and records `apollo_rating_policy_wrapper_v1`
with calibration/provisional/ranked status, fifth-match ranked transition,
inactivity sigma-inflation metadata, and climbing-cap metadata. OpenSkill
remains comparison-only, public tournaments remain blocked, deterministic
simulation was closed next, and deployed truth is unchanged.

`Rating Policy Simulation / Golden Expansion` is now closed in APOLLO
repo/runtime truth: deterministic fixtures, `apollo competition rating
simulation --format json`, legacy baseline deltas, OpenSkill sidecar deltas,
accepted/rejected scenario classification, cutover blockers, and policy risks
are real. OpenSkill remains comparison-only, production historical backtesting
remains deferred, public tournaments remain blocked, and deployed truth is
unchanged.

The active next ladder is now a post-matrix planning fork:

| Order | Line | Repo focus | Release line | Purpose | Hard stop |
| --- | --- | --- | --- | --- | --- |
| 1 | Frontend Route/API Contract Matrix | Hestia/Themis/platform/APOLLO docs | closed docs packet | freeze route, API, auth, state, and stub status before broader surface work | closed docs truth only; no runtime/deployed claims |
| 2 | Game Identity Policy Tuning Loop | APOLLO/platform docs plus bounded APOLLO proof if opened | later policy packet | tune CP/badge/rivalry/squad policies against real evidence without widening product surface | no frontend-owned formulas, public tournaments, OpenSkill active read path, public/member safety UI, or deployed truth claims |
| 3 | `Phase 3C` | cross-product | later | presentation, identity, packaging, and assistant presentation | no persona-first product before trustworthy rails |

## Historical Phase 2 Ladder

`Cleanup 0` is complete in this docs sync pass. It is intentionally docs-only
and not a tagged runtime line.

`Tracer 17` is now the current shipped closeout line for the richer HERMES
reconciliation surface and bounded ATHENA support release alignment.

| Platform tag | Vertical | Repo lines in scope | Intended purpose | Hard stop |
| --- | --- | --- | --- | --- |
| `v0.0.21` | `Milestone 1.7` | `hermes v0.1.2` only if runtime changes later, otherwise deploy-only closeout | live HERMES deployment proof over a bounded `Prometheus` runner slice | no write authority, public ingress, or broad assistant maturity |
| `v0.0.22` | `Tracer 15` | `ashton-mcp-gateway v0.2.0`, optional `ashton-proto v0.4.0` | caller identity, persisted audit, and second routed read | no write approvals yet |
| `v0.0.23` | `Tracer 16` | `athena v0.5.0` | durable edge-observation groundwork and ingress hardening | no prediction or broad rollout claims |
| `v0.0.24` | `Tracer 17` | `hermes v0.2.0`, `athena v0.5.1` | one richer read-only operator/reconciliation question | no overrides or writes |
| `v0.0.25` | `Tracer 18` | `athena v0.6.0` | facility catalog, hours, zones, closure windows, and per-facility metadata read surfaces | no social or product logic |
| `v0.0.26` | `Tracer 19` | `apollo v0.10.0` | sport registry for at least two sports, facility-sport capability mapping, and basic sport rules/config | no matchmaking runtime yet |
| `v0.0.27` | `Tracer 20` | `apollo v0.11.0` | team, roster, session, and match container primitives | no public standings |
| `v0.0.28` | `Tracer 21` | `apollo v0.12.0` | matchmaking / queue / assignment flow and session lifecycle | no rivalry or badge logic |
| `v0.0.29` | `Tracer 22` | `apollo v0.13.0`, optional `ashton-proto` widening only if result or rating contracts become truly shared | result capture, ratings, rudimentary standings, and member profile stats | no broad public social layer |
| `v0.0.30` | `Tracer 23` | `apollo v0.14.0` | planner, exercise library, templates/loadouts, and richer profile inputs as backend/CLI-first truth on `main` | no meaningful frontend widening |
| `v0.0.31` | `Tracer 24` | `apollo v0.15.0` | deterministic coaching substrate over planner, profile, and workout history | no diagnosis or opaque black box |
| `v0.0.32` | `Tracer 25` | `apollo v0.16.0` | conservative nutrition substrate with meal logging and calorie/macro ranges | no diagnosis or obsessive nutrition sprawl |
| `v0.0.33` | `Tracer 26` | `apollo v0.17.0` | explanation, summarization, bounded AI helper flows, and thin agent-facing helper surfaces over stable deterministic logic | no public social feed, no LLM-first core, no frontend-first pivot |
| `v0.0.34` | `Tracer 27` | `apollo v0.18.0` | member presence, tap-link, and streak substrate over explicit visit truth | no fake streak counters or silent visit inference |
| `v0.0.35` | `Tracer 28` | `apollo v0.19.0` | role/authz, actor attribution, trusted-surface primitives, and staff-runtime boundary substrate | no polished ops suite or speculative contract widening |
| `v0.0.36` | `Milestone 2.0` | `apollo v0.19.1`, `athena v0.6.1`, `ashton-mcp-gateway v0.2.1`, and docs-only `ashton-platform` closeout; `Prometheus` unchanged unless deploy truth changes | Phase 2 backend/base plateau: structural pillars, competition base, planner/coaching/nutrition/presence/authz substrate, deploy truth, actor-safe command/proposal posture, and docs aligned | not a broad demo milestone |
| later than `Milestone 2.0` | long-horizon ladder in `planning/STARSHOT-VISION.md` | Phase 3 demos and later system-proof | meaningful frontend, demos, and broader presentation only after the backend/base ladder is closed cleanly | do not widen frontend during Phase 2 |

## Historical Phase 2 Outcomes

| Line | Concrete outcome | Why it matters |
| --- | --- | --- |
| `Tracer 14` | HERMES logs request, result, and outcome clearly with low-noise structured logs | makes the staff slice operationally inspectable |
| `Milestone 1.7` | live HERMES occupancy read was deployed and verified through a bounded internal runner slice | upgrades the staff pillar from local truth to bounded deployment truth |
| `Tracer 15` | gateway gains caller identity, persisted audit, and one second routed read | turns the control plane from a demo route into a trusted narrow layer |
| `Tracer 16` | ATHENA gains durable edge-observation groundwork | removes all-memory dependence and sets up operator history / reconciliation groundwork |
| `Tracer 17` | HERMES answers one richer operator/reconciliation question from stable public upstream truth | creates the first real operator / reconciliation read surface |
| `Tracer 18` | ATHENA publishes facility catalog, hours, zones, and closure metadata cleanly | gives the rest of the platform trustworthy facility truth to build on |
| `Tracer 19` | APOLLO gains sports and facility-sport capability truth for at least two sports | creates the first honest competition substrate |
| `Tracer 20` | APOLLO gains team, roster, session, and match container primitives | gives competition runtime a real container model instead of preview-only logic |
| `Tracer 21` | APOLLO gains matchmaking / queue / assignment flow and session lifecycle | makes execution truth real beyond preview |
| `Tracer 22` | APOLLO gains results, ratings, standings, and member stats | turns competition history into usable truth |
| `Tracer 23` | APOLLO gains planner and richer profile inputs as backend-first truth | adds the workout/planning substrate without dragging Phase 2 into frontend work |
| `Tracer 24` | APOLLO gains deterministic coaching substrate | connects profile, planner, and workout history without risky claims |
| `Tracer 25` | APOLLO gains conservative nutrition substrate | adds nutrition truth and meal logging without turning the product into a diet app |
| `Tracer 26` | APOLLO gains explanation and agent-facing helpers over stable deterministic logic | improves agent/operator usability without making the model the core engine |
| `Tracer 27` | APOLLO gains member-facing visit/tap-link/streak truth | turns presence into an honest product surface instead of a fake badge layer |
| `Tracer 28` | APOLLO gains explicit role/authz, actor attribution, and staff-runtime boundary substrate | makes later ops UI and agent approvals honest instead of implied |
| `Milestone 2.0` | the backend/base ladder is closure-clean across repos, deploy truth, docs, and actor-safe command discipline | turns the platform from a pile of tracers into a modular backend plateau ready for Phase 3 demos |
| `System-Proof Milestone` | system-level proof of runtime truth, deployment truth, modularity, and maintenance model | shifts the platform from a pile of tracers to a coherent system |

## Historical Phase 2 Scope Bounds

| Line | In scope | Out of scope |
| --- | --- | --- |
| `Tracer 15` | caller identity for routed reads, persisted audit, and one second routed read | write approvals, broad orchestration, auth-platform rewrite |
| `Tracer 16` | durable edge observation history, rebuild / replay groundwork, and privacy-safe history proof | prediction rollout, override tooling, identity reconciliation UI |
| `Tracer 17` | one richer read-only operator question, occupancy reports, and heat-map style reads sourced from stable upstream truth | writes, overrides, private DB shortcuts, broad assistant UX |
| `Tracer 18` | facility catalog, hours, zones, closure windows, and per-facility metadata reads | social logic, matchmaking logic, public product UX |
| `Tracer 19` | sports, facility-sport capability mapping, and basic sport rules/config for at least two sports | matchmaking runtime, standings, public read surfaces |
| `Tracer 20` | team, roster, session, and match container primitives | public standings, badges, broad social layer |
| `Tracer 21` | queueing, assignment, lifecycle transitions, and deterministic session control flow | rivalry/badge logic, public ranking, broad UI |
| `Tracer 22` | results, ratings, rudimentary standings, and member profile stats | broad public social reads, public homepage, fake highlight layer |
| `Tracer 23` | planner, exercise library, templates/loadouts, and richer profile inputs as backend/CLI-first truth | meaningful frontend widening, medical claims, opaque recommendation logic |
| `Tracer 24` | deterministic conservative recommendations, feedback capture, progression logic, and explicit limitations | diagnosis, clinical advice, opaque model outputs, meal runtime |
| `Tracer 25` | conservative calorie / macro ranges, nutrition profile, low-friction meal logging, and explicit limitations | diagnosis, clinical advice, obsessive nutrition sprawl, chatbot-first guidance |
| `Tracer 26` | explanation, summarization, bounded AI helper reads, and thin support tooling over stable deterministic logic | public feed, social sharing, LLM-first core decisions, frontend-first product work |
| `Tracer 27` | member-facing tap-link, explicit visit association, streak state, and streak events | fake streak counters, silent visit inference, cross-facility ambiguity |
| `Tracer 28` | explicit roles, capability checks, actor attribution, and trusted-surface primitives for later approval workflows | polished ops product, persistent approval/proposal objects, speculative shared contracts, broad admin manipulation tooling |
| `Phase 3B.1` | APOLLO-owned read-only ops overview over schedule truth plus ATHENA current occupancy and bounded aggregate analytics | booking, public entrypoints, staff shell UI, owner policy writes, HERMES widening, gateway work, deploy claims, raw identity/tap leakage |

## Platform Guardrails

| Topic | Recommendation |
| --- | --- |
| Frontend | keep it thin or absent through Phase 2; CLI/internal/operator surfaces count as real |
| Demo pressure | defer to Phase 3; do not warp tracer boundaries around presentation |
| `ashton-proto` | widen only when a real shared contract is needed by a tracer |
| ATHENA signals | co-presence, dwell, regularity, and routine may exist as internal analytics; rivalry and teammate claims do not belong there |
| APOLLO social | public standings, social showcase, crews, and DMs stay later than the backend/base ladder |
| BMI | use as one coarse input only, not the main driver |
| Calories | keep them conservative, average-range, and clearly non-medical |
| Nutrition posture | this is not a diet app; it logs, tracks, and suggests simple ranges only |
| LLM | use later to explain recommendations, not compute the core decision first |
| OpenSkill | good fit for solo, team, and mixed sports if ratings are separated by sport and mode |
| Rating model | separate by sport and mode, not one global score |
| Rivalries | pairwise counters plus recency plus streaks, derived from real results |
| Public leaderboards | later only, and only after trusted results plus anti-abuse basics |
| Social | keep it opt-in and lightweight; DMs and achievement showcases can come before any public feed |

## Agentic-Native Target

By the end of Phase 2, the backend should be agent-friendly in a tool-shaped,
auditable way rather than a freeform chat-owned way.

| Capability | Expected home | Why it matters |
| --- | --- | --- |
| typed helper read models | `Tracer 26` | agents should inspect stable deterministic truth without scraping UI |
| typed proposal objects | `Tracer 24` through `Tracer 26` | coaching and nutrition changes should be structured, previewable, and reviewable |
| explicit plan diffs | `Tracer 24` and later | planner truth should change through visible proposals, not silent mutations |
| dry-run / preview semantics | `Tracer 26` and later | important mutations should be inspectable before apply |
| actor attribution and capability checks | `Tracer 28` | agent-originated actions need a real authority boundary |
| audit-friendly apply steps | `Tracer 28` | approval and execution should stay explainable after the fact |
| idempotency and optimistic concurrency | `Tracer 28` and later | agent writes should fail safely instead of duplicating or racing |
| CLI parity with HTTP over shared services | `Milestone 2.0` proof | the system should be toolable without creating a second source of truth |

## Historical Phase 2 Proof Plan

| Line | Minimum proof |
| --- | --- |
| `Tracer 14` | repeated HERMES runs, success/failure logs, no runtime widening |
| `Milestone 1.7` | rollout proof, live occupancy read, cluster smoke, safe negative proofs, docs aligned |
| `Tracer 15` | repeated routed reads, caller identity proof, persisted audit proof, second route proof |
| `Tracer 16` | durable-history groundwork behaves deterministically, restart / reload story is explicit, and no raw-ID leakage regresses |
| `Tracer 17` | richer question is source-backed, deterministic, read-only, and operationally useful |
| `Tracer 18` | invalid facility metadata, bad hours windows, missing zones, and closure-window conflicts fail cleanly without corrupting existing occupancy truth |
| `Tracer 19` | bad sport rules, invalid facility-sport mappings, and incompatible sport/facility combinations reject cleanly and repeatably |
| `Tracer 20` | duplicate team/session creation, invalid transitions, roster conflicts, and ownership/auth failures reject cleanly |
| `Tracer 21` | duplicate assignment, stale queue state, invalid lifecycle transitions, and replay attempts fail deterministically |
| `Tracer 22` | tampered results, per-sport separation, stale standings recompute, and anti-garbage-data checks all behave deterministically |
| `Tracer 23` | invalid exercise or machine IDs fail cleanly, impossible planner states are rejected, templates stay ownership-safe, and planner writes do not mutate unrelated domains |
| `Tracer 24` | extreme stats, missing history, cold starts, and deterministic reruns all behave conservatively without medical overclaiming |
| `Tracer 25` | dietary restrictions, budget/cooking constraints, missing nutrition history, and conservative reruns behave safely without medical overclaiming |
| `Tracer 26` | explanation/helper output remains traceable to deterministic core logic and never invents unsupported advice |
| `Tracer 27` | correct visit/tap linking, no spoofed presence, correct streak transitions, and no fake inferred streak mutation |
| `Tracer 28` | role escalation safety, trusted-surface gating, actor attribution, auditability, and no unauthorized staff/member cross-domain mutation |
| `Milestone 2.0` | repo audit plus deploy audit plus doc alignment plus CLI/internal surface coherence plus actor/capability safety coherence prove the backend/base plateau cleanly |
| `System-Proof Milestone` | repo audit plus deployment audit plus boundary audit plus post-tracer roadmap |

## System-Proof Target

| Area | What “40% done” should mean |
| --- | --- |
| Runtime | each major repo has one or more real, bounded, trustworthy slices |
| Deployment | physical truth and staff truth each have live proved boundaries |
| Control plane | gateway is narrow but real, caller-aware, and auditable |
| Modularity | ATHENA physical truth, APOLLO member truth, HERMES staff truth, and gateway control truth stay separate |
| Maintenance | runbooks, CLIs, tests, and hardening artifacts let future work land without collapsing boundaries |
| Agentic workflow | future agents can work repo by repo with narrow prompts, hardening discipline, and clear source-of-truth docs |

## Historical Phase 2 Immediate Next Steps

1. Treat `v0.0.19` as the shipped bounded ATHENA deployment closeout line.
2. Treat `Tracer 14` as closure-clean on `main` and keep it strictly observability-only.
3. Use `Milestone 1.7` to record the same HERMES slice as bounded live deployment truth without widening runtime scope.
4. Use `Tracer 15` to strengthen gateway trust before any approval or write widening.
5. Treat `Tracer 16` and `Tracer 17` as shipped and use `Tracer 18` to finish facility truth before product/base widening.
6. Use `Tracer 18` through `Tracer 22` to finish the operational and competition base before planner/coaching work.
7. Use `Tracer 23` through `Tracer 28` to finish the backend/agent-friendly product expansion without turning Phase 2 into a frontend push.
8. Treat `Tracer 27` and `Tracer 28` as required backend plateau lines, not optional product polish.
9. Treat `Milestone 2.0` as the backend/base plateau after all remaining tracers, not as a demo milestone.
10. Treat Phase 3 as the first meaningful demo/front-end era and keep the later system-proof milestone behind that.

## Decision Docs

Use these as the current decision docs for frontend gap discovery and
coaching/AI shaping:

- `planning/envisioning/ui-truth-consolidation.md`
- `planning/envisioning/coaching-nutrition-ai-architecture.md`

These docs are allowed to shape page, tracer, and model sequencing, but they do
not override current runtime truth in repo READMEs, roadmaps, or hardening
evidence.

Decision rule:

- `ui-truth-consolidation.md` is the buildability arbiter for UI work
- `coaching-nutrition-ai-architecture.md` is the buildability arbiter for
  coaching, nutrition, AI-helper, and agentic-shaping work

## Milestone 1 Deployment Truth

Milestone 1 closes on Option A:

- deployed truth means live ATHENA read-path verification
- deployed truth does not mean the full live `ATHENA -> NATS -> APOLLO`
  cluster boundary yet
- the live event boundary is a separate bounded deployment workstream, not an
  implicit extension of Tracers 0 through 4

Operator rule:

- do not describe the in-cluster event path as live until ATHENA publish
  config, any required broker wiring, APOLLO deployment, and live end-to-end
  verification are all real

## Milestone 1.5 Deployment Truth

Deployment Workstream A is now complete as a bounded deepening pass:

- `ATHENA` now publishes identified arrivals to live in-cluster NATS
- `NATS` is live in the `agents` namespace as the bounded broker for this path
- `APOLLO` is live in-cluster with init-time migrations, NATS consumer wiring,
  and Postgres persistence
- one live arrival created the expected APOLLO visit
- replay of that same live arrival resolved deterministically as `duplicate`

Operator rule:

- this does not yet imply a broad APOLLO product deployment
- this does not yet imply live departure-close proof in-cluster

That deferred boundary is now closed in Milestone 1.6 below.

## Milestone 1.6 Deployment Truth

Deployment Workstream B is now complete as a bounded deepening pass:

- `ATHENA` now proves the live identified departure publish path in-cluster
  using the deployed mock-backed ingress line
- `NATS` carries the live departure subject bytes
- `APOLLO` consumes the live departure and closes exactly one matching open
  visit in Postgres
- replay of that same live departure resolves deterministically as `duplicate`
- `apollo.workouts` and sampled unrelated member state remain unchanged

Operator rule:

- this does not imply broad APOLLO product deployment
- Milestone 1.6 itself did not imply live source-backed ATHENA ingress deployment
- that later bounded boundary now closes separately in the ATHENA edge deployment workstream

## Test Discipline

Every tracer should keep the same delivery standard:

- small commits
- tests before deploy changes
- smoke tests for containers
- rendered-manifest validation before GitOps changes
- more edge-case tests before widening the tracer

## Local Tooling Notes

- `kubectl` is installed on the MacBook and can render Kustomize overlays via `kubectl kustomize`
- `kustomize` should also be installed locally so manifests can be validated directly
- a missing kube context is a local environment problem, not a repo problem
- do not mark a GitOps rollout as verified until `kubectl rollout status` or Flux reconciliation is confirmed from a real cluster context
