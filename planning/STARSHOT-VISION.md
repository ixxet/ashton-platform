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
| Immediate pressure | build toward an investor-light demo without turning the platform into soup |
| Core rule | every tracer may widen one meaningful boundary, but it must also say what it will not do |

## Urgency Snapshot

| Area | State now | Meaning | Urgency |
| --- | --- | --- | --- |
| ATHENA deploy truth | done | physical-truth pillar is live and credible | done |
| APOLLO auth / membership / preview | done | member intent pillar exists | done |
| HERMES | thin | needs observability plus live proof | high |
| Gateway | thin | needs identity plus audit | high |
| ATHENA durability | missing | still weak on restart, history, and audit side | high |
| Workout planner | missing | needed for investor-visible real-platform feel | high |
| Conservative fitness coach | missing | needed to connect profile, plan, and recommendations | high |
| Match / session runtime | missing | needed before real ratings, leaderboards, and rivalries | medium-high |
| Public standings / home page | missing | investor-friendly, but only after trusted results | medium |
| Social / DMs / public achievement showcase | missing | good later, only if the product actually hits | gated |

## Current Truth

| Area | State | Meaning | Next |
| --- | --- | --- | --- |
| `Tracer 11` | shipped | APOLLO member shell is real | done |
| `Tracer 12` | shipped | explicit lobby membership and durable member intent are real | done |
| `Tracer 13` | shipped | deterministic read-only ARES preview is real | done |
| `ATHENA runtime` | `v0.4.1` shipped | edge ingress and edge-driven occupancy are real in repo/runtime | done |
| `ATHENA deployment closeout` | `Prometheus v0.0.3`, `ashton-platform v0.0.19` shipped | bounded live deploy truth is real | done |
| `APOLLO member runtime` | `v0.9.0` shipped | auth, visits, workouts, recommendations, membership, and deterministic preview are real | done |
| `HERMES` | `v0.1.0` shipped | one thin staff read exists | `Tracer 14` |
| `Gateway` | first routed read only | control plane is real but thin | `Tracer 15` |
| `Platform docs` | mostly synced | control-plane planning truth is much stronger than before | keep synced as work lands |

## Recent Prompt Themes

| Prompt theme | Why it mattered |
| --- | --- |
| stop branch-heavy drift | closure truth belongs on `main`, not in side branches |
| ATHENA deployment truth first | physical truth had to be live before more product widening |
| pre-`1.0.0` semver now | tags needed clearer meaning before the next ladder |
| investor-light urgency | future tracers must build toward a credible demo, not just local proofs |
| feature bank expansion | planner, coach, sessions, ratings, rivalries, achievements, and later social now have a real order |
| logger placement | HERMES observability first, gateway audit second |
| defer dangerous early surfaces | no premature public leaderboard, feed, chat, or opaque recommendation core |
| product posture | not a diet app; conservative logging, planning, and guidance only |
| matchmaking truth split | preview is real now; execution, ratings, and rivalries need later session truth |
| building analogy | the platform is a multi-floor structure with real concrete already poured, not a sketch |

## Brief History

| Phase | What happened | Why it mattered |
| --- | --- | --- |
| early member work | shell, auth, visits, workouts, recommendations | established member truth and a thin but real product surface |
| membership discipline | explicit joined lobby membership | made intent durable instead of inferred |
| coordination proof | deterministic preview | proved the platform can read from explicit member state without mutating it |
| physical truth | ATHENA edge ingress and occupancy | proved real-world edge truth and live deployment truth for a bounded slice |
| control-plane proof | first routed reads and bounded staff view | gave the system a narrow operator surface |
| current phase | ladder planning and product widening | move from slice proof to durable platform pillars and a demo-worthy product |

## Platform Shape

