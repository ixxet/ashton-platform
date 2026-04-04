# ashton-platform

Canonical source-of-truth repo for the ASHTON application stack.

> ASHTON is a contract-first, Go-first platform split across five repos with one
> locked ownership model: `ATHENA` owns physical truth, `APOLLO` owns member
> intent, `HERMES` stays staff-only, and `ashton-mcp-gateway` is a narrow
> control layer widened only after the service surfaces prove they are worth
> routing.

This repo is intentionally not a deployable app. Its job is to keep the system
readable as one coherent platform instead of five drifting repos.

## Start Here

| Reader | Start With | Why |
| --- | --- | --- |
| Recruiter or interviewer | [`Current Platform State`](#current-platform-state), [`Audit Snapshot`](#audit-snapshot), [`Current Real Flow`](#current-real-flow) | These sections show what is already real without making you read every repo |
| Engineer joining the stack | [`Repo Map`](#repo-map), [`Source Of Truth Split`](#source-of-truth-split), [`planning/IMPLEMENTATION-BOARD.md`](planning/IMPLEMENTATION-BOARD.md) | These files define ownership, active release lines, and where runtime truth actually lives |
| Architect or planner | [`planning/audits/2026-04-04-stack-audit.md`](planning/audits/2026-04-04-stack-audit.md), [`planning/sprints/TRACER-MATRIX.md`](planning/sprints/TRACER-MATRIX.md), background essays in `planning/architecture/` | Use the audit and tracer matrix for current state; use the architecture essays only as future/background reference |

## Current Platform State

| Repo | Role | Current State | Why It Matters |
| --- | --- | --- | --- |
| `ashton-proto` | Shared contracts, schemas, runtime helpers | Real and active | Keeps producers and consumers from hand-rolling wire contracts |
| `athena` | Physical truth for presence and occupancy | Real and executable | First Go service, first operational data surface, active visit-lifecycle publisher, and first source-backed ingress adapter locally |
| `apollo` | Member-facing application and intent state | Real and executable, but intentionally narrow | First member auth, profile-state, visit-history, visit-closing, derived-eligibility, explicit workout-runtime, and deterministic recommendation slice |
| `hermes` | Staff-facing operations assistant | Real and executable, but intentionally narrow | First read-only staff occupancy slice over ATHENA public truth |
| `ashton-mcp-gateway` | Shared tool routing, approval, and audit layer | Real and executable, but intentionally narrow | First manifest-backed routed ATHENA occupancy read proves the gateway pattern without widening into auth or approvals |

## Audit Snapshot

Audit reference: [`planning/audits/2026-04-04-stack-audit.md`](planning/audits/2026-04-04-stack-audit.md)

| Repo | Completeness | Code / Mechanics | Docs | Inter-connectedness | Currentness | Short verdict |
| --- | --- | --- | --- | --- | --- | --- |
| `ashton-platform` | `78/100` | `56/100` | `84/100` | `73/100` | `70/100` | Strong control-plane repo, but still markdown-mediated rather than machine-enforced |
| `ashton-proto` | `64/100` | `70/100` | `54/100` | `45/100` | `58/100` | Real shared-contract slice, but still partly hand-stitched and version-drifty |
| `athena` | `47/100` | `82/100` | `84/100` | `56/100` | `91/100` | Best service boundary so far; real and growing, not yet production-complete |
| `apollo` | `46/100` | `81/100` | `67/100` | `55/100` | `86/100` | Usable backend slice, but still short of the full member product |
| `hermes` | `27/100` | `82/100` | `58/100` | `24/100` | `68/100` | Credible read-only CLI tracer, not yet a broad staff platform |
| `ashton-mcp-gateway` | `34/100` | `78/100` | `61/100` | `26/100` | `81/100` | Good routed-tool proof, still too early to claim a mature control plane |

## Architecture

This is the target system view. For current runtime truth, use
[`Current Real Flow`](#current-real-flow) immediately below.

The standalone Mermaid source for the platform system view lives at
[`planning/diagrams/platform-system.mmd`](planning/diagrams/platform-system.mmd).

```mermaid
flowchart TD
  platform["ashton-platform<br/>planning, tracer discipline, source of truth"]
  proto["ashton-proto<br/>protobuf, JSON schema, runtime helpers"]
  athena["athena<br/>presence, occupancy, visit-lifecycle publish"]
  apollo["apollo<br/>visit ingest and close,<br/>auth, profile, eligibility, workouts"]
  hermes["hermes<br/>staff read-only ops<br/>occupancy CLI real"]
  gateway["ashton-mcp-gateway<br/>tool registry, routing<br/>first route real"]

  platform --> proto
  platform --> athena
  platform --> apollo
  platform --> hermes
  platform --> gateway

  proto --> athena
  proto --> apollo
  proto -. future contracts .-> hermes
  proto --> gateway

  athena --> apollo
  athena --> hermes
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
  hermes["hermes<br/>staff occupancy CLI<br/>real"]
  gateway["ashton-mcp-gateway<br/>MCP router<br/>first route real"]

  platform --> proto
  platform --> athena
  platform --> apollo
  platform --> hermes
  platform --> gateway

  proto --> athena
  proto --> apollo
  proto --> hermes
  proto --> gateway

  athena -- "athena.identified_presence.arrived / departed" --> apollo
  athena -- "public occupancy read" --> hermes
  athena -- "public occupancy route via manifest" --> gateway
  apollo -. "future tool routing" .-> gateway
  hermes -. "future tool routing" .-> gateway
```

## Platform Tech Stack

| Layer | Technology | Status | Line | Notes |
| --- | --- | --- | --- | --- |
| Shared contracts | Protobuf + Buf | Instituted | `v0.0.1` -> `v0.0.15` | The current package layout and generation path are active in `ashton-proto` |
| Shared event validation | JSON Schema + Go runtime helpers | Instituted | `v0.0.1` -> `v0.0.15` | `athena` and `apollo` now share one active event helper instead of private structs |
| Physical truth service | Go + chi + Cobra + Prometheus client + NATS | Instituted | `v0.0.1` -> `v0.0.15` | `athena` is the first executable service and now has one narrow source-backed ingress line locally |
| Member ingest and persistence | Go + chi + Cobra + pgx + sqlc + NATS | Instituted | `v0.0.2` -> `v0.0.18` | `apollo` currently focuses on auth, profile state, visit ingest, derived eligibility, workouts, and recommendations |
| Staff assistant | Go CLI + upstream HTTP client | Instituted | `v0.0.12` -> `v0.0.20` | `hermes` now proves one read-only occupancy question over ATHENA's public API |
| Tool control plane | Go-first MCP router, Postgres audit, HITL approval | Instituted, narrow | `v0.0.13` -> `v0.0.21` | `ashton-mcp-gateway` now proves one manifest-backed ATHENA occupancy route; broader control-plane layers stay deferred |
| Redis utility layer | Redis | Deferred | later than `v0.0.21` | Useful later for caches, rate limiting, and ephemeral hot state |
| APOLLO frontend | SvelteKit PWA | Planned | `v0.0.16` | Valuable next as a thin shell over already-real APIs |
| Gateway performance rewrite | Rust | Deferred | later than `v0.0.21` | The rewrite is earned only after a measured Go bottleneck exists |

## Repo Map

| Repo | Owns | Depends On | Current Truth | Docs |
| --- | --- | --- | --- | --- |
| `ashton-proto` | Shared proto packages, event schemas, runtime helper rules | - | Shared contract baseline is real and active | [README](https://github.com/ixxet/ashton-proto/blob/main/README.md) |
| `athena` | Presence, occupancy, ingress source handling, identified visit-lifecycle publication | `ashton-proto` | Mock and CSV-backed ingress feed one canonical occupancy read path; lifecycle publish remains real | [README](https://github.com/ixxet/athena/blob/main/README.md) |
| `apollo` | Member auth, profile state, visit ingest and close, derived eligibility, workout runtime, and deterministic recommendation reads | `ashton-proto`, `athena` | Auth, profile state, visit lifecycle, derived eligibility, workout runtime, and deterministic recommendation reads are real | [README](https://github.com/ixxet/apollo/blob/main/README.md) |
| `hermes` | Staff read-only operations over upstream service truth | `athena` | First occupancy CLI slice is real; write actions, agent orchestration, and deployment stay deferred | [README](https://github.com/ixxet/hermes/blob/main/README.md) |
| `ashton-mcp-gateway` | Tool discovery, routing, approval, audit | `ashton-proto`, `athena` | One manifest-backed ATHENA occupancy route is real; auth, audit storage, and approvals stay deferred | [README](https://github.com/ixxet/ashton-mcp-gateway/blob/main/README.md) |

## Repo Release Lines

| Repo | Current line | Next planned line | Why it is next |
| --- | --- | --- | --- |
| `ashton-proto` | `v0.3.0` shipped, `v0.3.1` unreleased on `main` | `v0.4.0` | broader routed manifest expansion only after a second route is real |
| `athena` | `v0.4.0` | `v0.4.1` only if a later deployment or ingress line needs repo truth changes | the Tracer 10 ingress line is shipped; the next widening should be evidence-driven |
| `apollo` | `v0.6.0` | `v0.7.0` | bounded live departure-close proof did not require new APOLLO repo truth, so the next repo line is the minimal member web shell |
| `hermes` | `v0.1.0` | `v0.1.1` | observability hardening before richer staff widening |
| `ashton-mcp-gateway` | `v0.0.1` shipped, `v0.1.0` unreleased on `main` | `v0.2.0` | caller identity, persisted audit, and a second routed read should come only after the first route is trusted |
| `ashton-platform` | `v0.0.15` | `v0.0.16` | Milestone 1.6 is closed; the next platform line is the minimal member web shell control-plane pass |

## Current State Block

### Already real in the repos

- `ashton-proto` ships Buf-clean packages, event schemas, generated Go code,
  and shared runtime helpers for
  `athena.identified_presence.arrived` and
  `athena.identified_presence.departed`
- `athena` has a canonical occupancy read path shared by CLI, HTTP, and
  Prometheus
- `athena` now has one source-backed CSV ingress adapter that feeds that same
  read path locally without widening deployment truth
- `athena` can publish identified arrival and departure events through the
  shared `ashton-proto` helpers to NATS
- `apollo` can consume those same events, validate them strictly, and open or
  close visits deterministically in Postgres
- `apollo` can verify member ownership, issue signed sessions, persist profile
  state, and derive open-lobby eligibility from explicit member intent
- `apollo` now serves authenticated workout create, update, finish, detail,
  and history reads without letting visits imply exercise activity
- `apollo` now serves one authenticated deterministic workout recommendation
  read derived from explicit workout history only
- `hermes` now serves one executable read-only staff flow:
  `hermes ask occupancy --facility <id>` reads ATHENA's public occupancy count
  and labels the result as ATHENA-backed truth
- `ashton-mcp-gateway` now serves one executable routed read-only flow:
  `athena.get_current_occupancy` is discovered from a real manifest, routed to
  ATHENA's public occupancy endpoint, and logged clearly on success and failure
- `apollo` keeps visit history separate from workout history and from
  recommendation logic and from matchmaking intent
- repo-local roadmaps, runbooks, ADRs, and growing-pains logs exist across the
  stack

### Real and wired across repos

- `athena` and `apollo` now share one runtime event contract instead of private
  JSON structs
- `hermes` now consumes ATHENA's public occupancy endpoint directly instead of
  inventing a private read model or staff-side truth store
- Tracer 2 proved the first end-to-end flow from physical presence to member
  visit history
- anonymous, malformed, duplicate, no-open, out-of-order, and unknown-tag
  visit-lifecycle events now resolve deterministically instead of being left
  ambiguous

### Planned next

The planned release sequence below is the authoritative expansion path. These
bullets are only the short summary.

- richer `hermes` staff questions only after a later tracer proves a real need
  beyond occupancy
- the next gateway line only after caller identity, persisted audit, or a
  second routed read is actually required
- broader `ashton-proto` contract expansion only when a real cross-repo tracer
  requires it

### Deferred on purpose

- Redis-backed hot state and rate limiting
- ATHENA prediction rollout before the read path and adapters widen
- APOLLO recommendation persistence, generated planning, LangGraph, Mem0, and
  matchmaking runtime
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
| `Tracer 5` | visit closing / departure flow | Complete | close the correct open visit from physical departure truth without creating workouts or social intent |
| `Tracer 6` | APOLLO workout runtime | Complete | make explicit member-owned workout history real without letting visits imply exercise activity |
| `Tracer 7` | APOLLO deterministic recommendation runtime | Complete | derive one member-scoped coaching recommendation from explicit workout history without inferring social or physical intent |
| `Tracer 8` | HERMES read-only staff occupancy path | Complete | answer one bounded staff occupancy question from ATHENA public truth without write authority |
| `Tracer 9` | first gateway routed read-only tool call | Complete | prove one manifest-backed ATHENA occupancy route with inspectable logs and no broader control-plane widening |
| `Tracer 10` | first source-backed ATHENA ingress adapter | Complete | prove one real CSV-backed physical-truth ingress line without widening deployment, prediction, or social logic |

## Release History

| Release line | Exact tags | Status | What became real | What stayed deferred |
| --- | --- | --- | --- | --- |
| `v0.0.1` -> `v0.0.6` | `v0.0.1`, `v0.0.2`, `v0.0.3`, `v0.0.4`, `v0.0.5`, `v0.0.6` | Shipped | Tracers 0 through 4 control-plane closeout and source-of-truth alignment | visit close, workout runtime, recommendation runtime, HERMES, and gateway lines |
| `v0.0.8` | `v0.0.8` | Shipped | Tracer 5 control-plane closeout | broader deployment deepening and later product lines |
| `v0.0.9` | `v0.0.9` | Shipped | Milestone 1.5 bounded deployment-deepening closeout | broad APOLLO product deployment and live departure-close proof |
| `v0.0.10` | `v0.0.10` | Shipped | Tracer 6 control-plane closeout | recommendation runtime, HERMES, gateway, and UI lines |
| `v0.0.11` | `v0.0.11` | Shipped | Tracer 7 control-plane closeout | HERMES, gateway, and later APOLLO widening |
| `v0.0.12` | `v0.0.12` | Shipped | Tracer 8 control-plane closeout | gateway, real ingress, and later product widening |
| `v0.0.13` | `v0.0.13` | Shipped | Tracer 9 control-plane closeout | caller identity, persisted audit, and broader gateway maturity |
| `v0.0.14` | `v0.0.14` | Shipped | Tracer 10 control-plane closeout | live source-backed ingress deployment proof and broader deployment widening |
| `v0.0.15` | `v0.0.15` | Shipped | Milestone 1.6 control-plane closeout for bounded live departure-close proof | broader APOLLO product deployment and Tracer 11 widening |

## Planned Release Lines

| Planned tag | Intended purpose | Restrictions | What it should not do yet |
| --- | --- | --- | --- |
| `v0.0.16` | Tracer 11 control-plane closeout for the minimal member web shell | keep the UI limited to already-real APOLLO APIs | do not widen into offline sync, generated plans, or matchmaking UI |
| `v0.0.17` | Tracer 12 control-plane closeout for explicit lobby membership runtime | keep lobby membership separate from visits and eligibility | do not imply invites or auto-entry from tap-in |
| `v0.0.18` | Tracer 13 control-plane closeout for the first deterministic ARES match preview | keep the line read-only and deterministic | do not widen into messaging or autonomous match flows |
| `v0.0.19` | Tracer 14 control-plane closeout for HERMES observability hardening | keep HERMES focused on existing occupancy truth | do not widen into richer staff questions or write actions |
| `v0.0.20` | Milestone 1.7 control-plane closeout for live HERMES deployment proof | keep the staff deployment claim narrow and read-only | do not imply write authority or broad assistant maturity |
| `v0.0.21` | Tracer 15 control-plane closeout for gateway caller identity and persisted audit | keep the gateway line to read-only routing plus audit | do not widen into write approvals or Redis-backed rate limiting |

## Planned Release Sequence

| Planned tag | Vertical | Repo lines in scope | Intended purpose | What it should not do yet |
| --- | --- | --- | --- | --- |
| `v0.0.15` | `Milestone 1.6` | `athena v0.4.0`, `apollo v0.6.0`, companion `Prometheus v0.0.2` | bounded live departure-close proof in-cluster | do not imply broad APOLLO product deployment |
| `v0.0.16` | `Tracer 11` | `apollo v0.7.0` | minimal member web shell over already-real APIs | do not widen into offline sync, generated plans, or matchmaking UI |
| `v0.0.17` | `Tracer 12` | `apollo v0.8.0` | explicit lobby membership runtime | do not imply invites or auto-entry from tap-in |
| `v0.0.18` | `Tracer 13` | `apollo v0.9.0` | first deterministic ARES match preview | do not widen into messaging or autonomous match flows |
| `v0.0.19` | `Tracer 14` | `hermes v0.1.1` | HERMES observability hardening only | do not widen into richer questions or write actions |
| `v0.0.20` | `Milestone 1.7` | `hermes v0.2.0`, companion `Prometheus v0.0.3` | live HERMES deployment proof | do not imply write authority or broad assistant maturity |
| `v0.0.21` | `Tracer 15` | `ashton-mcp-gateway v0.2.0`, optional `ashton-proto v0.4.0` | caller identity, persisted audit, second routed read-only tool | do not widen into write approvals or Redis-backed rate limiting |

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

## Milestone 1.5 Deployment Deepening Truth

Milestone 1.5 deepens deployment truth without rewriting Milestone 1's closure:

- live cluster proof now includes a bounded `ATHENA -> NATS -> APOLLO` arrival
  path
- the ATHENA deployment publishes identified arrivals to live in-cluster NATS
- the APOLLO deployment boots its schema in-cluster, consumes the live subject,
  and persists the visit in Postgres
- one live identified arrival `mock-in-001` created the expected APOLLO visit
- replay of that same live identified arrival resolved as `duplicate` and did
  not create a second visit

This claim is still intentionally narrow:

- it proves the bounded visit-ingest arrival path, not a broad APOLLO product
  rollout
- live departure close behavior remains locally proven, not cluster-proven

## Milestone 1.6 Deployment Hardening Truth

Milestone 1.6 deepens deployment truth without widening the product claim:

- live cluster verification now covers the bounded
  `ATHENA -> NATS -> APOLLO` departure-close path
- live ATHENA remains mock-backed in cluster; this workstream proves the
  boundary, not live source-backed ingress deployment
- one live identified departure `mock-out-001` closed the expected open visit
  for `tracer2-student-001`
- replay of that same live identified departure resolved as `duplicate` and
  did not mutate the visit again
- `apollo.workouts` stayed `0` and sampled unrelated member state
  (`preferences`, active claimed tags) stayed unchanged

This claim is still intentionally narrow:

- it proves bounded live departure-close behavior only
- it does not imply broad APOLLO product deployment
- it does not imply live source-backed ATHENA ingress deployment

## Tracer 6 Runtime Truth

Tracer 6 stays intentionally narrower than Milestone 1.5:

- verified local truth now includes APOLLO workout create, update, finish,
  detail, and history reads behind authenticated member ownership
- those workout surfaces were proven against disposable Postgres with a real
  auth/session smoke path
- workout history ordering is now explicit: newest workout created first using
  DB-owned `started_at DESC, id DESC`
- workout runtime remains separate from visits, eligibility, profile intent,
  and claimed tags
- deployed truth is unchanged from Milestone 1.5 and does not yet claim live
  in-cluster workout runtime surfaces

## Tracer 7 Runtime Truth

Tracer 7 stays intentionally narrower than a broad coaching or planning system:

- verified local truth now includes authenticated
  `GET /api/v1/recommendations/workout`
- recommendation precedence is explicit and deterministic:
  `resume_in_progress_workout`, `start_first_workout`, `recovery_day` for
  workouts finished within `24h`, then `repeat_last_finished_workout`
- recommendation reads are derived from explicit APOLLO-owned workout history,
  not from visits, arrivals, departures, or lobby/profile state
- recommendation reads are side-effect free: they do not create, update, or
  finish workouts and they do not mutate visits, preferences, claimed tags, or
  eligibility state
- deployed truth is unchanged from Milestone 1.5 and does not yet claim live
  in-cluster recommendation surfaces

## Tracer 8 Runtime Truth

Tracer 8 stays intentionally narrower than a broad assistant or workflow layer:

- verified local truth now includes
  `hermes ask occupancy --facility <id>`
- the HERMES runtime answers one bounded staff question only: current
  occupancy at a facility
- HERMES reads ATHENA's public
  `GET /api/v1/presence/count?facility=<id>` surface and returns structured
  source-backed output with `facility_id`, `current_count`, `observed_at`, and
  `source_service`
- the slice is strictly read-only: HERMES does not mutate ATHENA, APOLLO, or
  any staff-owned state and does not invent a private truth store
- Tracer 8 hardening intentionally included missing-input, invalid-config,
  malformed-upstream, and unavailable-upstream checks; those expected failures
  are part of the proof that the slice fails clearly instead of fabricating
  answers
- HERMES success-path observability is still thinner than the APOLLO runtime:
  inspection currently relies more on CLI output and runbook commands than on
  dedicated HERMES request/result logs
- deployed truth is unchanged from Milestone 1.5 and does not yet claim live
  in-cluster HERMES runtime surfaces

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
| `planning/hardening/` | standalone milestone hardening artifacts and policy notes |
| `planning/repo-briefs/` | canonical repo briefs and ownership model |
| `planning/runbooks/` | control-plane and tracer-closure discipline |
| `planning/sprints/` | build order and tracer sequencing |

## Docs Map

- [Stack audit: 2026-04-04](planning/audits/2026-04-04-stack-audit.md)
- [Implementation board](planning/IMPLEMENTATION-BOARD.md)
- [Tracer matrix](planning/sprints/TRACER-MATRIX.md)
- [Build order](planning/sprints/BUILD-ORDER.md) - historical planning reference
- [Hardening artifacts](planning/hardening/README.md)
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

- [Prometheus](https://github.com/ixxet/Prometheus)
- [Talos](https://github.com/ixxet/Talos)

Those repos are the platform substrate. ASHTON documents its own internal
system logic here and treats the homelab layer as an external dependency rather
than mixing infrastructure status into the application architecture.

## Why This Platform Matters

Read together, these repos tell a stronger story than any one service alone:
contract discipline, Go services, event-driven integration, operational
boundaries, documentation maturity, and a deliberate separation between what is
real now and what is only planned.
