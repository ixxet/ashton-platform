# ASHTON Implementation Board

This document ties the platform together operationally. It tracks what is locked,
what is deferred, and which documents are authoritative when files disagree.

## Source Of Truth Order

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
| Pre-`1.0.0` rule | Treat current tags as semver-lite release lines, not broad compatibility guarantees |
| Control-plane and deploy repos | `ashton-platform` and deployment repos may use patch bumps as ledger / closeout lines even when service repos use minor bumps for runtime growth |
| History rule | Existing tags stand as history; stricter version discipline applies forward only |

## Repo Release Lines

| Repo | Current line | Next planned line | Why it is next |
| --- | --- | --- | --- |
| `ashton-proto` | `v0.3.0` shipped, `v0.3.1` unreleased on `main` | `v0.4.0` | broader routed manifest expansion only after a second route is real |
| `athena` | `v0.4.1` shipped | `v0.4.2` | broader ingress hardening or durable edge-observation groundwork should come only after the bounded live `v0.4.1` line is trusted |
| `apollo` | `v0.9.0` shipped | `v0.10.0` | recommendation persistence is the next APOLLO widening after the deterministic preview line |
| `hermes` | `v0.1.0` shipped | `v0.1.1` | observability hardening before richer staff widening |
| `ashton-mcp-gateway` | `v0.0.1` shipped, `v0.1.0` unreleased on `main` | `v0.2.0` | caller identity, persisted audit, and a second routed read after the first route proves itself |
| `ashton-platform` | `v0.0.19` shipped | `v0.0.20` | the next platform line should move to HERMES observability hardening |

## Post-ATHENA Ladder

`Cleanup 0` is complete in this docs sync pass. It is intentionally docs-only
and not a tagged runtime line.

| Platform tag | Vertical | Repo lines in scope | Intended purpose | Hard stop |
| --- | --- | --- | --- | --- |
| `v0.0.20` | `Tracer 14` | `hermes v0.1.1` | HERMES observability hardening only | no richer questions or write actions |
| `v0.0.21` | `Milestone 1.7` | `hermes v0.2.0`, companion later `Prometheus` line if needed | live HERMES deployment proof | no write authority or broad assistant maturity |
| `v0.0.22` | `Tracer 15` | `ashton-mcp-gateway v0.2.0`, optional `ashton-proto v0.4.0` | caller identity, persisted audit, and second routed read | no write approvals yet |
| `v0.0.23` | `Tracer 16` | `athena v0.4.2` | durable edge-observation groundwork and ingress hardening | no prediction or broad rollout claims |
| `v0.0.24` | `Tracer 17` | `hermes v0.3.0`, maybe bounded `athena` read support | one richer read-only staff question | no overrides or writes |
| later than `v0.0.24` | `System-Proof Milestone` | cross-repo | verify the tracer ladder and deployment milestones as one modular system | no new feature surface |

## What Each Next Line Must Achieve

| Line | Concrete outcome | Why it matters |
| --- | --- | --- |
| `Tracer 14` | HERMES logs request, result, and outcome clearly with low-noise structured logs | makes the staff slice operationally inspectable |
| `Milestone 1.7` | live HERMES occupancy read is deployed and verified | upgrades the staff pillar from local truth to deployment truth |
| `Tracer 15` | gateway gains caller identity, persisted audit, and one second routed read | turns the control plane from a demo route into a trusted narrow layer |
| `Tracer 16` | ATHENA gains durable edge-observation groundwork | removes all-memory dependence and sets up operator history / reconciliation groundwork |
| `Tracer 17` | HERMES answers one richer staff question from stable public upstream truth | creates the first real operator / reconciliation read surface |
| `System-Proof Milestone` | system-level proof of runtime truth, deployment truth, modularity, and maintenance model | shifts the platform from a pile of tracers to a coherent system |

## Test / Proof Plan By Line

| Line | Minimum proof |
| --- | --- |
| `Tracer 14` | repeated HERMES runs, success/failure logs, no runtime widening |
| `Milestone 1.7` | rollout proof, live occupancy read, cluster smoke, docs aligned |
| `Tracer 15` | repeated routed reads, caller identity proof, persisted audit proof, second route proof |
| `Tracer 16` | durable-history groundwork behaves deterministically, restart / reload story is explicit, and no raw-ID leakage regresses |
| `Tracer 17` | richer question is source-backed, deterministic, read-only, and operationally useful |
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
5. Keep `Tracer 16`, `Tracer 17`, and the later system-proof milestone as the next foundation-first ladder after the staff and gateway pillars strengthen.

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
- this does not imply live source-backed ATHENA ingress deployment

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
