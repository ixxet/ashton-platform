# ASHTON Star-Shot Vision

This document is the long-range planning horizon for ASHTON.

It is not the runtime source of truth.
It is not a release ledger.
It is the place to keep the wide vision coherent while keeping each tracer
execution narrow, bounded, and testable.

## Thesis

| Area | Position |
| --- | --- |
| Vision style | wide vision, narrow tracer execution |
| Delivery style | formal pre-`1.0.0` semantic versioning, granular commits, push clean `main`, destructive proof |
| Immediate pressure | finish the backend/base pillars before meaningful demos or frontend work |
| Core rule | every tracer may widen one meaningful boundary, but it must also say what it will not do |
| Phase 2 posture | backend-first, CLI-first, modular, anti-frontend-sprawl |
| Phase 3 posture | demos and front-end presentation only after the backend/base ladder closes cleanly |

## Urgency Snapshot

| Area | State now | Meaning | Urgency |
| --- | --- | --- | --- |
| ATHENA deploy truth | done | physical-truth pillar is live and credible | done |
| APOLLO auth / membership / preview | done | member intent pillar exists | done |
| HERMES | observability-hardened and live-deployed | staff truth is real locally and in one bounded internal deployment slice | done |
| Gateway | caller-aware and auditable, still narrow | the routed control-plane layer is real even though write governance remains deferred | medium |
| ATHENA durability | shipped, deployed truth unchanged | durable-history groundwork is real and tagged, and the bounded Tracer 17 support follow-up is shipped without widening deployment truth | done |
| HERMES operator surface | shipped, still intentionally narrow | one richer reconciliation question is real and tagged while broader workflows remain deferred | done |
| Facility truth beyond occupancy | closure-clean on `main`, deployed truth unchanged | facility catalog, hours, zones, and closure metadata now have a clean ATHENA-owned home without widening deployment truth | done |
| Competition execution and history runtime | closure-clean in repo/runtime; deployed truth unchanged | sport registry, facility-sport capability mapping, queue/assignment/lifecycle truth, team/roster/session/match execution containers, immutable result capture, sport-and-mode ratings, session-scoped standings, self-scoped member stats, and the planner substrate now exist without widening into public competition reads | done |
| Planner / coaching / nutrition / presence / authz backend | current repo/runtime truth is real through the Tracer 28 authz line on `main` | planner, templates/loadouts, richer profile inputs, finished-workout coaching feedback, deterministic coaching proposals, typed nutrition inputs, meal-template/log truth, conservative nutrition ranges, bounded helper reads, facility-scoped presence/tap-link/streak truth, and the bounded competition authz substrate are now real on `main` while broader staff product and deployment claims remain deferred | done |
| Meaningful frontend | intentionally deferred | Phase 3 concern, not a Phase 2 driver | gated |

## Current Truth

| Area | State | Meaning | Next |
| --- | --- | --- | --- |
| `Tracer 11` | shipped | APOLLO member shell is real, but intentionally thin | done |
| `Tracer 12` | shipped | explicit lobby membership and durable member intent are real | done |
| `Tracer 13` | shipped | deterministic read-only ARES preview is real | done |
| `ATHENA runtime` | `v0.5.1` shipped; the Tracer 18 facility-truth line plus the `v0.6.1` hardening follow-up are on `main`; `v0.7.0` is now live | durable history, facility truth, Postgres-backed observations, derived sessions, and bounded internal analytics are real in repo/runtime and on the bounded live deploy line | done |
| `ATHENA deployment closeout` | earlier `Prometheus v0.0.3` / `ashton-platform v0.0.19` closeout remains historical; current live cluster runs `athena v0.7.0` | bounded live deploy truth is real | done |
| `APOLLO member runtime` | `v0.9.0` shipped | auth, visits, workouts, recommendations, membership, and deterministic preview are real | done |
| `APOLLO backend/runtime` | current Tracer 28 repo/runtime closeout line plus the later `Phase 3 shared substrate B` line are on `main`; deployed truth unchanged | sport registry, facility-sport capability mapping, queue/assignment/lifecycle truth, team/roster/session/match execution containers, immutable result capture, sport-and-mode-separated ratings, session-scoped standings, self-scoped member stats, the planner/deterministic-coaching plus bounded nutrition/helper and facility-scoped presence surfaces, the bounded competition role/authz substrate, and the first real scheduling substrate are real while public competition reads, public booking, and broader staff product remain deferred | done |
| `HERMES` | `v0.2.0` shipped | one thin staff read plus one richer reconciliation read are real | done |
| `Gateway` | current Tracer 15 line real on `main` | control plane is real, caller-aware, and still intentionally narrow | later gateway widening only if justified |
| `Platform docs` | synced to current repo truth and release lines | control-plane planning truth now matches the later `athena v0.7.0` deploy line plus current APOLLO `v0.19.1` and shared substrate B closeout truth without widening deployed claims | keep synced as work lands |

