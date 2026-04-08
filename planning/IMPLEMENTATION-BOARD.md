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
   - keep the next line narrower than the future control-plane vision
   - do not widen beyond read-only routing until caller identity and audit are
     explicitly scoped
2. `ashton-proto`
   - keep manifest expansion tracer-driven now that the first ATHENA manifest is
     real
3. `athena`
   - keep the new bounded live edge-ingress deployment narrower than any broad
     source-backed ingress rollout
   - do not widen into broader prediction, persistence, or admin workflow work
     next
4. `hermes`
   - keep staff operations read-only and sourced from public upstream service
     truth until a later tracer proves a richer question or explicit write
     authority
   - improve HERMES request/result observability before claiming broader
     operational maturity than the current CLI slice proves
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
| `ashton-proto` | `v0.3.0` shipped, `v0.3.1` unreleased on `main` | `v0.4.0` | broader routed manifest expansion only after a second route is real |
| `athena` | `v0.4.1` shipped | `v0.5.0` | durable edge-observation groundwork is the next true ATHENA capability line after the bounded live `v0.4.1` line is trusted |
| `apollo` | `v0.9.0` shipped | `v0.10.0` | workout planner, exercise library, templates, and richer profile inputs are the next investor-visible APOLLO widening after deterministic preview |
| `hermes` | `v0.1.0` shipped | `v0.1.1` | observability hardening before richer staff widening |
| `ashton-mcp-gateway` | `v0.0.1` shipped, `v0.1.0` unreleased on `main` | `v0.2.0` | caller identity, persisted audit, and a second routed read after the first route proves itself |
| `ashton-platform` | `v0.0.19` shipped | `v0.0.20` | the next platform line should move to HERMES observability hardening |

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

## Post-ATHENA Ladder

`Cleanup 0` is complete in this docs sync pass. It is intentionally docs-only
and not a tagged runtime line.

| Platform tag | Vertical | Repo lines in scope | Intended purpose | Hard stop |
| --- | --- | --- | --- | --- |
| `v0.0.20` | `Tracer 14` | `hermes v0.1.1` | HERMES observability hardening only | no richer questions or write actions |
| `v0.0.21` | `Milestone 1.7` | `hermes v0.1.2` if runtime changes, otherwise deploy closeout only, companion later `Prometheus` line if needed | live HERMES deployment proof once a bounded `Prometheus` HERMES slice exists | no write authority or broad assistant maturity |
| `v0.0.22` | `Tracer 15` | `ashton-mcp-gateway v0.2.0`, optional `ashton-proto v0.4.0` | caller identity, persisted audit, and second routed read | no write approvals yet |
| `v0.0.23` | `Tracer 16` | `athena v0.5.0` | durable edge-observation groundwork and ingress hardening | no prediction or broad rollout claims |
| `v0.0.24` | `Tracer 17` | `hermes v0.2.0`, maybe bounded `athena` read support | one richer read-only staff question | no overrides or writes |
| `v0.0.25` | `Tracer 18` | `apollo v0.10.0` | workout planner, exercise library, templates, and richer profile inputs | no medical claims or LLM-first logic |
| `v0.0.26` | `Tracer 19` | `apollo v0.11.0` | conservative deterministic recommendation engine plus calorie / macro ranges and low-friction meal logging | no diagnosis or opaque black box |
| `v0.0.27` | `Milestone 2.0` | deploy closeout only unless runtime truth changes | investor-light deployed platform: shell, planner, conservative coach, live ATHENA, live HERMES | no broad public leaderboard yet |
| later than `Milestone 2.0` | long-horizon ladder in `planning/STARSHOT-VISION.md` | `Tracer 20+` and later system-proof | expand into session truth, ratings, rivalries, public highlights, later social, and system-proof only after the earlier truth exists | do not skip the investor-light demo lane |

## What Each Next Line Must Achieve

| Line | Concrete outcome | Why it matters |
| --- | --- | --- |
| `Tracer 14` | HERMES logs request, result, and outcome clearly with low-noise structured logs | makes the staff slice operationally inspectable |
| `Milestone 1.7` | live HERMES occupancy read is deployed and verified after the deploy repo grows a bounded HERMES slice | upgrades the staff pillar from local truth to deployment truth |
| `Tracer 15` | gateway gains caller identity, persisted audit, and one second routed read | turns the control plane from a demo route into a trusted narrow layer |
| `Tracer 16` | ATHENA gains durable edge-observation groundwork | removes all-memory dependence and sets up operator history / reconciliation groundwork |
| `Tracer 17` | HERMES answers one richer staff question from stable public upstream truth | creates the first real operator / reconciliation read surface |
| `Tracer 18` | APOLLO gains planner, templates, exercise library, and richer profile inputs | creates the first investor-visible fitness-product loop |
| `Tracer 19` | APOLLO gains conservative deterministic coaching and nutrition guidance | connects profile, plan, and recommendations without risky claims |
| `Milestone 2.0` | the member platform is deployable as a coherent demo slice | turns the platform from a stack of narrow truths into an investor-legible core product |
| `System-Proof Milestone` | system-level proof of runtime truth, deployment truth, modularity, and maintenance model | shifts the platform from a pile of tracers to a coherent system |

## Scope Bounds For The Next Five Lines

| Line | In scope | Out of scope |
| --- | --- | --- |
| `Tracer 15` | caller identity for routed reads, persisted audit, and one second routed read | write approvals, broad orchestration, auth-platform rewrite |
| `Tracer 16` | durable edge observation history, rebuild / replay groundwork, and privacy-safe history proof | prediction rollout, override tooling, identity reconciliation UI |
| `Tracer 17` | one richer read-only operator question sourced from stable upstream truth | writes, overrides, private DB shortcuts, broad assistant UX |
| `Tracer 18` | weekly planner, exercise library, machine dropdowns, templates/loadouts, richer profile goals/body metrics | medical claims, LLM-first recommendation logic, broad nutrition coaching |
| `Tracer 19` | deterministic conservative recommendations, calorie / macro ranges, low-friction meal logging, explicit limitations | diagnosis, clinical advice, opaque model outputs, obsessive nutrition sprawl |

## Product Guardrails

| Topic | Recommendation |
| --- | --- |
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
| `Milestone 1.7` | rollout proof, live occupancy read, cluster smoke, docs aligned |
| `Tracer 15` | repeated routed reads, caller identity proof, persisted audit proof, second route proof |
| `Tracer 16` | durable-history groundwork behaves deterministically, restart / reload story is explicit, and no raw-ID leakage regresses |
| `Tracer 17` | richer question is source-backed, deterministic, read-only, and operationally useful |
| `Tracer 18` | invalid exercise or machine IDs fail cleanly, impossible planner states are rejected, templates stay ownership-safe, and planner writes do not mutate unrelated domains |
| `Tracer 19` | extreme stats, missing history, cold starts, and deterministic reruns all behave conservatively without medical overclaiming |
| `Milestone 2.0` | deployed member shell, planner, conservative coach, live ATHENA, and live HERMES all prove the same bounded demo story |
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
2. Start `Tracer 14` as the next execution line and keep it strictly observability-only.
3. Use `Milestone 1.7` to convert the same HERMES slice from local/runtime truth into bounded live deployment truth.
4. Use `Tracer 15` to strengthen gateway trust before any approval or write widening.
5. Use `Tracer 16` and `Tracer 17` to finish the next foundation-first ladder before product widening.
6. Use `Tracer 18`, `Tracer 19`, and `Milestone 2.0` as the shortest path to an investor-light deployed platform.
7. Keep `Tracer 20+` and the later system-proof milestone behind that demo path unless urgency changes.

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
