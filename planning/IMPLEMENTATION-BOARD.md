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

These should exist before the first real Go implementation work starts:

1. `ashton-proto`
   - standard event envelope
   - naming conventions for event subjects
   - initial contract placeholders without over-specifying payloads
2. `athena`
   - `001_initial` migration for facilities and `presence_events`
   - ADR for capacity prediction
3. `apollo`
   - `001_initial` migration for users, visits, workouts, exercises, and ARES foundations
   - ADR for member state model
   - ADR for member authentication
4. terminology cleanup
   - use `presence` instead of `check-in` in current working docs
   - use `lobby` for product-facing matchmaking terminology in APOLLO

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

## Immediate Next Steps

1. Treat the work completed in this thread as `Tracer 0: bootstrap and alignment`
2. Start a dedicated chat for `Tracer 1`
3. Keep this thread as the architecture and arbitration thread
4. Update the tracer matrix and repo runbooks after each tracer closes
5. Keep bootstrap-only work out of tracer chats unless it is directly blocking the tracer

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