## What Phase 2 Is For

Phase 2 is the era where ASHTON finishes its backend/base pillars.

It should leave behind:

- durable physical truth
- trustworthy operator truth
- auditable control-plane truth
- facility and sport truth
- session, result, rating, and stats truth
- planner, coaching, nutrition, presence, and role/authz backends that are still modular and deterministic
- typed helper reads, proposal objects, and approval rails that later AI and agents can use safely
- CLI/internal surfaces that agents can work with directly

Phase 2 should not be judged by:

- how polished the UI is
- how broad the public surface is
- how pretty the demo looks

Those become Phase 3 concerns.

## Platform Shape

| Pillar | Real now | Strong after Phase 2 |
| --- | --- | --- |
| Physical truth runtime + deploy | yes | yes |
| Cross-repo lifecycle event boundary | yes | yes |
| Member truth runtime | yes | yes |
| Member shell | yes, thin | yes, still intentionally thin |
| Explicit member intent | yes | yes |
| Deterministic coordination preview | yes | yes |
| Staff read surface | yes, thin | yes, stronger |
| Staff observability | yes | yes |
| Staff live deployment truth | yes, bounded | yes |
| Control-plane routed read | yes, thin | yes |
| Control-plane identity + audit | yes, narrow | yes |
| Durable edge observation history | yes | yes |
| Operator review / reconciliation read surface | yes, narrow | yes |
| Facility catalog / hours / zones | yes | yes |
| Sport / team / session / result substrate | yes | yes |
| Planner / coaching / nutrition substrate | partial | yes |
| Presence / tap-link / streak substrate | yes | yes |
| Role/authz and staff boundary substrate | yes | yes |
| Agentic proposal/apply rails | partial | yes |
| Public/demo/frontend polish | no | still deferred |

## Building Analogy

| Building piece | ASHTON meaning | What it does |
| --- | --- | --- |
| concrete slab | ATHENA deploy truth | the part that must hold weight before anything else stands |
| framing | APOLLO member truth | gives the interior shape for identity, workouts, preview, and later sports |
| plumbing | NATS and event flow | moves real state between rooms without leaking abstraction |
| wiring | gateway and control plane | lets operators and clients reach the right surfaces safely |
| mechanical room | deploy, audit, history, runbooks | hidden but essential systems work |
| staff office | HERMES | inspect, review, reconcile, and observe without mutating everything |
| competition floor | sports, teams, sessions, ratings | where execution truth becomes real |
| training floor | planner, templates, coaching, nutrition, and presence rails | later than the competition substrate in the new Phase 2 order |
| showroom | demos and front end | Phase 3 only |

## Execution Rules

| Rule | Requirement |
| --- | --- |
| pre-`1.0.0` semver discipline | new bounded runtime capability or breaking pre-`1.0.0` surface = `minor`; hardening, docs sync, deploy closeout, observability, and bounded fixes with no new capability = `patch` |
| commits | make granular commits aligned to one slice of truth at a time |
| push | push clean `main` after closure-clean state; do not leave repo truth local |
| branching | do not leave closure claims on side branches |
| hardening | destructive checks are mandatory, not optional |
| truth split | always separate local truth, deployed truth, and deferred truth |
| scope lock | every tracer must say what it will not do |
| docs | update repo-local docs first, then `ashton-platform` as the control-plane ledger |
| CLI-first posture | CLI, internal HTTP, or bounded operator tooling is enough to make a tracer real |
| frontend posture | keep it thin or absent through Phase 2 |
| LLM use | use later for explanation or summarization, not as the first opaque decision engine |

## Standing Doctrines