| Pillar | Real now | Strong after next ladder |
| --- | --- | --- |
| Physical truth runtime + deploy | yes | yes |
| Cross-repo lifecycle event boundary | yes | yes |
| Member truth runtime | yes | yes |
| Member shell | yes | yes |
| Explicit member intent | yes | yes |
| Deterministic coordination preview | yes | yes |
| Staff read surface | yes, thin | yes, stronger |
| Staff observability | no | yes |
| Staff live deployment truth | no | yes |
| Control-plane routed read | yes, thin | yes |
| Control-plane identity + audit | no | yes |
| Durable edge observation history | no | yes |
| Operator review / reconciliation read surface | no | yes |

| Count | Value |
| --- | --- |
| Meaningful pillars real now | **8** |
| Meaningful pillars after `Tracer 14` + `Milestone 1.7` + `Tracer 15` | **11** |
| Meaningful pillars after `Tracer 16` + `Tracer 17` | **13** |

## Building Analogy

| Building piece | ASHTON meaning | What it does |
| --- | --- | --- |
| concrete slab | ATHENA deploy truth | the part that must hold weight before anything else stands |
| framing | APOLLO member truth | gives the interior shape for identity, workouts, preview, and recommendations |
| plumbing | NATS and event flow | moves real state between rooms without leaking abstraction |
| wiring | gateway and control plane | lets operators and clients reach the right surfaces safely |
| lobby | shell and auth | first human-facing entry point |
| fitness rooms | planner, templates, recommendations, logs | member training experience |
| competition rooms | sessions, ratings, rivalries, standings | game and status experience |
| staff office | HERMES | inspect, review, and observe without mutating everything |
| mechanical room | deploy, audit, history, runbooks | hidden but essential systems work |

| Building type | Best fit |
| --- | --- |
| warehouse | no, too flat |
| detached house | no, too small |
| apartment | partially, but too generic |
| mansion | closer, but too decorative |
| multi-floor office / clubhouse with a strong mechanical core | best fit |

| Floor | Main function | Rooms |
| --- | --- | --- |
| basement / mechanical | deployment truth, event flow, durability | ATHENA, NATS, deploy closeout, audit |
| ground floor / lobby | auth, shell, entry | member shell, session start, navigation |
| floor 2 / member work | workouts, profiles, goals, planner | planner, templates, recommendations |
| floor 3 / competition | preview, sessions, ratings, rivalries | competition runtime, standings, badges |
| floor 4 / staff ops | HERMES, reviews, observation | staff reads, alerts, review tools |
| upper floors / future suite | social, DMs, sharing, LLM explanation | opt-in later polish only when justified |

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
| LLM use | use later for explanation or summarization, not as the first opaque decision engine |

## Pre-1.0 Semver By Repo

| Repo | Current posture | Versioning behavior |
| --- | --- | --- |
| `apollo` | already on meaningful minors | use `minor` for new capability lines and `patch` for hardening and closeout |
| `athena` | runtime line shipped at `v0.4.1` | durable history becomes a new minor line, not a patch |
| `hermes` | thin first slice at `v0.1.0` | observability hardening can be patch-style, richer staff question becomes minor |
| `ashton-mcp-gateway` | thin route truth | identity + audit + second route becomes minor |
| `ashton-platform` | ledger and planning repo | patch-style closeout lines are fine |
| deploy repos | deployment ledgers | patch-style closeout lines are fine |

## Table Placement Map

| Table family | Central home | Repo-grounded home | Why |
| --- | --- | --- | --- |
| urgency, feature bank, product lanes, investor-light path, long future ladder | `planning/STARSHOT-VISION.md` | none required by default | this is the one place where the whole vision should stay together |
| release lines, next execution order, proof ladder, pre-`1.0.0` semver guardrails | `planning/IMPLEMENTATION-BOARD.md` | repo roadmaps reference their local slice | this is the operational ledger for what is next and how it closes |
| runtime surfaces, current real state, shipped lines | `README.md` in each repo | same file | landing pages should ground reality, not carry the whole star-shot |
| next ladder role and local hard stops | repo `docs/roadmap.md` | same file | each repo should know its next narrow job without reading the whole platform plan |
| tracer closure details and historical hardening truth | `planning/sprints/TRACER-MATRIX.md` and repo hardening docs | repo hardening files | closure evidence should stay near the tracer, not in vision docs |
| deployment-closeout truth | deployment repo docs plus `ashton-platform` ledger | deployment repo README / runbooks | deploy truth needs its own source, not just product planning tables |

