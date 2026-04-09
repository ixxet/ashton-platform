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
| Facility truth beyond occupancy | missing | hours, zones, and closure metadata still need a clean home | medium-high |
| Competition substrate | missing | sport, team, session, result, and ratings truth do not exist yet | medium-high |
| Planner / coaching backend | missing | still important, but explicitly later than the operations/competition base | medium |
| Meaningful frontend | intentionally deferred | Phase 3 concern, not a Phase 2 driver | gated |

## Current Truth

| Area | State | Meaning | Next |
| --- | --- | --- | --- |
| `Tracer 11` | shipped | APOLLO member shell is real, but intentionally thin | done |
| `Tracer 12` | shipped | explicit lobby membership and durable member intent are real | done |
| `Tracer 13` | shipped | deterministic read-only ARES preview is real | done |
| `ATHENA runtime` | `v0.5.1` shipped; `v0.4.1` still deployed | durable history plus bounded privacy-safe history support are real in repo/runtime while deployed truth stays narrower | done |
| `ATHENA deployment closeout` | `Prometheus v0.0.3`, `ashton-platform v0.0.19` shipped | bounded live deploy truth is real | done |
| `APOLLO member runtime` | `v0.9.0` shipped | auth, visits, workouts, recommendations, membership, and deterministic preview are real | done |
| `HERMES` | `v0.2.0` shipped | one thin staff read plus one richer reconciliation read are real | done |
| `Gateway` | current Tracer 15 line real on `main` | control plane is real, caller-aware, and still intentionally narrow | later gateway widening only if justified |
| `Platform docs` | synced to current shipped lines | control-plane planning truth now matches current repo and tag truth | keep synced as work lands |

## What Phase 2 Is For

Phase 2 is the era where ASHTON finishes its backend/base pillars.

It should leave behind:

- durable physical truth
- trustworthy operator truth
- auditable control-plane truth
- facility and sport truth
- session, result, rating, and stats truth
- planner and coaching backends that are still modular and deterministic
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
| Facility catalog / hours / zones | no | yes |
| Sport / team / session / result substrate | no | yes |
| Planner / coaching backend | partial | yes |
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
| training floor | planner, templates, coaching | later than the competition substrate in the new Phase 2 order |
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
| `apollo` | API/CLI-first sport, team, session, result, planner, and coaching primitives |
| `ashton-mcp-gateway` | actor-aware routed reads and persisted audit |
| `Prometheus` | deploy/runbook truth, not product logic |

The exact commands can evolve, but the principle should stay stable:

- agents should not need a thick UI to inspect or harden Phase 2 truth

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
| Deterministic coaching and conservative nutrition guidance | Must have | `apollo` | partial | `Tracer 24` | no opaque or medical core |
| Explanation and agent-facing helper layer | Good later | `apollo` | yes | `Tracer 25` | explanation layer only, not the decision engine |
| Rivalries, badges, rematch prompts, public highlights | Good later | `apollo` | yes | later than `Tracer 22` and likely Phase 3+ | depends on trusted competition truth |
| Public leaderboards | Dangerous too early | `apollo` | yes | later than `Tracer 22` | requires trusted results and anti-abuse basics |
| Full social feed / chat | Dangerous too early | `apollo` | no | later than real product traction | high complexity, moderation burden, low foundation value |

## Product Ordering

| If you want a stable backend before a front end | Best order | Why |
| --- | --- | --- |
| structural pillars first | `14 -> 1.7 -> 15 -> 16 -> 17` | finish weak platform pillars first |
| operational / competition base next | `18 -> 19 -> 20 -> 21 -> 22` | gives the platform honest facility, sport, session, result, and standings truth |
| planner / coaching backend after that | `23 -> 24 -> 25` | keep fitness logic real, but do it after the operations/competition base exists |
| demos and frontend later | Phase 3 only | do not let presentation pressure distort Phase 2 modularity |

## Future Tracer Ladder

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
| `Tracer 24` | `apollo` | `v0.15.0` / `v0.0.31` | deterministic coaching, conservative calorie/macro ranges, and low-friction meal logging | no diagnosis, no opaque core |
| `Tracer 25` | `apollo` | `v0.16.0` / `v0.0.32` | explanation, summarization, and thin agent-facing helper surfaces over stable deterministic logic | no public social feed or frontend-first pivot |
| `Milestone 2.0` | cross-repo closeout | `v0.0.33` unless repo-local patch lines are needed | Phase 2 backend/base plateau: pillars, competition base, planner/coaching substrate, CLI/internal surfaces, and docs/deploy truth aligned | not a broad demo milestone |
| `System-Proof Milestone` | cross-repo | later than `v0.0.33` | verify runtime truth, deployment truth, modularity, and maintenance model across the ladder | no new feature surface |

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

### `Tracer 23` through `Tracer 25`

| Line | In scope | Out of scope |
| --- | --- | --- |
| `Tracer 23` | planner, exercise library, templates/loadouts, richer profile inputs as backend/CLI-first truth | meaningful frontend widening |
| `Tracer 24` | deterministic conservative coaching and low-friction nutrition guidance | diagnosis, clinical advice, opaque model outputs |
| `Tracer 25` | explanation, summarization, and thin agent-facing helpers over stable deterministic logic | public social feed, frontend-first presentation, LLM-first core decisions |

## Phase 3 Boundary

Phase 3 is where these become allowed:

- meaningful frontend widening
- demo workstreams
- broader presentation layers
- public or semi-public product storytelling

Phase 2 should not be back-solved around a UI.

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
| `Tracer 25` | explanation drift, hallucinated unsupported advice, no-hidden-core-decision proof |

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
| 1 | start `Tracer 14` | make the current staff slice inspectable |
| 2 | keep tracer prompts explicit about formal pre-`1.0.0` semver, granular commits, and push-on-`main` | prevent drift |
| 3 | keep Phase 2 backend-first and CLI-first | prevent premature UI pressure |
| 4 | treat `Tracer 18` through `Tracer 22` as the competition/operations base, not demo polish | keep the ladder structurally honest |
| 5 | treat `Tracer 23` through `Tracer 25` as backend product expansion, still without real frontend widening | keep the platform modular |
| 6 | begin demo/front-end planning only after `Milestone 2.0` is closure-clean | keep Phase 3 honest |