These are not separate roadmap items.
They are always-on doctrines.

| Doctrine | Meaning | Trigger marker |
| --- | --- | --- |
| release discipline | semver, tags, release notes, repo/doc alignment | any new runtime claim, public boundary change, or closeout-ready line |
| deployment discipline | split deploy truth honestly, add deploy slices intentionally, pin critical images | any change to a deployed repo or any new live claim |
| contract discipline | widen `ashton-proto` only when a real shared dependency exists | second producer/consumer, shared schema change, or new public contract |
| hardening discipline | destructive reruns, smoke reruns, `race`/`vet` where appropriate, truth splits | every tracer closeout and every risky line |

## CLI And Agent Frames

Phase 2 should leave behind surfaces that agents can reason over directly.

| Repo | Desired Phase 2 frame |
| --- | --- |
| `athena` | CLI/internal reads for observations, sessions, facilities, hours, and metadata |
| `hermes` | CLI/operator reads for occupancy, heat maps, reports, and reconciliation |
| `apollo` | API/CLI-first sport, team, session, result, planner, coaching, presence, and approval-capable primitives |
| `ashton-mcp-gateway` | actor-aware routed reads and persisted audit |
| `Prometheus` | deploy/runbook truth, not product logic |

The exact commands can evolve, but the principle should stay stable:

- agents should not need a thick UI to inspect or harden Phase 2 truth

## Agentic-Native Phase 2 Target

By the end of Phase 2, ASHTON should be agent-friendly because the backend is
tool-shaped and auditable, not because a chat endpoint owns the product.

The target is:

- typed helper read models
- typed proposal objects
- explicit plan diffs
- dry-run or preview semantics for important mutations
- actor attribution and capability checks
- audit-friendly apply steps
- idempotent, race-safe writes
- CLI and HTTP parity over shared domain services

## Table Placement Map

| Table family | Central home | Repo-grounded home | Why |
| --- | --- | --- | --- |
| urgency, long ladder, feature bank, doctrine framing | `planning/STARSHOT-VISION.md` | none required by default | this is the one place where the whole vision should stay together |
| release lines, next execution order, proof ladder, versioning guardrails | `planning/IMPLEMENTATION-BOARD.md` | repo roadmaps reference their local slice | this is the operational ledger for what is next and how it closes |
| runtime surfaces, current real state, shipped lines | `README.md` in each repo | same file | landing pages should ground reality, not carry the whole star-shot |
| next ladder role and local hard stops | repo `docs/roadmap.md` | same file | each repo should know its next narrow job without reading the whole platform plan |
| tracer closure details and historical hardening truth | `planning/sprints/TRACER-MATRIX.md` and repo hardening docs | repo hardening files | closure evidence should stay near the tracer, not in vision docs |
| deployment-closeout truth | deployment repo docs plus `ashton-platform` ledger | deployment repo README / runbooks | deploy truth needs its own source, not just product planning tables |

## Feature Bank

