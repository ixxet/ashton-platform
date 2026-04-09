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

Use this order when planning or implementing:

1. `planning/repo-briefs/`
2. repo-local `README.md` and `docs/roadmap.md`
3. repo-local ADRs
4. repo-local migrations and schemas
5. `planning/sprints/TRACER-MATRIX.md`
6. `planning/audits/`
7. `planning/STARSHOT-VISION.md` for strategic horizon and future-ladder context only
8. `planning/sprints/BUILD-ORDER.md` as historical planning context only
9. `planning/architecture/` as background reference only

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

Tracer 10 is now closure-clean and Milestone 1.6 now proves the bounded live
departure-close boundary. The remaining active blockers before the next major
implementation slice are:

1. `ashton-mcp-gateway`
   - keep the line narrower than the future control-plane vision even after
     caller identity and audit are real
   - do not widen beyond two read-only ATHENA routes until a later tracer
     explicitly justifies it
2. `ashton-proto`
   - keep manifest expansion tracer-driven now that the first ATHENA manifest is
     real
3. `athena`
   - keep the new bounded live edge-ingress deployment narrower than any broad
     source-backed ingress rollout
   - do not widen into broader prediction, persistence, or admin workflow work
     next
4. `hermes`
   - keep staff operations read-only and source-backed; the deploy-only
     Milestone 1.7 closeout now proves the bounded live runner slice without
     widening runtime truth
   - keep any later operator surface narrower than a broader assistant or
     write-authority claim
5. `apollo`
   - keep explicit lobby membership narrower than future ARES, invites, or
     social coordination flows
   - do not let eligibility, visits, workouts, or recommendations auto-create
     membership
6. terminology cleanup
   - keep using `presence` instead of `check-in` in current working docs
   - keep using `lobby` for product-facing matchmaking terminology in APOLLO

## Deferred But Planned

These are documented and expected, but they are not blockers for the first line of Go:

- Redis
  - `ATHENA`: fast occupancy counters and short-lived zone aggregate caching
  - `APOLLO`: caching expensive visit/workout aggregation reads and later lobby hot state
  - `HERMES` and `ashton-mcp-gateway`: rate limiting and ephemeral session state
- AlertManager rules in `Prometheus`
- Loki and full observability refinements
- Mermaid diagram refresh
- Full MCP gateway rollout
- TOON / `incur` experimentation
- Rust rewrite of the gateway
- Cluster expansion and workload scheduling milestones in `Prometheus`

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

| Versioning piece | Meaning in ASHTON now |
| --- | --- |
| Tag shape | All repos keep `vMAJOR.MINOR.PATCH` tags so release lines are readable and comparable |
| `MAJOR` | Stay at `0` during the current foundation-building era; move to `1` only after the system-proof phase makes a repo meaningfully stable |
| `MINOR` | Use for a new bounded runtime capability or tracer-sized widening in that repo |
| `PATCH` | Use for hardening, docs sync, deployment closeout, observability, bug fixes, or other bounded follow-up on an existing capability line |
| Pre-`1.0.0` rule | Use formal pre-`1.0.0` semantic versioning discipline: `MINOR` may widen or intentionally break a pre-`1.0.0` surface; `PATCH` must not add a new capability or a breaking change |
| Breaking-change rule | Before `1.0.0`, breaking changes to public HTTP surfaces, CLI contracts, shared schemas, manifests, or migrations require a `MINOR` bump, never a `PATCH` bump |
| Control-plane and deploy repos | `ashton-platform` and deployment repos may use patch bumps as ledger / closeout lines even when service repos use minor bumps for runtime growth |
| `1.0.0` graduation | A repo graduates only after stable public surfaces, explicit compatibility rules, repeatable hardening evidence, and release automation exist; see `planning/runbooks/phase-2-launch.md` |
| History rule | Existing tags stand as history; stricter version discipline applies forward only |

## Repo Release Lines

| Repo | Current line | Next planned line | Why it is next |
| --- | --- | --- | --- |
| `ashton-proto` | `v0.3.0` shipped; current Tracer 15 contract line `v0.4.0` | later than `v0.4.0` | the second routed manifest line is now real in the current repo line; further widening should stay tracer-driven |
| `athena` | `v0.5.1` shipped; the Tracer 18 facility-truth line is now on `main`; `v0.4.1` remains the current deployed line | `v0.6.0` | facility truth is now the current repo line while deployed truth stays unchanged |
| `apollo` | current repo/runtime line: `v0.11.0`; deployed truth unchanged | `v0.12.0` | sport registry, facility-sport capability mapping, and team/roster/session/match container truth are now real without widening into matchmaking, results, or public standings |
| `hermes` | `v0.2.0` shipped | `v0.3.0` | the richer read-only reconciliation line is now shipped and the first write/approval boundary is the next true widening |
| `ashton-mcp-gateway` | `v0.0.1` shipped; current Tracer 15 line `v0.2.0` | `v0.3.0` | the caller-aware audited read-only control-plane slice is now real; write governance is the next true widening |
| `ashton-platform` | current repo/control-plane line: `v0.0.27`; deployed truth unchanged | `v0.0.28` | Tracer 20 control-plane closeout now tracks the APOLLO competition container line while deployment claims remain unchanged |

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
- `v0.5.1`, `v0.2.0`, and `v0.0.24` are the Tracer 17 release lines for
  bounded ATHENA support, HERMES runtime truth, and platform closeout truth
