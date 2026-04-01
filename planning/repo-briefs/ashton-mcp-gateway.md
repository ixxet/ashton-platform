# ashton-mcp-gateway

The AI-agent orchestration layer for the ASHTON platform. A unified gateway that registers tool manifests from every service, routes agent tool calls, enforces human-in-the-loop approval on write operations, and audits every invocation.

Any MCP-compatible client — LangGraph agents, Claude, custom automation — can discover and invoke any facility tool through this single entry point. The facility becomes programmable infrastructure that AI agents operate on.

## Architecture

```
  ┌──────────────────┐    ┌──────────────────┐    ┌─────────────┐
  │ LangGraph Agent  │    │ Claude (via MCP)  │    │ CLI / cURL  │
  └────────┬─────────┘    └────────┬─────────┘    └──────┬──────┘
           │                       │                      │
           ▼                       ▼                      ▼
  ┌────────────────────────────────────────────────────────────────┐
  │                    MCP GATEWAY (Go / Rust)                     │
  │                                                                │
  │  ┌──────────────┐  ┌──────────────┐  ┌─────────────────────┐ │
  │  │ Tool Registry│  │  Auth Layer  │  │  Audit Logger       │ │
  │  │              │  │  Tailscale   │  │  Postgres + Loki    │ │
  │  │ Discovers:   │  │  WhoIs +     │  │                     │ │
  │  │  athena.*    │  │  API keys    │  │  Every invocation:  │ │
  │  │  hermes.*    │  │              │  │   who, what, when,  │ │
  │  │  apollo.*    │  │              │  │   input, output,    │ │
  │  │  ares.*      │  │              │  │   latency, outcome  │ │
  │  │  system.*    │  │              │  │                     │ │
  │  └──────┬───────┘  └──────────────┘  └─────────────────────┘ │
  │         │                                                      │
  │  ┌──────┴──────────────────────────────────────────────────┐  │
  │  │                    HITL Gate                             │  │
  │  │                                                          │  │
  │  │  Read operations: pass through immediately               │  │
  │  │  Write operations: hold → notify operator → await approval│ │
  │  │  Destructive operations: require explicit confirmation    │  │
  │  └─────────────────────────────────────────────────────────┘  │
  │         │                                                      │
  │  ┌──────┴──────────────────────────────────────────────────┐  │
  │  │                 Service Router                           │  │
  │  │  athena.*  ──▶ http://athena.ashton.svc:8080            │  │
  │  │  hermes.*  ──▶ http://hermes.ashton.svc:8080            │  │
  │  │  apollo.*  ──▶ http://apollo.ashton.svc:8080            │  │
  │  │  system.*  ──▶ internal (kubectl, flux, pod health)     │  │
  │  └─────────────────────────────────────────────────────────┘  │
  └────────────────────────────────────────────────────────────────┘
```

## Why This Exists

In 2026, the agentic AI pattern is clear: AI systems need structured, discoverable, auditable ways to interact with software. MCP (Model Context Protocol) is the emerging standard. By building a gateway that speaks MCP, every service in the ASHTON platform becomes natively operable by any AI agent without custom integration code.

The gateway also enforces safety: write operations require human approval, every invocation is logged with full context, and rate limiting prevents runaway agent behavior.

## Tech Stack

| Component | Technology | Why |
|-----------|-----------|-----|
| Phase 1 | **Go** | Ship fast, consistent with platform. |
| Phase 2 | **Rust (Axum + tokio)** | Rewrite for performance. Demonstrates polyglot capability and justified language selection. |
| Protocol | **MCP-compatible JSON-RPC over HTTP** | Emerging standard for agent↔tool communication. |
| Tool discovery | **Static manifests from ashton-proto** | Tools registered at startup from `tools.json` files. |
| Auth | **Tailscale WhoIs + API keys** | Mesh identity for interactive agents. API keys for automated pipelines. |
| Audit | **Postgres + structured logs** | Every invocation: actor, tool, input, output, latency, outcome. |
| HITL | **WebSocket notification + approval** | Operator receives pending action, approves or rejects. |
| Rate limiting | **Redis token bucket** | Per-agent, per-tool rate limits. |
| Metrics | **prometheus/client_golang** | Tool call frequency, latency distribution, approval rates. |

## Project Structure