| Feature | Bucket | Repo owner | Needs real history first? | Earliest sane point | Why / boundary |
| --- | --- | --- | --- | --- | --- |
| HERMES request / result / outcome logger | Must have | `hermes` | no | `Tracer 14` | makes the current staff slice inspectable |
| Gateway caller identity + persisted audit | Must have | `ashton-mcp-gateway` | no | `Tracer 15` | needed before broader routing or any later write governance |
| Durable ATHENA edge observation history | Must have | `athena` | no | `Tracer 16` | removes all-memory dependence and enables rebuild / review |
| Richer operator review / reconciliation read | Must have | `hermes` | yes | `Tracer 17` | turns HERMES into a real operator surface |
| Facility catalog / hours / zones / closure windows | Must have | `athena` | no | `Tracer 18` | trustworthy facility truth should exist before sport/session logic builds on it |
| Sport registry and facility-sport capability mapping | Must have | `apollo` | no | `Tracer 19` | creates the first honest competition substrate |
| Team / roster / session / match containers | Must have | `apollo` | no | `Tracer 20` | makes execution truth possible beyond preview |
| Matchmaking queues and assignment flow | Must have | `apollo` | yes | `Tracer 21` | wait for real session containers first |
| Result capture, ratings, standings, and member stats | Must have | `apollo` | yes | `Tracer 22` | required before truthful competition reads |
| Workout planner / weekly plan / machine dropdowns | Must have | `apollo` | no | `Tracer 23` | move planner after the competition/operations base is real |
| Exercise library / templates / richer profile inputs | Must have | `apollo` | no | `Tracer 23` | planner and recommendation substrate |
| Deterministic coaching substrate | Must have | `apollo` | partial | `Tracer 24` | no opaque or medical core |
| Conservative nutrition substrate | Must have | `apollo` | no | `Tracer 25` | no diagnosis, no obsessive nutrition sprawl |
| Explanation and agent-facing helper layer | Good later | `apollo` | yes | `Tracer 26` | explanation layer only, not the decision engine |
| Member-facing tap-link and streak substrate | Must have | `apollo` | yes | `Tracer 27` | presence should become explicit product truth, not a fake badge |
| Role/authz and staff-runtime boundary substrate | Must have | `apollo` | no | `Tracer 28` | required before a truthful ops shell or broader agent approvals |
| Agent-safe proposal/apply rails | Must have | `apollo` | no | `Tracer 28` and later | agents should propose and preview changes, not bypass domain validation |
| Rivalries, badges, rematch prompts, public highlights | Good later | `apollo` | yes | later than `Tracer 22` and likely Phase 3+ | depends on trusted competition truth |
| Public leaderboards | Dangerous too early | `apollo` | yes | later than `Tracer 22` | requires trusted results and anti-abuse basics |
| Full social feed / chat | Dangerous too early | `apollo` | no | later than real product traction | high complexity, moderation burden, low foundation value |

## Product Ordering

| If you want a stable backend before a front end | Best order | Why |
| --- | --- | --- |
| structural pillars first | `14 -> 1.7 -> 15 -> 16 -> 17` | finish weak platform pillars first |
| operational / competition base next | `18 -> 19 -> 20 -> 21 -> 22` | gives the platform honest facility, sport, session, result, and standings truth |
| planner / coaching / nutrition / AI-helper / presence / authz backend after that | `23 -> 24 -> 25 -> 26 -> 27 -> 28` | keep fitness, presence, and staff-boundary logic real, but do it after the operations/competition base exists |
| demos and frontend later | Phase 3 only | do not let presentation pressure distort Phase 2 modularity |

## Active Post-Milestone 2.0 Ladder

`Phase 3 shared substrate B` is now closed in APOLLO repo/runtime truth on
`main`: schedule resources, resource-graph truth, typed blocks, RFC3339-only
calendar windows, block-timezone recurrence, explicit exceptions, and
active-plus-bookable inventory-claim semantics are real without widening
deployed truth or public booking.

| Line | Repo focus | Release line | Purpose | Hard stop |
| --- | --- | --- | --- | --- |
| `Phase 3A.2` | later frontend repo | after closed `Phase 3A.1` | frontend repo bootstrap over already-real member APIs and the closed APOLLO member-shell foundation, reopening APOLLO only if the audit proves one tiny helper is needed | no fake booking UI, no staff-shell drift, and no APOLLO shell reopen as the main line |
| later APOLLO authz/admin widening only if earned | `apollo` | later | add a distinct `admin` role only if real runtime/product needs justify it, then let admin do owner-like graph work intentionally | no accidental role widening hidden inside frontend bootstrap |
| `Phase 3B` | `apollo` plus later `hermes` when earned | later | supervisor/manager/owner ops product over the settled role/authz and scheduling substrate | no broad admin blob or fake operational truth |
| `Phase 3C` | cross-product | later | presentation, identity, packaging, and assistant presentation only after member and ops truth are real | no persona-first product before trustworthy rails |

## Historical Phase 2 Ladder