| Repo | Tables that belong locally |
| --- | --- |
| `apollo` | current product truth, planner/recommendation lane, competition lane, product design calls that directly affect member-facing logic |
| `athena` | deploy/runtime truth, durable history lane, ingress hard stops, history/rebuild proof tables |
| `hermes` | observability lane, live deployment lane, richer operator-question lane, staff-only scope tables |
| `ashton-mcp-gateway` | caller identity, persisted audit, second-route scope, later approval deferrals |
| `ashton-platform` | urgency, cross-repo ladder, feature bank, investor-light demo path, pre-`1.0.0` semver policy, system-proof horizon |

## Current Product Truth

| Area | State |
| --- | --- |
| member shell | real, thin |
| explicit membership | real |
| deterministic preview | real |
| workouts | real, but not planner-grade |
| workout recommendation | real, but deterministic and narrow |
| game sessions | not real |
| leaderboards | not real |
| social graph / rivalries | not real |
| operator review tools | not real yet |

## Feature Bank

| Feature | Bucket | Repo owner | Needs real history first? | Earliest sane point | Why / boundary |
| --- | --- | --- | --- | --- | --- |
| HERMES request / result / outcome logger | Must have | `hermes` | no | `Tracer 14` | makes the current staff slice inspectable |
| Gateway caller identity + persisted audit | Must have | `ashton-mcp-gateway` | no | `Tracer 15` | needed before broader routing or any later write governance |
| Durable ATHENA edge observation history | Must have | `athena` | yes | `Tracer 16` | removes all-memory dependence and enables rebuild / review |
| Richer operator review / reconciliation read | Must have | `hermes` | yes | `Tracer 17` | turns HERMES into a real operator surface |
| Workout planner / weekly plan / machine dropdowns | Must have | `apollo` | no | `Tracer 18` | natural extension of the current workout runtime |
| Exercise library with machine / free-weight / sport tags | Must have | `apollo` | no | `Tracer 18` | planner and recommendation substrate |
| Saved workout templates / loadouts | Must have | `apollo` | no | `Tracer 18` | makes planning and repetition practical |
| Profile goals / body metrics | Must have | `apollo` | no | `Tracer 18` | makes recommendations and planning personalized |
| Progression history | Must have | `apollo` | yes | `Tracer 18` and later | turns workouts into meaningful trends |
| Conservative calorie / macro target range | Must have | `apollo` | no | `Tracer 19` | useful only as non-medical guidance |
| Deterministic fitness recommendation engine | Must have | `apollo` | partial | `Tracer 19` | planner plus profile plus history, no opaque core |
| Match / session runtime | Must have | `apollo` | no | `Tracer 20` | execution boundary beyond preview |
| Per-sport / per-mode ratings, likely OpenSkill | Must have | `apollo` | yes | `Tracer 21` | required before trusted standings |
| Transparent admin audit | Must have | `ashton-mcp-gateway` and later ops surfaces | yes | `Tracer 15` onward | needed before competition becomes public |
| Streak-friendly meal logging | Good later | `apollo` | no | after `Tracer 19` | useful if it stays low-friction and non-obsessive |
| Meal templates | Good later | `apollo` | no | after `Tracer 19` | lowers friction for simple logging |
| Weekly trend summary | Good later | `apollo` | yes | after `Tracer 19` | better than noisy daily views |
| Challenge / rematch prompts | Good later | `apollo` | yes | `Tracer 22` | ties competition and retention together |
| Rivalry tracker / streak counters / tease badges | Good later | `apollo` | yes | `Tracer 22` | emotionally strong, but depends on trusted results |
| Achievements / badges | Good later | `apollo` | yes | `Tracer 22` | easy game feel once history is trustworthy |
| XP bars / consistency bars | Good later | `apollo` | yes | `Tracer 22` and later | should reflect real activity, not fake grinding |
| Daily / weekly quests | Good later | `apollo` | partial | `Tracer 22` and later | works best once multiple activity loops are real |
| Goal presets and training archetypes | Good later | `apollo` | no | after `Tracer 19` | helps profile and plan selection |
| Progressive overload suggestions | Good later | `apollo` | yes | after `Tracer 19` | only if conservative and explainable |
| Matchmaking queues | Good later | `apollo` | yes | after `Tracer 20` | wait for real session truth |
| Seasonal ladders | Good later | `apollo` | yes | after `Tracer 22` | only after trusted standings |
| Lightweight opt-in crews / squads | Good later | `apollo` | no | after product success | later than the core planner and competition loops |
| Profile DMs and achievement showcases | Good later | `apollo` | no | after product success | opt-in only, not a public feed |
| LLM-backed recommendation explainer | Good later | `apollo` | yes | after deterministic recommendation is trusted | explanation layer only |
| Public leaderboards, including public front-page standings | Dangerous too early | `apollo` public read | yes | later than `Tracer 22` | requires trusted results and anti-abuse basics |
| Full social feed | Dangerous too early | `apollo` | no | defer until major adoption | high complexity, low foundation value |
| Chat | Dangerous too early | `apollo` | no | defer until major adoption | moderation burden |
| Aggressive calorie or body-fat claims | Dangerous too early | `apollo` | no | defer | risk and low trust |
| Opaque LLM recommendation core | Dangerous too early | `apollo` | no | defer | hard to trust and harden |
| Complex item / shop / metagame | Dangerous too early | later product surfaces | yes | defer | too much surface too soon |
| Admin manipulation or cheat tools | Dangerous too early | later ops/admin surfaces | yes | defer | requires much stronger audit and governance |

