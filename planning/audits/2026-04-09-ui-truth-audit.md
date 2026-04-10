# 2026-04-09 UI Truth Audit

This audit grounds the current web-product vision against the actual runtime and
contract truth in:

- `apollo`
- `athena`
- `hermes`
- `ashton-mcp-gateway`
- `ashton-proto`

This is not a roadmap.
This is not a frontend concept deck.
It is a truth-first audit of what the platform can support now, what is
backend-real but still hidden, what is missing, and what should not be forced
into the wrong repo.

## Audit Method

This pass used:

- `ashton-platform` implementation and repo-brief docs as arbitration
- repo-local READMEs, roadmaps, runbooks, ADRs, and hardening docs
- runtime entrypoints and HTTP/CLI surfaces in code
- per-repo sub-agent review for docs and runtime boundaries where useful

When docs conflicted, newer roadmap and hardening lines were treated as more
authoritative than older summary text.

## Executive Summary

### Already Real Enough To Ground UI Work

- member auth and session cookies in `apollo`
- member profile, lobby eligibility, explicit lobby membership, workouts, and
  deterministic workout recommendation in `apollo`
- planner substrate and competition substrate/history APIs in `apollo`
- occupancy counts, facility catalog truth, and bounded edge/tap history in
  `athena`
- read-only staff occupancy and reconciliation truth in `hermes`
- narrow caller-aware routed read surfaces in `ashton-mcp-gateway`
- shared presence-event and occupancy contracts in `ashton-proto`

### Backend-Real But Not Yet A Real Product Surface

- planner APIs in `apollo`
- competition session / queue / result / member-stats APIs in `apollo`
- facility catalog and history reads in `athena`
- reconciliation read in `hermes`
- gateway tool routing beyond two narrow ATHENA reads

### Missing For The Envisioned Product

- real role model for `supervisor`, `manager`, and `owner`
- member-facing visits / tap history / claimed-tag linking flow
- meal / macro / nutrition runtime
- badges / achievements / avatars / shareable public profile identity
- booking / rentals / derived scheduling engine
- calendar conflict logic over closures, events, and rentals
- staff web runtime and staff HTTP API boundary
- public competition reads and social features
- stable shared auth / error / pagination / role contracts across repos

### Major Constraint

The current platform is intentionally more backend-real than frontend-ready.
That is not a flaw. It is the current architecture posture.

The right near-term move is to expose and compose the truth that already exists,
not to force broad product logic into the wrong services.

## Repo Truth

## APOLLO

### Real Now

- `apollo` is the only repo with a real browser shell today:
  [web_ui.go](/Users/zizo/Personal-Projects/ASHTON/apollo/internal/server/web_ui.go)
- member auth is real:
  [002-member-auth.md](/Users/zizo/Personal-Projects/ASHTON/apollo/docs/adr/002-member-auth.md)
- the current shell already covers login, profile summary, lobby membership,
  match preview, workout list/detail/edit/finish, and recommendation:
  [shell.html](/Users/zizo/Personal-Projects/ASHTON/apollo/internal/server/web/templates/shell.html)