| Line | Repo focus | Release line | Purpose | Hard stop |
| --- | --- | --- | --- | --- |
| `Cleanup 0` | `ashton-platform` | patch-only docs | align docs with shipped truth and remove stale wording | docs only |
| `Tracer 14` | `hermes` | `v0.1.1` / `v0.0.20` | observability hardening for the current occupancy slice | no richer questions, no writes |
| `Milestone 1.7` | `hermes` + deploy repo | `v0.1.2` if runtime changes, otherwise deploy closeout only | prove live HERMES deployment truth | no write authority |
| `Tracer 15` | `ashton-mcp-gateway` + optional `ashton-proto` | `v0.2.0` / `v0.0.22` | caller identity, persisted audit, second routed read | no approvals/writes yet |
| `Tracer 16` | `athena` | `v0.5.0` / `v0.0.23` | durable edge-observation history and rebuild groundwork | no prediction rollout |
| `Tracer 17` | `hermes` + bounded `athena` support | `v0.2.0` / `v0.5.1` / `v0.0.24` | one richer read-only operator/reconciliation question over stable truth plus the minimum privacy-safe support follow-up | no overrides/writes |
| `Tracer 18` | `athena` | `v0.6.0` / `v0.0.25` | facility catalog, hours, zones, closure windows, and per-facility metadata reads | no social or product logic |
| `Tracer 19` | `apollo` | `v0.10.0` / `v0.0.26` | sport registry, facility-sport capability mapping, and basic sport rules/config for at least two sports | no matchmaking runtime yet |
| `Tracer 20` | `apollo` | `v0.11.0` / `v0.0.27` | team, roster, session, and match container primitives | no public standings |
| `Tracer 21` | `apollo` | `v0.12.0` / `v0.0.28` | matchmaking / queue / assignment flow and session lifecycle | no rivalry or badge logic |
| `Tracer 22` | `apollo` | `v0.13.0` / `v0.0.29` | result capture, ratings, rudimentary standings, and member profile stats | no broad public social layer |
| `Tracer 23` | `apollo` | `v0.14.0` / `v0.0.30` | planner, exercise library, templates/loadouts, and richer profile inputs as backend/CLI-first truth | no meaningful frontend widening |
| `Tracer 24` | `apollo` | `v0.15.0` / `v0.0.31` | deterministic coaching substrate over planner, profile, and workout history | no diagnosis, no opaque core |
| `Tracer 25` | `apollo` | `v0.16.0` / `v0.0.32` | conservative nutrition substrate with meal logging and calorie/macro ranges | no diagnosis or obsessive nutrition sprawl |
| `Tracer 26` | `apollo` | `v0.17.0` / `v0.0.33` | explanation, summarization, bounded AI helper flows, and thin agent-facing helper surfaces over stable deterministic logic | no public social feed, no LLM-first core, no frontend-first pivot |
| `Tracer 27` | `apollo` | `v0.18.0` / `v0.0.34` | member presence, tap-link, and streak substrate over explicit visit truth | no fake streak counters or silent visit inference |
| `Tracer 28` | `apollo` | `v0.19.0` / `v0.0.35` | role/authz, actor attribution, trusted-surface primitives, and staff-runtime boundary substrate | no polished ops suite or speculative contract widening |
| `Milestone 2.0` | cross-repo closeout | `v0.0.36`, plus `apollo v0.19.1`, `athena v0.6.1`, and `ashton-mcp-gateway v0.2.1` only where real hardening lands | Phase 2 backend/base plateau: pillars, competition base, planner/coaching/nutrition/presence/authz substrate, CLI/internal surfaces, proposal/apply rails, and docs/deploy truth aligned | not a broad demo milestone |
| `System-Proof Milestone` | cross-repo | later than `v0.0.36` | verify runtime truth, deployment truth, modularity, and maintenance model across the ladder | no new feature surface |

## Milestone 2.0 Current Ruling

Canonical note:
`planning/audits/2026-04-10-milestone-2.0-reconciliation.md`

- APOLLO, ATHENA, and the gateway each needed a narrow patch-line hardening
  follow-up, not a new capability line
- at Milestone 2.0 closeout time, the live ATHENA deploy remained the bounded
  `v0.4.1` edge path with quick tunnel ingress and no proved history-path
  rollout
- that historical deploy ruling is now superseded by the later bounded live
  `athena v0.7.0` rollout with Postgres-backed observations, derived session
  facts, and bounded internal history/analytics reads
- proposal/apply safety stays explicit by remaining narrow: gateway is still
  read-only, and APOLLO trusted-surface writes stay capability-gated
- Phase 3 remains blocked on this backend/base plateau staying honest

## Scope Bounds By Tracer

### `Tracer 15`

