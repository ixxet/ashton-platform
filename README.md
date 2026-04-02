# ashton-platform

Canonical source-of-truth repo for the ASHTON platform.

This repo is intentionally not a deployable application. It holds the planning material that coordinates the platform repos:

- `ashton-proto`
- `athena`
- `hermes`
- `apollo`
- `ashton-mcp-gateway`

## Current Sprint

Bootstrap, `Tracer 1`, and `Tracer 2` are complete. The platform now has
aligned docs, a narrow reproducible contract baseline in `ashton-proto`, a
stable ATHENA mock read path, and the first cross-repo event integration that
turns physical presence into member visit history.

Tracer 1 was also the first foundation tracer where contracts, executable
behavior, image publishing, GitOps pinning, and live rollout verification all
had to become real together. The planning repo preserves that narrative in the
tracer matrix so later implementation chats can inherit the lessons without
having to rediscover the same bootstrap friction.

Tracer 2 also closed the runtime contract gap instead of stopping at a working
payload shape:

- `ashton-proto` now publishes the shared runtime helper for
  `athena.identified_presence.arrived`
- `athena` publishes through that helper instead of a private JSON contract
- `apollo` consumes the same helper and rejects invalid source, type, enum, and
  timestamp values before visit persistence
- local manual smoke passed for `apollo serve`, `athena presence publish-identified`,
  and `athena serve` with the publisher worker against real NATS and Postgres

The next recommended implementation chat is `Tracer 3`:

- turn APOLLO auth into profile state
- keep identity linkage separate from auth
- avoid widening into workouts, matchmaking, HERMES, or gateway work unless the
  tracer truly requires it

## Current Model

The platform ownership split is:

- `ATHENA` owns physical truth: presence, ingress source, occupancy, and later zone occupancy
- `APOLLO` owns member intent: profile, privacy, availability, workouts, coaching, and ARES
- `HERMES` stays staff-only and consumes operational data instead of student social state

The key rule is: tap-in updates presence, not matchmaking intent.

## Workspace Layout

- `planning/architecture/`
  Canonical platform architecture documents.
- `planning/repo-briefs/`
  Canonical service and repo briefs.
- `planning/sprints/`
  Build order and sprint sequencing.
- `planning/diagrams/`
  Generated diagrams and architecture visuals.
- `planning/archive/`
  Raw packaged material and non-canonical source assets.

## Repo Map

| Repo | Role | Depends On | Current State |
| --- | --- | --- | --- |
| `ashton-proto` | Shared contracts, events, MCP manifests | — | Tracer 2 runtime-shared event contract complete |
| `athena` | Physical truth layer for presence and occupancy | `ashton-proto` | Tracer 2 shared publisher path complete |
| `hermes` | Staff operations assistant | `ashton-proto`, `athena` | Docs-first stub |
| `apollo` | Multi-mode member app: profile, coaching, workouts, and matchmaking | `ashton-proto`, `athena` | Tracer 2 visit-ingest slice complete |
| `ashton-mcp-gateway` | Shared MCP tool gateway and safety layer | all service repos | Docs-first stub |

## Build Order

1. `ashton-proto`
2. `athena`
3. `hermes`
4. `apollo`
5. `ashton-mcp-gateway`

The authoritative build sequence lives in [planning/sprints/BUILD-ORDER.md](planning/sprints/BUILD-ORDER.md).

## Canonical Planning Docs

- [Implementation board](planning/IMPLEMENTATION-BOARD.md)
- [Tracer matrix](planning/sprints/TRACER-MATRIX.md)
- [Tracer closure-hardening template](planning/runbooks/tracer-closure-hardening-template.md)
- [Platform architecture](planning/architecture/portfolio-architecture.md)
- [Architecture addendum](planning/architecture/ashton-addendum-v2.md)
- [Build order](planning/sprints/BUILD-ORDER.md)
- [ATHENA brief](planning/repo-briefs/athena.md)
- [HERMES brief](planning/repo-briefs/hermes.md)
- [APOLLO brief](planning/repo-briefs/apollo.md)
- [ASHTON-PROTO brief](planning/repo-briefs/ashton-proto.md)
- [Gateway brief](planning/repo-briefs/ashton-mcp-gateway.md)

For current implementation decisions, the repo briefs, ADRs, migrations, the implementation board, and the tracer matrix are the working source of truth. The longer architecture essays remain useful background, but they may lag behind recent pivots and are explicitly treated as background reference.

## Infra Boundary

Infrastructure stays outside this workspace:

- `../Computers/Prometheus`
- `../Computers/Talos`

That repo remains the platform foundation and is intentionally outside this planning repo.

## Repo Status

The repo briefs in `planning/repo-briefs/` are the working source of truth for implementation decisions. The longer architecture essays remain useful background, but they may lag behind recent pivots.