- planner APIs are real but not yet surfaced in the shell:
  [server.go](/Users/zizo/Personal-Projects/ASHTON/apollo/internal/server/server.go#L275)
- competition and member-stats APIs are real but still internal/authenticated
  product substrate, not public-facing UI truth:
  [server.go](/Users/zizo/Personal-Projects/ASHTON/apollo/internal/server/server.go#L429)

### Partial Or Hidden

- planner substrate is real, but backend-first and intentionally separate from
  workout runtime:
  [tracer-23.md](/Users/zizo/Personal-Projects/ASHTON/apollo/docs/hardening/tracer-23.md)
- competition execution and history are real in repo/runtime, but not a broad
  member or staff shell yet:
  [roadmap.md](/Users/zizo/Personal-Projects/ASHTON/apollo/docs/roadmap.md)
- member stats exist, but only as self-scoped reads:
  [server.go](/Users/zizo/Personal-Projects/ASHTON/apollo/internal/server/server.go#L439)

### Missing

- no meal logging, macro ranges, or nutrition runtime yet:
  [roadmap.md](/Users/zizo/Personal-Projects/ASHTON/apollo/docs/roadmap.md#L44)
- no badges, achievement system, avatars, social profile, DM, or public profile
  surface
- no member-facing visits / tap history API; current visit readback is CLI-only
- no role-aware staff or admin UI model
- no public leaderboards / standings / rivalry / invite / notification flows

### Incompatibilities

- current competition routes are owner-scoped, not role-scoped:
  [README.md](/Users/zizo/Personal-Projects/ASHTON/apollo/README.md#L114)
- competition storage and queries are keyed around `owner_user_id`, which does
  not match the envisioned supervisor / manager / owner operational model:
  [competition.sql](/Users/zizo/Personal-Projects/ASHTON/apollo/db/queries/competition.sql)
- `apollo` explicitly keeps visits, workouts, recommendations, lobby state,
  planner, and competition as separate domains:
  [roadmap.md](/Users/zizo/Personal-Projects/ASHTON/apollo/docs/roadmap.md#L57)
- the current shell is still intentionally thin and local/runtime-first, not a
  broad PWA product:
  [README.md](/Users/zizo/Personal-Projects/ASHTON/apollo/README.md#L188)

### Audit Call

`apollo` is the main backend truth owner for the member product, but it is not
yet shaped for multi-role ops control.

The lowest-risk near-term path is to widen the member shell and add missing
member APIs before mutating competition semantics or forcing ops tooling into
the same surface.

## ATHENA

### Real Now

- canonical occupancy read path:
  [server.go](/Users/zizo/Personal-Projects/ASHTON/athena/internal/server/server.go#L84)
- facility catalog list/detail reads:
  [server.go](/Users/zizo/Personal-Projects/ASHTON/athena/internal/server/server.go#L181)
- bounded privacy-safe history read:
  [server.go](/Users/zizo/Personal-Projects/ASHTON/athena/internal/server/server.go#L106)
- edge tap ingress is real:
  [README.md](/Users/zizo/Personal-Projects/ASHTON/athena/README.md#L143)

### Partial Or Hidden

- facility truth is real, but config-gated and internal-only:
  [facility-truth.md](/Users/zizo/Personal-Projects/ASHTON/athena/docs/runbooks/facility-truth.md)
- history is real, but narrow, privacy-safe, and support-oriented:
  [edge-ingress.md](/Users/zizo/Personal-Projects/ASHTON/athena/docs/runbooks/edge-ingress.md)
- occupancy truth is mode-dependent: adapter-backed by default, edge-projection
  backed during live serve mode

### Missing

- no derived scheduling answers like `is_open_now`, next opening, or booking
  availability:
  [README.md](/Users/zizo/Personal-Projects/ASHTON/athena/README.md#L247)
- no booking engine
- no identity search / staff audit / override backend
- no public product-ready facility dashboard API that joins live occupancy with
  facility metadata
- no analytics / prediction runtime suitable for product UX

### Incompatibilities

- `athena` explicitly does not own member intent, sport capability, reservation
  policy, or broad admin product logic:
  [README.md](/Users/zizo/Personal-Projects/ASHTON/athena/README.md#L112)
- live public deployment proof is still intentionally tiny; do not assume all
  read routes are broadly deployed:
  [README.md](/Users/zizo/Personal-Projects/ASHTON/athena/README.md#L252)
- facility and history surfaces disappear entirely without explicit config

### Audit Call

`athena` can back truth-first facility and occupancy reads, but it should not
become the web app’s scheduling, booking, or admin workflow engine.

The right path is a composition layer above raw ATHENA truth, not forcing
derived product semantics into ATHENA.

## HERMES

### Real Now

- `hermes ask occupancy --facility <id>`
- `hermes ask reconciliation --facility <id> --window <duration> --bin <duration>`
- machine-readable JSON output and structured stderr observability:
  [root.go](/Users/zizo/Personal-Projects/ASHTON/hermes/internal/command/root.go)

### Partial Or Hidden

- reconciliation is real locally/runtime, but not deployment-proven in the same
  way as the older occupancy runner slice

### Missing

- no HTTP service
- no browser surface
- no role/session/auth model for staff
- no write or approval flows
- no identity-level or roster-level operator answers

### Incompatibilities

- current runtime is CLI-only, not web-ready
- repo boundary is explicitly read-only
- current surface is ownerless and staff-facing, not user/session-scoped:
  [README.md](/Users/zizo/Personal-Projects/ASHTON/hermes/README.md#L150)

### Audit Call

`hermes` is a promising future ops read seam, but not a current staff web API.
If the ops UI needs it soon, it would need an intentional HTTP/session layer,
not a thin wrapper around CLI stdout.

## Gateway

### Real Now

- `GET /health`
- `POST /mcp/v1/tools/list`
- `POST /mcp/v1/tools/call`
- two routed ATHENA reads only:
  [README.md](/Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/README.md#L109)

### Partial Or Hidden

- caller identity exists, but only in narrow internal header/API-key form
- audit exists, but only as sanitized routed-call persistence

### Missing

- no APOLLO routes
- no HERMES routes
- no write approvals
- no broad role/session model
- no stable product error envelope or broad product contract

### Incompatibilities

- current line is a narrow control plane, not a general app backend
- product should not assume this is the universal web API yet
- current registry is intentionally manifest-small

### Audit Call

The gateway is real and useful, but only as a narrow routed read layer today.
It is too small to be the main product backend contract without deliberate
widening.

## Ashton-Proto

### Real Now

- common health proto
- ATHENA occupancy/presence proto
- identified arrival/departure event schemas and helpers
- two ATHENA occupancy manifests:
  [README.md](/Users/zizo/Personal-Projects/ASHTON/ashton-proto/README.md)

### Missing

- no shared auth contract
- no shared role model
- no shared pagination/filtering contract
- no shared product error envelope
- no shared booking / scheduling / approval contracts
- no shared member-shell or ops-shell resource model

### Incompatibilities

- `zone_id` is optional in presence events but required for zone occupancy tool
  inputs
- generated Python exists but is not a promised release surface
- this repo should not be turned into a speculative schema dump

### Audit Call

`ashton-proto` is sufficient for current occupancy/event truth and gateway
manifests, but it does not yet provide a broad frontend contract layer.

## Vision Fit Matrix

| Product area | Status | Notes |
| --- | --- | --- |
| Member login | `Ready` | APOLLO auth/session is real, but delivery UX is still dev-first |
| Member home shell | `Partial` | Thin APOLLO shell exists, but not the broader envisioned nav/product shell |
| Tap in / streak | `Partial` | ATHENA edge tap and APOLLO visit ingest exist; streak logic and member-facing presence UX do not |
| Workout tracker | `Ready-ish` | Real backend and real thin UI in APOLLO |
| Workout planner | `Partial` | Backend substrate is real; browser product surface is missing |
| Meals / macros | `Missing` | Explicitly planned later in APOLLO `v0.16.0` after the coaching-first `v0.15.0` split |
| Profile identity / badges / avatar | `Missing` | No backend runtime yet beyond bounded profile/coaching inputs |
| Competition tab | `Partial` | Real internal backend substrate, no broad member-facing shell |
| Public leaderboards / standings / rivalry | `Missing` | Current competition history is internal and self-scoped, not public/social |
| Facility hours | `Partial` | ATHENA facility truth is real but internal/config-gated |
| Facility schedule / booking | `Missing` | No derived schedule or booking engine exists |
| Calendar conflict management | `Missing` | Requires a new scheduling layer above ATHENA raw truth |
| Supervisor dashboard | `Missing` | No staff web API or role model |
| Manager dashboard | `Missing` | No manager/owner authz model or broader ops runtime |
| Owner-only member stats / plan visibility | `Missing` | Current stats are self-scoped or owner-scoped by competition object ownership, not org role |
| Gateway-backed web integration | `Partial` | Real for two ATHENA reads only |
| Shared frontend contract layer | `Missing` | Proto/manifest layer is intentionally still narrow |

## What Should Be Built Without Mutating The Backend Much

### Safe To Do Mostly By Composing Existing Truth

1. widen the member shell in front of existing APOLLO APIs
2. expose planner UI over current planner APIs
3. expose competition self-views over current APOLLO session/history/member-stat
   APIs
4. show facility hours and facility metadata from ATHENA where config exists
5. show occupancy and basic facility state as read-only cards or dashboards

### Likely Needs Bounded Backend Mutation

1. member-facing visits / tap history / streak model in `apollo`
2. explicit claimed-tag or e-tap linking flow between physical presence and
   member product identity
3. role and authorization model for `member`, `supervisor`, `manager`, `owner`
4. staff-facing HTTP surface for ops reads and later write approvals
5. schedule derivation layer above ATHENA raw hours / closures
6. booking / rental workflow
7. public or multi-user competition reads if those should go beyond
   owner-scoped/internal models

## Lowest-Risk Mutation Ladder

1. Keep member work centered in `apollo`.
   Reason: the richest product truth already lives there.

2. Add a bounded visits API and explicit tap-link/streak model in `apollo`.
   Reason: this closes the gap between physical presence and member UX without
   polluting ATHENA.

3. Add a real role/authz layer before building ops UI.
   Reason: the envisioned supervisor/manager/owner model does not exist today.

4. Keep raw facility truth in `athena`, but move derived schedule and booking
   logic into a separate composition layer.
   Reason: ATHENA should stay physical-truth-first.

5. Keep `hermes` read-only unless and until a real staff web/runtime line is
   intentionally opened.
   Reason: the current CLI is trustworthy because it is narrow.

6. Widen the gateway only when there is a real product reason.
   Reason: today it is too narrow to serve as a universal product backend.

7. Delay social, badge, public rivalry, and LLM-heavy coaching until the
   deterministic product substrate is stable.

## Key Decisions This Audit Supports

- The frontend should become its own repo when implementation starts.
- The first meaningful frontend target should still be the member shell.
- The ops UI should be designed now, but should not assume current backend
  support for role-based staff workflows.
- Scheduling and booking are real product opportunities, but they need a new
  derived backend layer and should not be shoved into ATHENA.
- A lot of the vision is compatible with the platform.
  The main risk is not incompatibility.
  The main risk is widening too many boundaries at once.

## Next Consolidation Pass

The next useful step after this audit is a direct `vision vs truth` matrix that
sorts every major screen and workflow into:

- can build now
- can fake in UI over real backend truth
- needs bounded backend widening first
- should stay deferred on purpose
