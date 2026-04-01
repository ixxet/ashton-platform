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

1. Finalize `ashton-proto` envelope and placeholder contract files
2. Finalize `athena` and `apollo` migrations
3. Finalize APOLLO auth and member-state ADRs
4. Finalize ATHENA prediction ADR
5. Start `athena` implementation with `go.mod`, core types, and adapter interfaces