```
ashton-mcp-gateway/
├── cmd/
│   └── gateway/
│       └── main.go
├── internal/
│   ├── registry/              # Tool manifest loading + discovery
│   │   ├── registry.go
│   │   └── manifest.go
│   ├── router/                # Dispatches tool calls to services
│   │   ├── router.go
│   │   └── client.go          # HTTP client pool to backend services
│   ├── auth/                  # Tailscale WhoIs + API key validation
│   │   └── auth.go
│   ├── hitl/                  # Human-in-the-loop approval gate
│   │   ├── gate.go
│   │   └── notifier.go        # WebSocket push to operator
│   ├── audit/                 # Invocation logging
│   │   ├── logger.go
│   │   └── repository.go
│   ├── ratelimit/             # Redis-backed token bucket
│   │   └── limiter.go
│   ├── server/
│   │   ├── server.go
│   │   ├── handlers.go        # MCP protocol handlers
│   │   └── middleware.go
│   └── config/
│       └── config.go
├── pkg/
│   └── mcp/                   # MCP protocol types (reusable)
│       ├── types.go
│       ├── request.go
│       └── response.go
├── db/
│   ├── migrations/
│   └── queries/
├── deploy/
├── docs/
│   ├── growing-pains.md
│   ├── adr/
│   │   ├── 001-mcp-protocol-choice.md
│   │   └── 002-go-to-rust-rewrite.md   # Added after Phase 2
│   ├── security-model.md
│   └── benchmarks/                      # Go vs Rust comparison (Phase 2)
├── Dockerfile
├── Makefile
├── .github/workflows/ci.yml
├── go.mod
└── go.sum
```

## MCP Protocol

### Tool Discovery

```
POST /mcp/v1/tools/list
→ { "tools": [ { "name": "athena.get_current_occupancy", ... }, ... ] }
```

### Tool Invocation

```
POST /mcp/v1/tools/call
{
  "tool": "athena.get_current_occupancy",
  "arguments": { "facility": "ashtonbee" }
}
→ {
  "result": { "count": 187, "capacity": 200, "utilization": 0.935 },
  "metadata": { "latency_ms": 12, "source": "athena", "cached": false }
}
```

### Write Operations (HITL)

```
POST /mcp/v1/tools/call
{
  "tool": "hermes.booking.create",
  "arguments": { "room": "overflow", "end": "20:00" }
}
→ {
  "status": "pending_approval",
  "approval_id": "apr_01HXY...",
  "message": "Write operation requires human approval"
}

# Operator approves via WebSocket or CLI:
POST /mcp/v1/approvals/apr_01HXY.../approve
→ { "result": { "booking_id": "...", "status": "confirmed" } }
```

## CLI Reference

```bash
gateway serve                              # Start the gateway
gateway tools list                         # List all registered tools
gateway tools call [tool] [--args json]    # Invoke a tool (for testing)
gateway audit list [--since] [--tool]      # View invocation audit log
gateway approvals pending                  # List pending HITL approvals
gateway approvals approve [--id]           # Approve a pending action
gateway approvals reject  [--id]           # Reject a pending action
gateway version
```

## Prometheus Metrics

| Metric | Type | Description |
|--------|------|-------------|
| `gateway_tool_calls_total{tool,status}` | counter | Invocations by tool and outcome |
| `gateway_tool_call_duration_seconds{tool}` | histogram | End-to-end tool call latency |
| `gateway_hitl_pending` | gauge | Pending approval count |
| `gateway_hitl_decisions_total{action,outcome}` | counter | Approval/rejection counts |
| `gateway_ratelimit_rejected_total{agent}` | counter | Rate-limited requests |
| `gateway_registered_tools` | gauge | Number of registered tools |

## Rust Rewrite (Phase 2)

After the Go implementation is stable and serving production traffic, the gateway gets rewritten in Rust using Axum + tokio. The rewrite produces:

- A benchmark comparison document (Go vs Rust: latency, memory, throughput)
- An ADR explaining why Rust was justified for this specific component
- Both implementations in the repo (Go in `cmd/`, Rust in `rust/`)

This demonstrates to employers: "I don't pick languages for hype. I shipped in Go for speed, profiled the bottleneck, and rewrote the hot path in Rust with measurable improvement."

## Status

| Milestone | Status |
|-----------|--------|
| Project scaffold | Not started |
| Tool registry + manifest loading | Not started |
| Service router | Not started |
| Auth (Tailscale + API keys) | Not started |
| HITL approval gate | Not started |
| Audit logging | Not started |
| Rate limiting | Not started |
| MCP protocol endpoints | Not started |
| CLI | Not started |
| Prometheus metrics | Not started |
| CI pipeline | Not started |
| Cluster deployment via Flux | Not started |
| Rust rewrite (Phase 2) | Not started |
| Go vs Rust benchmark | Not started |

## Part of the ASHTON Platform

```
                   ashton-proto
                        │
    ┌───────────────────┼───────────────────┐
    ▼                   ▼                   ▼
  athena ◄──────── hermes ────────▶ apollo
    │                   │                   │
    └───────────────────┼───────────────────┘
                        ▼
              [ashton-mcp-gateway]
                        │
           ┌────────────┼────────────┐
           ▼            ▼            ▼
      LangGraph      Claude      Automation
       Agents       (via MCP)     Pipelines
```

The gateway is the top of the pyramid. It depends on all services being deployed
and healthy. Build it last, after ATHENA, HERMES, and APOLLO are stable.

## License

MIT