- `v0.6.0` and `v0.0.25` are the Tracer 18 release lines for ATHENA facility
  truth and platform control-plane closeout
- deployed truth remains unchanged: the bounded live edge path is still real,
  while the durable-history branch and reconciliation widening remain local/runtime proof only

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
| ATHENA durability | shipped | deterministic durable-history groundwork is tagged, bounded support follow-up is released, and durable-branch deployment truth is still unchanged | done |
| HERMES operator surface | shipped | one richer reconciliation question, occupancy reports, and heat-map-style reads are now shipped while deployed truth stays unchanged | done |
| Facility catalog / hours | closure-clean on `main` | ATHENA now exposes config-gated facility catalog, hours, zones, closure windows, and bounded metadata reads while deployed truth stays unchanged | done |
| Sport / team / session base | closure-clean in repo/runtime; deployed truth unchanged | APOLLO now owns sport registry plus team/roster/session/match container truth without widening into queueing, results, ratings, or public standings | done |
| Ratings / standings / profile stats | missing | needed before truthful competition surfaces or later demos | medium |
| Planner / coaching backend | missing | still important, but explicitly later than the operational and competition base | medium |
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

## Post-ATHENA Ladder

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
| `v0.0.30` | `Tracer 23` | `apollo v0.14.0` | planner, exercise library, templates/loadouts, and richer profile inputs as backend/CLI-first truth | no meaningful frontend widening |
| `v0.0.31` | `Tracer 24` | `apollo v0.15.0` | deterministic coaching, conservative calorie/macro ranges, and low-friction meal logging | no diagnosis or opaque black box |
| `v0.0.32` | `Tracer 25` | `apollo v0.16.0` | explanation, summarization, and thin agent-facing helper surfaces over stable deterministic logic | no public social feed or frontend-first pivot |
| `v0.0.33` | `Milestone 2.0` | patch closeout only unless runtime truth changes | Phase 2 backend/base plateau: structural pillars, competition base, planner/coaching substrate, deploy truth, and docs aligned | not a broad demo milestone |
| later than `Milestone 2.0` | long-horizon ladder in `planning/STARSHOT-VISION.md` | Phase 3 demos and later system-proof | meaningful frontend, demos, and broader presentation only after the backend/base ladder is closed cleanly | do not widen frontend during Phase 2 |

## What Each Next Line Must Achieve

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
| `Tracer 24` | APOLLO gains deterministic coaching and conservative nutrition guidance | connects profile, plan, and recommendations without risky claims |
| `Tracer 25` | APOLLO gains explanation and agent-facing helpers over stable deterministic logic | improves agent/operator usability without making the model the core engine |
| `Milestone 2.0` | the backend/base ladder is closure-clean across repos and deploy truth | turns the platform from a pile of tracers into a modular backend plateau ready for Phase 3 demos |
| `System-Proof Milestone` | system-level proof of runtime truth, deployment truth, modularity, and maintenance model | shifts the platform from a pile of tracers to a coherent system |

## Scope Bounds For The Remaining Phase 2 Lines

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
| `Tracer 24` | deterministic conservative recommendations, calorie / macro ranges, low-friction meal logging, explicit limitations | diagnosis, clinical advice, opaque model outputs, obsessive nutrition sprawl |
| `Tracer 25` | explanation, summarization, agent-facing helper reads, and thin support tooling over stable deterministic logic | public feed, social sharing, LLM-first core decisions, frontend-first product work |

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

## Test / Proof Plan By Line

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
| `Tracer 25` | explanation/helper output remains traceable to deterministic core logic and never invents unsupported advice |
| `Milestone 2.0` | repo audit plus deploy audit plus doc alignment plus CLI/internal surface coherence prove the backend/base plateau cleanly |
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

## Immediate Next Steps

1. Treat `v0.0.19` as the shipped bounded ATHENA deployment closeout line.
2. Treat `Tracer 14` as closure-clean on `main` and keep it strictly observability-only.
3. Use `Milestone 1.7` to record the same HERMES slice as bounded live deployment truth without widening runtime scope.
4. Use `Tracer 15` to strengthen gateway trust before any approval or write widening.
5. Treat `Tracer 16` and `Tracer 17` as shipped and use `Tracer 18` to finish facility truth before product/base widening.
6. Use `Tracer 18` through `Tracer 22` to finish the operational and competition base before planner/coaching work.
7. Use `Tracer 23` through `Tracer 25` to finish the backend/agent-friendly product expansion without turning Phase 2 into a frontend push.
8. Treat `Milestone 2.0` as the backend/base plateau after all remaining tracers, not as a demo milestone.
9. Treat Phase 3 as the first meaningful demo/front-end era and keep the later system-proof milestone behind that.

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
