# ashton-mcp-gateway

Shared tool routing, approval, and audit layer for the ASHTON platform.

Current truth:

- the repo is now executable, but intentionally narrow
- one real Go runtime exists
- one real manifest-backed ATHENA occupancy route exists
- the first real line is still much smaller than the future full control plane

## Current Repo Truth

- `v0.0.1` is the docs-only planning baseline
- `v0.1.0` is now the active runtime line:
  - one `GET /health`
  - one `POST /mcp/v1/tools/list`
  - one `POST /mcp/v1/tools/call`
  - one registered tool: `athena.get_current_occupancy`
- the runtime loads manifests from `ashton-proto/mcp`
- the runtime calls ATHENA only through its public `GET /api/v1/presence/count`
  surface
- the runtime emits inspectable success and failure logs for the routed path
- caller identity, persisted audit, write approvals, and rate limiting do not
  exist yet

## Immediate Release Lines

| Line | Intended purpose | What becomes real | What stays deferred |
| --- | --- | --- | --- |
| `v0.1.0` | first routed read-only tool call | one Go runtime, one manifest loader, one routed ATHENA occupancy read, one inspectable log path | caller identity, persisted audit, write approvals, rate limiting, Rust |
| `v0.2.0` | caller identity, persisted audit, and second routed read | stronger auditability and a second read-only route | write approvals, rate limiting, Rust |
| `v0.3.0` | first write approval line | explicit HITL handling for write calls | rate limiting, broad orchestration, Rust |
| `v0.4.0` | broader registry and rate limiting | wider registry plus guarded traffic control | Rust rewrite unless a measured bottleneck exists |

## Future Control-Plane Vision

The original gateway vision is still valid. It just does not belong inside the
first routed read-only line.

The long-range control plane still aims to provide:

- manifest-backed discovery across service-owned tools
- one routed entry point for read and later write operations
- caller identity for interactive and automated clients
- persisted audit for every invocation
- explicit HITL approval for write and destructive operations
- low-noise operational metrics and later traffic controls
- a Rust rewrite only if the Go runtime earns it through measured pressure

That future shape stays intentionally deferred until the earlier lines prove the
gateway deserves to exist as a real runtime.

## Deferred Architecture

| Deferred capability | Target line | Why it is deferred from Tracer 9 | Later value if the earlier lines succeed |
| --- | --- | --- | --- |
| Multi-service registry across `athena`, `hermes`, `apollo`, and later `ares` or `system` tools | `v0.2.0` and later | Tracer 9 only needs one manifest and one route to prove discovery and routing | broadens the gateway from one proof path into a true shared control layer |
| Caller identity via Tailscale and API keys | `v0.2.0` | Tracer 9 should prove routing before introducing trust-policy complexity | lets interactive operators and automated systems share one gateway with explicit identity |
| Persisted audit storage for actor, tool, latency, inputs, outputs, and outcome | `v0.2.0` | first-line proof only needs inspectable logs | turns routing into a real auditable control surface instead of a thin proxy |
| Second routed read-only tool call | `v0.2.0` | Tracer 9 should avoid widening beyond one source-backed read | proves the registry and router can widen without collapsing into one-off glue |
| Human-in-the-loop approval for writes | `v0.3.0` | Tracer 9 is read-only and should not invent write governance early | creates the first meaningful safety boundary for tool-triggered mutations |
| Broader CLI/operator workflow surface | `v0.3.0` and later | a large CLI before one stable routed path would be theater | gives operators a practical way to inspect routes, audit entries, and approvals |
| Prometheus metrics for tool volume, latency, approvals, and failures | `v0.3.0` and later | metrics on a single first route are less important than proving the route exists | supports real operational visibility once traffic and approvals are meaningful |
| Redis-backed rate limiting | `v0.4.0` | there is no traffic profile to govern yet | protects the gateway once it fronts multiple tools and callers |
| Broad orchestration posture across many tools | later than `v0.4.0` | Tracer 9 should prove routing, not agent autonomy | becomes credible only after read routing, audit, and write approvals are already real |
| Rust rewrite path | later than `v0.4.0` | there is no Go bottleneck to justify a rewrite | demonstrates measured language choice rather than speculative optimization |

## Tracer 9 Scope Lock

Tracer 9 is the first real gateway slice:

- bootstrap the Go repo
- load one real manifest from `ashton-proto`
- route one read-only tool call
- log the path clearly
- stop

Route target:

- direct ATHENA occupancy, not HERMES

Reason:

- `ashton-proto/mcp` now has an ATHENA-first manifest line
- the first route proves manifest discovery and service routing with the
  smallest possible boundary
- stacking the gateway on top of HERMES first would widen debug and
  observability complexity before the core control-plane shape is proven

## Ownership Boundary

- the gateway owns routing, caller identity, approval, and audit policy
- it does not own physical truth, member truth, or staff truth data
- it consumes routed service surfaces, not private databases
- it widens only after the first routed read-only call is trustworthy

## Why This Repo Matters

The value of the repo right now is no longer just that it has a plan. The value
is that it now has one disciplined first real line: `v0.1.0` proves discovery,
routing, and inspectability without pretending the full agent-control surface
already exists.
