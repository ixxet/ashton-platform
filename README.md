# ashton-platform

Canonical source-of-truth repo for the ASHTON application stack.

> ASHTON is a contract-first, Go-first platform split across five repos with one
> locked ownership model: `ATHENA` owns physical truth, `APOLLO` owns member
> intent, `HERMES` stays staff-only, and `ashton-mcp-gateway` is a later control
> layer built only after the service surfaces are worth routing.

This repo is intentionally not a deployable app. Its job is to keep the system
readable as one coherent platform instead of five drifting repos.

## Current Platform State

| Repo | Role | Current State | Why It Matters |
| --- | --- | --- | --- |
| `ashton-proto` | Shared contracts, schemas, runtime helpers | Real and active | Keeps producers and consumers from hand-rolling wire contracts |
| `athena` | Physical truth for presence and occupancy | Real and executable | First Go service and first operational data surface |
| `apollo` | Member-facing application and intent state | Real and executable, but intentionally narrow | First member auth, profile-state, visit-history, and derived-eligibility slice |
| `hermes` | Staff-facing operations assistant | Docs-first | Planned read-only staff layer on top of ATHENA |
| `ashton-mcp-gateway` | Shared tool routing, approval, and audit layer | Docs-first | Planned control surface after the service repos are stable |

## Architecture

The standalone Mermaid source for the platform system view lives at
[`planning/diagrams/platform-system.mmd`](planning/diagrams/platform-system.mmd).

```mermaid
flowchart TD
  platform["ashton-platform<br/>planning, tracer discipline, source of truth"]
  proto["ashton-proto<br/>protobuf, JSON schema, runtime helpers"]
  athena["athena<br/>presence, occupancy, identified arrival publish"]
  apollo["apollo<br/>visit ingest, auth, profile,<br/>derived eligibility now"]
  hermes["hermes<br/>staff operations assistant<br/>planned"]
  gateway["ashton-mcp-gateway<br/>tool registry, routing, HITL, audit<br/>planned"]

  platform --> proto
  platform --> athena
  platform --> apollo
  platform --> hermes
  platform --> gateway

  proto --> athena
  proto --> apollo
  proto -. future contracts .-> hermes
  proto -. future manifests .-> gateway

  athena --> apollo
  athena -. future read surfaces .-> hermes
  athena -. future routed tools .-> gateway
  apollo -. future routed tools .-> gateway
  hermes -. future routed tools .-> gateway
```

## Current Real Flow

The current cross-repo flow is narrower than the long-term architecture on
purpose. The standalone Mermaid source lives at
[`planning/diagrams/platform-current-real-flow.mmd`](planning/diagrams/platform-current-real-flow.mmd).

```mermaid
flowchart LR
  platform["ashton-platform<br/>source of truth"]
  proto["ashton-proto<br/>proto, schemas, runtime helpers"]
  athena["athena<br/>presence, occupancy, event publish"]
  apollo["apollo<br/>visit ingest, member state seed"]
  hermes["hermes<br/>staff ops assistant<br/>planned"]
  gateway["ashton-mcp-gateway<br/>MCP router<br/>planned"]

  platform --> proto
  platform --> athena
  platform --> apollo
  platform --> hermes
  platform --> gateway

  proto --> athena
  proto --> apollo
  proto --> hermes
  proto --> gateway

  athena -- "athena.identified_presence.arrived" --> apollo
  athena -. "future read surfaces" .-> hermes
  athena -. "future tool routing" .-> gateway
  apollo -. "future tool routing" .-> gateway
  hermes -. "future tool routing" .-> gateway
```

## Platform Tech Stack

