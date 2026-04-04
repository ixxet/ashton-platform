# ASHTON Platform — Historical Repo Map and Initial Build Order

> Status: historical planning reference.
>
> This file preserves the original repo-creation sequence and the first
> expected sprint order. It is not the current source of runtime truth.
>
> Use these files instead for current truth:
> - `planning/IMPLEMENTATION-BOARD.md`
> - `planning/sprints/TRACER-MATRIX.md`
> - repo-local `README.md` and `docs/roadmap.md`

## The Pyramid

```
                    ▲
                   ╱ ╲
                  ╱   ╲
                 ╱ MCP  ╲          ashton-mcp-gateway
                ╱ GATEWAY ╲        (Go → Rust rewrite)
               ╱───────────╲
              ╱  APPLICATIONS╲
             ╱                ╲
            ╱ HERMES   APOLLO  ╲   hermes, apollo
           ╱  (ops bot) (train) ╲  (Go + LangGraph)
          ╱─────────────────────╲
         ╱     ATHENA (Go)       ╲  athena
        ╱   facility intelligence ╲ (first Go service)
       ╱─────────────────────────────╲
      ╱      SHARED CONTRACTS         ╲  ashton-proto
     ╱   proto, events, MCP manifests  ╲ (schemas only)
    ╱───────────────────────────────────╲
   ╱         PROMETHEUS (exists)         ╲  homelab-gitops
  ╱  Talos · K8s · Cilium · Flux · GPU   ╲ tower-bootstrap
 ╱─────────────────────────────────────────╲
```

## All Repos (8 Total)

| # | Repo Name | Layer | Language | Historical status at planning time | Depends On |
|---|-----------|-------|----------|--------|------------|
| 1 | `homelab-gitops` | 0 — Platform | YAML/HCL | **Exists** | — |
| 2 | `tower-bootstrap` | 0 — Platform | Shell/YAML | **Exists** | — |
| 3 | `ashton-proto` | 1 — Contracts | Protobuf/JSON | **Create now** | Nothing |
| 4 | `athena` | 2 — First App | Go | **Create now** | ashton-proto |
| 5 | `hermes` | 3 — App | Go + Python | **Create now** | ashton-proto, athena |
| 6 | `apollo` | 3 — App | Go + Python + Svelte | **Create now** | ashton-proto, athena |
| 7 | `ashton-mcp-gateway` | 4 — Gateway | Go → Rust | **Create now** | ashton-proto, all services |
| 8 | `User-Adaptive-Summarization_COMP385-402_Group-4_Winter2026` | — | Python + Svelte | **Exists** | — |

## Build Order (Sprint Sequence)

This is the original build hypothesis. Some repo order and internal scope still
matter, but the authoritative sequence now lives in the tracer matrix and the
implementation board.

### Sprint 0: Schema Foundation (Week 1 post-graduation)
**Repo: `ashton-proto`**
- Define common protobuf types
- Define ATHENA proto messages
- Set up buf.yaml and generation pipeline
- Define NATS event envelope schema
- Define ATHENA MCP tool manifest
- CI: buf lint + buf breaking

### Sprint 1-4: ATHENA (Weeks 2-5)
**Repo: `athena`**
- Week 1: Go scaffold, Postgres schema, sqlc, migrations, first handler
- Week 2: Presence service, MockAdapter, repository layer, tests
- Week 3: Capacity predictor, CLI, Prometheus metrics, NATS publishing
- Week 4: Grafana dashboard, MCP manifest, cluster deployment via Flux
- **Gate: ATHENA serving on cluster, observable in Grafana, CLI working**

### Sprint 5: Proto Expansion (Week 6)
**Repo: `ashton-proto`**
- Add HERMES proto messages (bookings, equipment, maintenance)
- Add APOLLO proto messages (workouts, recommendations)
- Add ARES proto messages (ratings, matches)
- Add HERMES + APOLLO MCP tool manifests

### Sprint 6-9: HERMES (Weeks 7-10)
**Repo: `hermes`**
- Week 1: Go WebSocket gateway, gRPC bridge scaffold, Tailscale auth
- Week 2: LangGraph agent, ATHENA tool integration, HITL flow
- Week 3: Booking + equipment + maintenance services
- Week 4: NATS listener, Mem0 integration, CLI, metrics, Flux deploy
- **Gate: HERMES answering queries over Tailscale, booking rooms with HITL**

### Sprint 10-13: APOLLO (Weeks 11-14)
**Repo: `apollo`**
- Week 1: Profile + visit/workout API, Postgres schema, Go handlers
- Week 2: Training history analytics, volume/frequency calculations
- Week 3: Recommendation pipeline (LangGraph + vLLM), ARES rating engine
- Week 4: SvelteKit PWA, offline support, matchmaking lobby, Flux deploy
- **Gate: Members logging workouts, receiving recs, matchmaking functional**

### Sprint 14-15: MCP Gateway (Weeks 15-16)
**Repo: `ashton-mcp-gateway`**
- Week 1: Tool registry, service router, auth, HITL gate (Go)
- Week 2: Audit logging, rate limiting, CLI, metrics, Flux deploy
- **Gate: All tools discoverable and invocable through single endpoint**

### Sprint 16+: Rust Rewrite, Real Users, Polish
- Rust rewrite of MCP gateway (Axum + tokio)
- Go vs Rust benchmark document
- Connect ATHENA to real tap-in system
- Onboard Ashtonbee users onto APOLLO
- Stress test under real load
- Growing-pains documentation from real incidents

## GitHub Repo Creation Checklist

For each repo, when you create it on GitHub:

- [x] Public visibility
- [x] MIT license
- [x] README.md (provided in this package)
- [x] .gitignore (Go template for Go repos, Python for agent code)
- [x] No template — start empty
- [x] Main branch protection: require CI to pass before merge

## Naming Convention

All repos use lowercase kebab-case. The platform name "ASHTON" appears only in
the shared repos (`ashton-proto`, `ashton-mcp-gateway`). Individual services use
their mythological names directly (`athena`, `hermes`, `apollo`).

This keeps GitHub URLs clean:
```
github.com/ixxet/ashton-proto
github.com/ixxet/athena
github.com/ixxet/hermes
github.com/ixxet/apollo
github.com/ixxet/ashton-mcp-gateway
```

## What Each Repo Signals to an Employer

| Repo | Signal |
|------|--------|
| `homelab-gitops` | "I operate production Kubernetes with GitOps" |
| `tower-bootstrap` | "I built the infrastructure from bare metal" |
| `ashton-proto` | "I design contracts before writing code" |
| `athena` | "I write production Go with real users" |
| `hermes` | "I build agentic AI systems with tool calling and safety gates" |
| `apollo` | "I build full-stack applications with ML pipelines" |
| `ashton-mcp-gateway` | "I understand the agentic AI infrastructure pattern" |
| Capstone | "I ship tested software with CI and documentation" |
