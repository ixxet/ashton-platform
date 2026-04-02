# ashton-proto

Shared contracts repo for the ASHTON platform.

This repo is not an app. It exists to keep real cross-repo contracts authored,
validated, generated, and reused from one place instead of letting `athena`,
`apollo`, and future services drift.

## Current Role

Today `ashton-proto` owns the active shared contract surface for:

- the common health baseline
- ATHENA's first read-path proto types
- the shared event envelope
- the active `athena.identified_presence.arrived` and
  `athena.identified_presence.departed` event schemas
- the Go runtime helpers that both `athena` and `apollo` use

The important point is operational, not aspirational: producer and consumer now
share one runtime contract instead of carrying private JSON structs.

## Current Real Slice

| Surface | Status | Notes |
| --- | --- | --- |
| Buf package layout under `proto/ashton/...` | Real | Lint-clean and generation-backed |
| Generated Go bindings in `gen/go/...` | Real | Imported by service repos |
| Event envelope schema | Real | Keeps subject naming and outer payload rules stable |
| `athena.identified_presence.arrived` schema | Real | Active arrival contract for visit opening |
| `athena.identified_presence.departed` schema | Real | Active departure contract for visit closing |
| `events/identified_presence_arrived.go` and `events/identified_presence_departed.go` helpers | Real | Shared marshal, parse, source, and timestamp validation |
| Shared fixtures and contract tests | Real | Prevent hand-written payload drift |
| MCP manifest expansion | Deferred | Not widened until routed tool surfaces are real |

## Ownership Rules

| `ashton-proto` owns | `ashton-proto` does not own |
| --- | --- |
| shared wire contracts | service runtime behavior |
| schema validation rules | application business logic |
| generated bindings | deployment and infrastructure policy |
| runtime helpers for active cross-repo payloads | speculative future payload catalogs |

## Current Milestone Truth

- `athena` publishes identified-arrival and identified-departure events
  through this repo
- `apollo` parses those same events through this repo
- runtime validation for source, type, and timestamp semantics happens here once
- contract expansion stays tracer-driven instead of speculative

## Project Shape

| Path | Purpose |
| --- | --- |
| `proto/` | shared protobuf contracts |
| `events/` | JSON schemas, runtime helpers, and fixture bytes |
| `gen/` | generated language bindings |
| `tests/` | contract import and schema validation checks |
| `mcp/` | future shared manifest layer |
| `docs/` | roadmap, runbooks, diagrams, ADR index, and growing pains |

## Verification Standard

Treat this repo as trustworthy only when:

- `buf lint` passes
- `buf generate` is clean and reproducible
- Go tests pass
- active consumer repos still compile and test against the generated output

## Boundary To Other Repos

- `athena` and `apollo` import contracts from here
- `ashton-platform` defines when new contracts are justified
- `Prometheus` does not own any contract truth from this repo

## Deferred On Purpose

- broad proto catalogs for surfaces that do not have a real tracer yet
- gateway-wide manifest authoring before routed tools exist
- breaking version churn while the active contract surface is still narrow