## Design Calls

| Topic | Recommendation |
| --- | --- |
| BMI | use as one coarse input only, not the main driver |
| Calories | keep it conservative, average-range, and clearly non-medical |
| Nutrition posture | this is not a diet app; it logs, tracks, and suggests simple ranges only |
| LLM | use later to explain recommendations, not compute the core decision first |
| OpenSkill | good fit for solo, team, and mixed sports if ratings are separated by sport and mode |
| Rating model | separate by sport and mode, not one global score |
| Rivalries | pairwise counters plus recency plus streaks, derived from real results |
| Public leaderboards | later only, and only after trusted results plus anti-abuse basics |
| Social | keep it opt-in and lightweight; DMs and achievement showcases can come before any public feed |

## What To Defer

| Idea | Defer until | Why |
| --- | --- | --- |
| full social feed | product traction | high complexity, low foundation value |
| chat | product traction | moderation burden |
| aggressive calorie or body-fat claims | never as a core product | risk and low trust |
| opaque LLM recommendation core | deterministic core trusted first | hard to trust and harden |
| public leaderboards before trusted results | trusted session truth exists | invites garbage data and drama |
| complex item / shop / metagame | later | too much surface too soon |
| social DMs and achievement sharing | around 300+ users or clear product pull | keep surface narrow until real usage proves it |

## Product Ordering

| If you want both fitness and competition | Best order | Why |
| --- | --- | --- |
| platform-first | `14 -> 15 -> 16 -> 17` | finish weak platform pillars first |
| fitness lane after that | planner -> deterministic recommender -> optional LLM explainer | APOLLO already owns workouts and profile state |
| competition lane after that | match/session runtime -> ratings -> rivalries/leaderboards | standings need trusted session truth first |

## Future Tracer Ladder