| Area | In scope | Out of scope |
| --- | --- | --- |
| identity | caller identity for routed reads | user-auth platform rewrite |
| audit | persisted audit trail for routed calls | write approvals |
| routing | one second routed read | broad orchestration |
| tests | repeated routed reads, actor attribution, audit persistence | full agent runtime |

### `Tracer 16`

| Area | In scope | Out of scope |
| --- | --- | --- |
| history | append-only or durable edge observation history | broad predictive product surfaces |
| rebuild | startup rebuild and replay groundwork | full operator override tooling |
| privacy | no raw-ID leakage | identity reconciliation UI |
| tests | append-only proof, restart proof, rebuild proof, duplicate/stale proof | broad analytics rollout |

### `Tracer 17`

| Area | In scope | Out of scope |
| --- | --- | --- |
| HERMES question | one richer read-only operator question | writes or overrides |
| source | stable upstream truth only | private DB hacks |
| operator usefulness | reconciliation and report flavor | admin mutation workflows |
| tests | deterministic answers, failure mapping, source-truth proof | broad assistant UX |

### `Tracer 18` through `Tracer 22`

| Line | In scope | Out of scope |
| --- | --- | --- |
| `Tracer 18` | facility metadata, hours, zones, closure windows | social/product logic |
| `Tracer 19` | sports, facility-sport mappings, basic rules/config | matchmaking runtime |
| `Tracer 20` | team/roster/session/match containers | public standings |
| `Tracer 21` | queueing, assignment, lifecycle transitions | rivalry/badge logic |
| `Tracer 22` | results, ratings, rudimentary standings, member stats | broad public social reads |

### `Tracer 23` through `Tracer 28`

| Line | In scope | Out of scope |
| --- | --- | --- |
| `Tracer 23` | planner, exercise library, templates/loadouts, richer profile inputs as backend/CLI-first truth | meaningful frontend widening |
| `Tracer 24` | deterministic conservative coaching substrate | diagnosis, clinical advice, opaque model outputs |
| `Tracer 25` | conservative nutrition substrate and low-friction meal logging | diagnosis, clinical advice, obsessive nutrition sprawl |
| `Tracer 26` | explanation, summarization, and thin agent-facing helpers over stable deterministic logic | public social feed, frontend-first presentation, LLM-first core decisions |
| `Tracer 27` | member-facing tap-link, visit association, streak state, and streak events | fake streak counters, silent visit inference, broad gamified identity |
| `Tracer 28` | explicit roles, capability checks, actor attribution, trusted-surface primitives, and approval substrate | polished ops suite, speculative shared contracts, broad admin manipulation |

## Phase 3 Boundary

Phase 3 is where these become allowed:

- meaningful frontend widening
- demo workstreams
- broader presentation layers
- public or semi-public product storytelling

Phase 3 may also justify new backend or storage work when a real product or
demo surface proves a hard blocker.

The rule is still narrow:

- mutate the substrate first
- then build the screen over that substrate
- do not fake the screen to avoid the substrate work

Phase 2 should not be back-solved around a UI.

## Phase 3 Shape

Shared Phase 3 substrate lines may land before either shell when product truth
requires them:

- `ATHENA v0.7.x` closeout in repo/runtime truth: `v0.7.1` closed bounded
  projector absent-state retention, and the later `main` line added a compact
  durable projector-miss guardrail while keeping replay authoritative and
  source/site ordering redesign deferred
- `Phase 3 shared substrate B`: an APOLLO-owned scheduling and booking
  substrate above ATHENA facility truth is now closed in repo/runtime truth,
  so calendars and conflict checks are real instead of implied; zones stay
  first-class, bookable truth widened to resource refs plus a resource graph,
  graph authoring is migrations plus owner-first CLI today, and any later
  distinct admin parity with owner must be an explicit APOLLO authz widening

### Phase 3A

`Real member web product`

- polished member shell
- truthful planner / tracker / coaching / meals / competition surfaces
- progress graphs, streak presentation, and PR views
- installable PWA quality

### Phase 3B

`Real ops product`

- supervisor dashboard
- manager event, schedule, and competition control
- owner analytics and policy surfaces
- manager occupancy and booking analytics only over real historical/session and
  scheduling truth
- manager UI authoring only after the underlying scheduling/resource-graph truth
  is already real

### Phase 3C

`Distribution, identity, and assistant presentation`

