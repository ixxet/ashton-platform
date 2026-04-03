# ASHTON Implementation Board

This document ties the platform together operationally. It tracks what is locked,
what is deferred, and which documents are authoritative when files disagree.

## Source Of Truth Order

Use this order when planning or implementing:

1. `planning/repo-briefs/`
2. repo-local `README.md` and `docs/roadmap.md`
3. repo-local ADRs
4. repo-local migrations and schemas
5. `planning/sprints/BUILD-ORDER.md`
6. `planning/architecture/` as background reference only

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

Tracer 9 is now closure-clean. The remaining active blockers before the next
major implementation slice are:

1. `ashton-mcp-gateway`
   - keep the next line narrower than the future control-plane vision
   - do not widen beyond read-only routing until caller identity and audit are
     explicitly scoped
2. `ashton-proto`
   - keep manifest expansion tracer-driven now that the first ATHENA manifest is
     real
3. `athena`
   - the next real credibility gain after Tracer 9 is still a real ingress
     adapter, not broader prediction or persistence work
4. `hermes`
   - keep staff operations read-only and sourced from public upstream service
     truth until a later tracer proves a richer question or explicit write
     authority
   - improve HERMES request/result observability before claiming broader
     operational maturity than the current CLI slice proves
5. `apollo`
   - keep recommendation widening deterministic and derived from explicit
     workout history until a later tracer proves persistence or generated plans
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

## Repo Release Lines

| Repo | Current line | Next planned line | Why it is next |
| --- | --- | --- | --- |
| `ashton-proto` | `v0.3.1` | `v0.4.0` | broader routed manifest expansion only after a second route is real |
| `athena` | `v0.3.x` | `v0.4.0` | first real ingress adapter for Tracer 10 |
| `apollo` | `v0.6.0` | `v0.6.1` if Milestone 1.6 needs repo truth changes, otherwise `v0.7.0` | bounded live departure-close support first if needed, then minimal member web shell |
| `hermes` | `v0.1.0` | `v0.1.1` | observability hardening before richer staff widening |
| `ashton-mcp-gateway` | `v0.1.0` | `v0.2.0` | caller identity, persisted audit, and a second routed read after the first route proves itself |
| `ashton-platform` | `v0.0.13` | `v0.0.14` | Tracer 10 control-plane closeout |

## Planned Release Sequence

| Platform tag | Vertical | Repo lines in scope | Intended purpose | Hard stop |
| --- | --- | --- | --- | --- |
| `v0.0.14` | `Tracer 10` | `athena v0.4.0` | first real ingress adapter | no persistence or prediction widening |
| `v0.0.15` | `Milestone 1.6` | `athena v0.4.1`, optional `apollo v0.6.1`, companion `Prometheus v0.0.2` | bounded live departure-close proof in-cluster | no broad APOLLO product deployment claim |
| `v0.0.16` | `Tracer 11` | `apollo v0.7.0` | minimal member web shell over already-real APIs | no offline sync, generated plans, or matchmaking UI |
| `v0.0.17` | `Tracer 12` | `apollo v0.8.0` | explicit lobby membership runtime | no invites or auto-entry from tap-in |
| `v0.0.18` | `Tracer 13` | `apollo v0.9.0` | first deterministic ARES match preview | no messaging or autonomous match flows |
| `v0.0.19` | `Tracer 14` | `hermes v0.1.1` | HERMES observability hardening only | no richer questions or write actions |
| `v0.0.20` | `Milestone 1.7` | `hermes v0.2.0`, companion `Prometheus v0.0.3` | live HERMES deployment proof | no write authority or broad assistant maturity |
| `v0.0.21` | `Tracer 15` | `ashton-mcp-gateway v0.2.0`, optional `ashton-proto v0.4.0` | caller identity, persisted audit, second routed read-only tool | no write approvals or Redis-backed rate limiting |

## Immediate Next Steps

1. Treat `Tracer 9` as the first completed gateway routed read-only slice and `v0.0.13` as its control-plane closeout line.
2. Start `Tracer 10` as the next dedicated implementation chat with the scope locked to one real ATHENA ingress adapter.
3. Keep this thread as the architecture and arbitration thread.
4. Update repo READMEs and repo-local roadmaps before starting implementation work on the next tracer.
5. Update the tracer matrix and repo runbooks after each tracer closes.

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