| Line | Repo focus | Release line | Purpose | Hard stop |
| --- | --- | --- | --- | --- |
| `Cleanup 0` | `ashton-platform` | patch-only docs | align docs with shipped truth and remove stale wording | docs only |
| `Tracer 14` | `hermes` | `v0.1.1` / `v0.0.20` | observability hardening for the current occupancy slice | no richer questions, no writes |
| `Milestone 1.7` | `hermes` + deploy repo | `v0.1.2` if runtime changes, otherwise deploy closeout only | prove live HERMES deployment truth | no write authority |
| `Tracer 15` | `ashton-mcp-gateway` + optional `ashton-proto` | `v0.2.0` / `v0.0.22` | caller identity, persisted audit, second routed read | no approvals/writes yet |
| `Tracer 16` | `athena` | `v0.5.0` / `v0.0.23` | durable edge-observation history and rebuild groundwork | no prediction rollout |
| `Tracer 17` | `hermes` | `v0.2.0` / `v0.0.24` | one richer read-only staff question over stable truth | no overrides/writes |
| `Tracer 18` | `apollo` | `v0.10.0` / `v0.0.25` | workout planner, weekly schedule, exercise library, templates/loadouts, richer profile goals/body metrics | no medical claims, no LLM-first logic |
| `Tracer 19` | `apollo` | `v0.11.0` / `v0.0.26` | conservative deterministic recommendation engine, calorie/macro ranges, low-friction meal logging, progression summaries | no diagnosis, no opaque black box |
| `Milestone 2.0` | `apollo` + deploy repos | patch closeout only unless runtime changes | investor-light platform demo: shell + planner + conservative coach + live ATHENA + live HERMES | no broad public leaderboard yet |
| `Tracer 20` | `apollo` | `v0.12.0` / `v0.0.28` | match/session runtime beyond preview, solo + team result capture | no full social network |
| `Tracer 21` | `apollo` | `v0.13.0` / `v0.0.29` | per-sport / per-mode ratings, likely OpenSkill | no public standings if trust is weak |
| `Tracer 22` | `apollo` | `v0.14.0` / `v0.0.30` | rivalries, streaks, badges, achievements, rematch and challenge prompts | no admin cheat panel |
| `Tracer 23` | `apollo` public read | `v0.15.0` / `v0.0.31` | safe public front page with trusted standings/highlights | no garbage-data-friendly public ladder |
| `Tracer 24` | `apollo` | `v0.16.0` / `v0.0.32` | opt-in crews/squads, DMs, achievement showcases | only after traction |
| `Tracer 25` | `apollo` | `v0.17.0` / `v0.0.33` | LLM explanation layer only: coach notes, plan rewrites, summaries | not the core decision engine |
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
| operator usefulness | reconciliation and review flavor | admin mutation workflows |
| tests | deterministic answers, failure mapping, source-truth proof | broad assistant UX |

### `Tracer 18`

| Area | In scope | Out of scope |
| --- | --- | --- |
| planner | weekly planner, exercise library, machine dropdowns, templates/loadouts | social scheduling or broad nutrition coaching |
| profile | richer goals and body metrics needed for planning | medical claims or invasive profile sprawl |
| history | progression-friendly workout capture tied to planning | broad analytics stack |
| tests | invalid exercise IDs, impossible planner states, duplicate templates, ownership boundaries | recommendation black box or deploy-proof claims |

### `Tracer 19`

| Area | In scope | Out of scope |
| --- | --- | --- |
| recommendation | deterministic conservative plan guidance, calorie/macro ranges, structured reasons | diagnosis, clinical advice, opaque model outputs |
| nutrition | low-friction meal logging and simple templates | obsessive food-database sprawl |
| safety | conservative ranges, explicit limitations, confidence-style wording | body-fat promises or medical claims |
| tests | cold start, missing history, extreme stats, deterministic reruns, no-medical-overclaim checks | LLM-first logic |

## Demo Milestone