- avatars, badges, and public or semi-public identity surfaces if earned
- visible assistant persona only after recommendation and proposal rails are trustworthy
- native packaging only if the member web product proves out

## Destructive Harness Expectations

| Line | Must break / test |
| --- | --- |
| `Tracer 14` | upstream timeout, malformed upstream, upstream `500`, repeat-run noise stability |
| `Tracer 15` | bad caller identity, missing actor context, audit write failure, second-route upstream failure |
| `Tracer 16` | duplicate/stale observations, restart rebuild, partial replay, no raw-ID leakage regression |
| `Tracer 17` | missing upstream fields, no-result cases, timeout cases, deterministic reruns |
| `Tracer 18` | invalid facility metadata, malformed hours windows, bad zone references, closure-window conflicts |
| `Tracer 19` | invalid sport definitions, incompatible facility-sport mappings, duplicate keys, config ownership failures |
| `Tracer 20` | duplicate sessions, invalid transitions, replay attempts, roster conflicts, auth conflicts |
| `Tracer 21` | duplicate assignment, stale queue state, invalid lifecycle transitions, replay attempts |
| `Tracer 22` | tampered results, stale standings recompute, per-sport separation, anti-garbage-data checks |
| `Tracer 23` | invalid planner inputs, duplicate templates, impossible states, no cross-domain mutation |
| `Tracer 24` | extreme body stats, missing history, cold start, deterministic reruns, no-medical-overclaim checks |
| `Tracer 25` | dietary restriction conflicts, budget/cooking edge cases, conservative reruns, no-medical-overclaim checks |
| `Tracer 26` | explanation drift, hallucinated unsupported advice, no-hidden-core-decision proof |
| `Tracer 27` | spoofed tap links, duplicate streak events, cross-facility confusion, fake inferred presence, and stale visit-link replay |
| `Tracer 28` | role escalation, trusted-surface bypass, actor-attribution gaps, unauthorized staff/member mutation, and approval-path drift |
| `Milestone 2.0` | repo audit, deploy audit, decision-doc alignment, CLI/internal coherence, and proposal/apply safety checks across the full ladder |

## System-Proof Target

| Area | What it should prove |
| --- | --- |
| runtime truth | each major repo has one or more real, bounded, trustworthy slices |
| deployment truth | physical truth, staff truth, and the later demo/front-end era each have honest boundaries |
| control plane | gateway is narrow but real, caller-aware, and auditable |
| modularity | ATHENA physical truth, APOLLO member/competition truth, HERMES staff truth, and gateway control truth stay separate |
| maintenance | runbooks, CLIs, tests, and hardening artifacts let future work land without collapsing boundaries |
| agentic workflow | future agents can work repo by repo with narrow prompts, hardening discipline, and clear source-of-truth docs |

## Next Moves

| Order | Action | Outcome |
| --- | --- | --- |
| 1 | keep `Phase 3 shared substrate B` and `Phase 3A.1` closed in APOLLO repo/runtime truth | make frontend bootstrap and later ops work build on already-real scheduling and member-shell truth instead of reopening APOLLO by default |
| 2 | open `Phase 3A.2` frontend repo bootstrap now that the embedded-shell line is closed | move the active blocker to frontend repo posture without backsliding into shell rework |
| 3 | keep the RFC3339-only calendar boundary as the anti-ambiguity runtime contract while letting later shells render friendlier local formats | preserve precise scheduling truth without forcing end-user display to look like raw transport data |
| 4 | keep ATHENA deploy truth separate from ATHENA repo/runtime closeout | prevent an optional deploy repin from blocking the product ladder |
| 5 | leave HERMES and gateway follow-ups deferred until those surfaces are about to be used | prevent sidecar hardening from distorting the product ladder |
| 6 | keep docs-first truth and fresh planning-chat seeding as standard practice | keep packet generation tied to committed repo truth |

## Decision Docs

For UI and AI planning, the decision docs are:

- `planning/envisioning/ui-truth-consolidation.md`
- `planning/envisioning/coaching-nutrition-ai-architecture.md`

Treat `ui-truth-consolidation.md` as the buildability arbiter for UI work,
`coaching-nutrition-ai-architecture.md` as the buildability arbiter for
coaching/nutrition/AI-helper work, and `ui-product-vision.md` as deliberate gap
discovery and directional design.