| Layer | Technology | Status | Notes |
| --- | --- | --- | --- |
| Shared contracts | Protobuf + Buf | Instituted | The current package layout and generation path are active in `ashton-proto` |
| Shared event validation | JSON Schema + Go runtime helpers | Instituted | `athena` and `apollo` now share one active event helper instead of private structs |
| Physical truth service | Go + chi + Cobra + Prometheus client + NATS | Instituted | `athena` is the first executable service |
| Member ingest and persistence | Go + chi + Cobra + pgx + sqlc + NATS | Instituted | `apollo` currently focuses on auth, profile state, visit ingest, and derived eligibility |
| Staff assistant | Go gateway + Python LangGraph sidecar + Mem0 | Planned | `hermes` is intentionally still docs-first |
| Tool control plane | Go-first MCP router, Postgres audit, HITL approval | Planned | `ashton-mcp-gateway` starts only after service surfaces are stable |
| Redis utility layer | Redis | Deferred | Useful later for caches, rate limiting, and ephemeral hot state |
| APOLLO frontend | SvelteKit PWA | Deferred | Valuable later, but not part of the current executable slice |
| Gateway performance rewrite | Rust | Deferred | The rewrite is earned only after a measured Go bottleneck exists |

## Repo Map

| Repo | Owns | Depends On | Current Truth | Docs |
| --- | --- | --- | --- | --- |
| `ashton-proto` | Shared proto packages, event schemas, runtime helper rules | - | Shared contract baseline is real and active | [README](../ashton-proto/README.md) |
| `athena` | Presence, occupancy, ingress source handling, identified-arrival publication | `ashton-proto` | Mock-backed read path and publish path are real | [README](../athena/README.md) |
| `apollo` | Member auth, profile state, visit ingest, and derived eligibility | `ashton-proto`, `athena` | Auth, profile state, visit history, and derived eligibility are real | [README](../apollo/README.md) |
| `hermes` | Staff read-only ops first, later booking and maintenance flows | `ashton-proto`, `athena` | Planned only | [README](../hermes/README.md) |
| `ashton-mcp-gateway` | Tool discovery, routing, approval, audit | All service repos | Planned only | [README](../ashton-mcp-gateway/README.md) |

## Current State Block

### Already real in the repos

- `ashton-proto` ships Buf-clean packages, event schemas, generated Go code, and
  a shared runtime helper for `athena.identified_presence.arrived`
- `athena` has a canonical occupancy read path shared by CLI, HTTP, and
  Prometheus
- `athena` can publish identified-arrival events through the shared
  `ashton-proto` helper to NATS
- `apollo` can consume that same event, validate it strictly, and record a visit
  deterministically in Postgres
- `apollo` can verify member ownership, issue signed sessions, persist profile
  state, and derive open-lobby eligibility from explicit member intent
- `apollo` keeps visit history separate from workout history and from
  matchmaking intent
- repo-local roadmaps, runbooks, ADRs, and growing-pains logs exist across the
  stack

### Real and wired across repos

- `athena` and `apollo` now share one runtime event contract instead of private
  JSON structs
- Tracer 2 proved the first end-to-end flow from physical presence to member
  visit history
- anonymous, malformed, duplicate, and unknown-tag arrivals now resolve
  deterministically instead of being left ambiguous

### Planned next

- `apollo` visit-closing tracer: explicit departure handling without widening
  into workouts or matchmaking
- `hermes` first read-only slice: one staff question answered with real ATHENA
  data
- `ashton-mcp-gateway` first routed read-only tool call after service surfaces
  are stable
- broader `ashton-proto` contract expansion only when a real cross-repo tracer
  requires it

### Deferred on purpose

- Redis-backed hot state and rate limiting
- ATHENA prediction rollout before the read path and adapters widen
- APOLLO recommendation pipelines, LangGraph, Mem0, and matchmaking runtime
- APOLLO SvelteKit PWA and offline sync work
- gateway Rust rewrite before a measured Go bottleneck exists

## Tracer Milestones

| Tracer | Scope | Status | Outcome |
| --- | --- | --- | --- |
| `Tracer 0` | bootstrap and source-of-truth alignment | Complete | repo layering, first contracts, first executable ATHENA slice |
| `Tracer 1` | presence contract to ATHENA read path | Complete | shared contract baseline plus stable mock-backed read surfaces |
| `Tracer 2` | ATHENA event to APOLLO visit record | Complete | first cross-repo event-driven member-history slice |
| `Tracer 3` | APOLLO member auth to profile state | Complete | make member auth and profile state real without widening into matchmaking |
| `Tracer 4` | explicit lobby eligibility | Complete | make explicit member state drive derived lobby eligibility without letting tap-in imply intent |