| Milestone | Must be live | Why it matters |
| --- | --- | --- |
| investor-light core demo | member auth + thin shell | basic legitimacy |
| investor-light core demo | profile goals / body metrics | shows personalization |
| investor-light core demo | workout planner + templates | makes it feel like a real product |
| investor-light core demo | conservative recommendations | shows intelligence without risky claims |
| investor-light core demo | explicit membership + match preview | keeps the competition lane visible |
| investor-light core demo | live ATHENA occupancy | real-world systems story |
| investor-light core demo | live HERMES staff read | operator story |
| investor-light core demo | gateway audit / identity | trust and control-plane story |
| investor-light core demo | public leaderboard | not yet; wait for trusted session truth |

## Destructive Harness Expectations

| Line | Must break / test |
| --- | --- |
| `Tracer 14` | upstream timeout, malformed upstream, upstream `500`, repeat-run noise stability |
| `Tracer 15` | bad caller identity, missing actor context, audit write failure, second-route upstream failure |
| `Tracer 16` | duplicate/stale observations, restart rebuild, partial replay, no raw-ID leakage regression |
| `Tracer 17` | missing upstream fields, no-result cases, timeout cases, deterministic reruns |
| `Tracer 18` | invalid machine/exercise IDs, impossible planner states, duplicate templates, no cross-domain mutation |
| `Tracer 19` | extreme body stats, missing history, cold start, deterministic reruns, no-medical-overclaim checks |
| `Tracer 20` | duplicate sessions, invalid transitions, replay attempts, team-size mismatch, auth conflicts |
| `Tracer 21` | ties, stale recompute, solo-vs-team separation, per-sport separation, tampered result rejection |
| `Tracer 22` | rivalry spam, badge inflation, stale standings recompute, anti-garbage-data checks |
| `Tracer 23` | public-read abuse, cache staleness, no-mutation proof |
| `Tracer 24` | DM/privacy abuse, invite spam, opt-in enforcement |
| `Tracer 25` | hallucinated advice, unsupported medical claims, explanation drift from deterministic core |

## System-Proof Target

| Area | What it should prove |
| --- | --- |
| runtime truth | each major repo has one or more real, bounded, trustworthy slices |
| deployment truth | physical truth, staff truth, and the investor-light member platform each have live proved boundaries |
| control plane | gateway is narrow but real, caller-aware, and auditable |
| modularity | ATHENA physical truth, APOLLO member truth, HERMES staff truth, and gateway control truth stay separate |
| maintenance | runbooks, CLIs, tests, and hardening artifacts let future work land without collapsing boundaries |
| agentic workflow | future agents can work repo by repo with narrow prompts, hardening discipline, and clear source-of-truth docs |

## Investor-Light Demo Path

| Order | Action | Why this order |
| --- | --- | --- |
| 1 | `Tracer 14` | makes the weakest current pillar inspectable |
| 2 | `Milestone 1.7` | turns staff truth from local/runtime into live truth |
| 3 | `Tracer 15` | makes the control plane caller-aware and auditable |
| 4 | `Tracer 16` | gives ATHENA durable history and rebuild strength |
| 5 | `Tracer 17` | gives HERMES a real operator/reconciliation surface |
| 6 | `Tracer 18` | adds a visible workout-planner product loop |
| 7 | `Tracer 19` | adds a conservative coach layer on top of planner truth |
| 8 | `Milestone 2.0` | proves the first integrated investor-light platform story |

## Next Moves

| Order | Action | Outcome |
| --- | --- | --- |
| 1 | start `Tracer 14` | make the current staff slice inspectable |
| 2 | keep tracer prompts explicit about pre-`1.0.0` semver, granular commits, and push-on-`main` | prevent drift |
| 3 | keep future lanes split | fitness first, competition second, social later |
| 4 | revisit public standings and social only after trusted history and traction exist | avoid premature drama |
```\n\nIf you want, I can also turn this into a second version that is shorter and more landing-page friendly for `README.md`, plus a fuller archive version for a `STARSHOT-VISION.md` planning file."}}
