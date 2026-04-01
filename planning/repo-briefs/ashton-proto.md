# ashton-proto

Shared protocol buffer definitions, event schemas, and MCP tool manifests for the ASHTON platform.

Every service in the platform imports from this repo. Nothing here runs independently — it exists to enforce type safety and schema consistency across `athena`, `hermes`, `apollo`, and `ashton-mcp-gateway`.

## What Lives Here

```
ashton-proto/
├── proto/                      # Protocol Buffer definitions
│   ├── common/
│   │   └── v1/
│   │       ├── common.proto    # Shared types: Timestamp, UUID, Pagination, Error
│   │       └── health.proto    # Standardized health check response
│   ├── athena/
│   │   └── v1/
│   │       └── athena.proto    # CheckinEvent, CapacitySnapshot, PredictionResult
│   ├── hermes/
│   │   └── v1/
│   │       └── hermes.proto    # Booking, Equipment, MaintenanceTicket, ChatMessage
│   ├── apollo/
│   │   └── v1/
│   │       ├── apollo.proto    # Workout, Exercise, Recommendation, UserProfile
│   │       └── ares.proto      # Rating, Match, MatchPlayer, TeamFormation
│   └── gateway/
│       └── v1/
│           └── gateway.proto   # ToolManifest, ToolInvocation, AuditEntry
├── events/                     # NATS event envelope schemas (JSON Schema)
│   ├── envelope.schema.json    # Standard event envelope: id, source, type, timestamp, data
│   ├── athena.checkin.schema.json
│   ├── athena.capacity.schema.json
│   ├── hermes.booking.schema.json
│   ├── apollo.workout.schema.json
│   └── apollo.ares.match.schema.json
├── mcp/                        # MCP tool manifest definitions
│   ├── athena.tools.json
│   ├── hermes.tools.json
│   ├── apollo.tools.json
│   └── ares.tools.json
├── sql/                        # Shared SQL migration conventions
│   └── naming.md              # Table, column, index naming standards
├── buf.yaml                   # Buf configuration for proto linting and generation
├── buf.gen.yaml               # Code generation targets (Go, Python)
└── Makefile                   # generate, lint, breaking-change-detect
```

## Why a Separate Repo

- **Single source of truth** for all inter-service contracts
- **Breaking change detection** via `buf breaking` against the main branch
- **Polyglot generation** — Go structs and Python dataclasses from the same `.proto` files
- **Event schema validation** — NATS consumers validate payloads against JSON Schemas at runtime
- **MCP manifests** — the gateway discovers tools from these definitions, not from service code

## Code Generation

```bash
# Install buf (https://buf.build/docs/installation)
# Generate Go and Python code from proto definitions
make generate

# Lint protos for style compliance
make lint

# Check for breaking changes against main branch
make breaking
```

Generated code lands in:
- `gen/go/` — Go packages, importable by athena/hermes/apollo/gateway
- `gen/python/` — Python packages, importable by LangGraph agent code

## Event Envelope Standard

Every NATS event follows this envelope:

```json
{
  "id": "evt_01HXY...",
  "source": "athena",
  "type": "checkin.occurred",
  "timestamp": "2026-07-15T18:30:00Z",
  "correlation_id": "req_01HXY...",
  "data": { }
}
```

Services publish and subscribe using subject patterns: `{service}.{entity}.{action}`

## MCP Tool Manifest Standard

Each service publishes a tool manifest that the MCP gateway registers:

```json
{
  "name": "athena.get_current_occupancy",
  "description": "Returns real-time facility occupancy count and utilization ratio",
  "input_schema": { },
  "output_schema": { },
  "requires_approval": false,
  "category": "read"
}
```

Tools with `"requires_approval": true` trigger HITL confirmation in the gateway before execution.

## Versioning

Proto packages use `/v1/` versioning. Breaking changes create `/v2/`. Non-breaking additions stay in the current version. Buf enforces this.

## Status

| Component | Status |
|-----------|--------|
| Common types | Not started |
| ATHENA protos | Not started |
| HERMES protos | Not started |
| APOLLO / ARES protos | Not started |
| Gateway protos | Not started |
| Event schemas | Not started |
| MCP manifests | Not started |
| CI (buf lint + breaking) | Not started |

## Part of the ASHTON Platform

```
ashton-proto ◄── athena, hermes, apollo, ashton-mcp-gateway
     │
     └── Shared contracts that enforce consistency across all services
```

## License

MIT