## Milestone 1 Hardening Truth

Milestone 1 closes on the narrow, honest deployment boundary:

- live cluster proof covers the ATHENA read-path slice only
- the live ATHENA deployment is verified as the mock-backed read service for
  health, occupancy count, and Prometheus metrics
- the live `ATHENA -> NATS -> APOLLO` cluster boundary is explicitly deferred
  and is not part of the Milestone 1 deployment claim

That means the current platform truth is:

- cross-repo event behavior is proven locally with real NATS and Postgres
- APOLLO runtime behavior is proven locally with real auth, profile, and
  eligibility smoke checks
- deployed cluster truth for Milestone 1 is the ATHENA read path, not the full
  event-consumer chain

## Source Of Truth Split

| Need | Use | Why |
| --- | --- | --- |
| Current implementation truth | repo-local `README.md`, `docs/roadmap.md`, ADRs, migrations, schemas, and [`planning/sprints/TRACER-MATRIX.md`](planning/sprints/TRACER-MATRIX.md) | These files track what is actually real now |
| Cross-repo arbitration | [`planning/IMPLEMENTATION-BOARD.md`](planning/IMPLEMENTATION-BOARD.md) and [`planning/runbooks/control-plane.md`](planning/runbooks/control-plane.md) | These files lock ownership, terminology, and tracer discipline |
| Future-state ideas and background rationale | [`planning/architecture/portfolio-architecture.md`](planning/architecture/portfolio-architecture.md) and [`planning/architecture/ashton-addendum-v2.md`](planning/architecture/ashton-addendum-v2.md) | These are background references, not runtime status documents |

The architecture essays still matter, but they are future-leaning. They should
explain where the platform is heading and why earlier choices were made, not
pretend that planned services already exist.

## Project Structure

| Path | Purpose |
| --- | --- |
| `planning/architecture/` | background architecture essays and technology rationale |
| `planning/diagrams/` | standalone Mermaid sources for platform-level diagrams |
| `planning/repo-briefs/` | canonical repo briefs and ownership model |
| `planning/runbooks/` | control-plane and tracer-closure discipline |
| `planning/sprints/` | build order and tracer sequencing |

## Docs Map

- [Implementation board](planning/IMPLEMENTATION-BOARD.md)
- [Tracer matrix](planning/sprints/TRACER-MATRIX.md)
- [Build order](planning/sprints/BUILD-ORDER.md)
- [Control-plane runbook](planning/runbooks/control-plane.md)
- [Platform system diagram](planning/diagrams/platform-system.mmd)
- [Current real flow diagram](planning/diagrams/platform-current-real-flow.mmd)
- [ATHENA brief](planning/repo-briefs/athena.md)
- [APOLLO brief](planning/repo-briefs/apollo.md)
- [HERMES brief](planning/repo-briefs/hermes.md)
- [ASHTON-PROTO brief](planning/repo-briefs/ashton-proto.md)
- [Gateway brief](planning/repo-briefs/ashton-mcp-gateway.md)
- [Portfolio architecture](planning/architecture/portfolio-architecture.md) - background reference
- [Architecture addendum](planning/architecture/ashton-addendum-v2.md) - background reference

## Infra Boundary

Infrastructure remains outside this repo on purpose:

- `../Computers/Prometheus`
- `../Computers/Talos`

Those repos are the platform substrate. ASHTON documents its own internal
system logic here and treats the homelab layer as an external dependency rather
than mixing infrastructure status into the application architecture.

## Why This Platform Matters

Read together, these repos tell a stronger story than any one service alone:
contract discipline, Go services, event-driven integration, operational
boundaries, documentation maturity, and a deliberate separation between what is
real now and what is only planned.
