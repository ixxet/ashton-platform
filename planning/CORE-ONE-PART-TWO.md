# Core One Part Two Planning Chat Charter

This document codifies the operating map for the current ASHTON strategic
planning chat. It is a planning authority artifact, not a Codex skill, runtime
contract, deploy claim, or replacement for APOLLO's launch-expansion audit.

Canonical launch-expansion truth remains in
[`../../apollo/docs/launch-expansion-audit.md`](../../apollo/docs/launch-expansion-audit.md).

## Ruling

Continue the competition path. Do not pivot into frontend-first work.

The competition trust spine is complete through `3B.20.1`, `3B-LC` is closed,
and Milestone 3.0 proved bounded APOLLO/ATHENA deploy smoke, APOLLO metrics
export, Prometheus scrape proof, and cross-repo compatibility matrix truth.
Gate 8 Scale numeric ceilings are now locally/runtime-proved; full production
load validation remains deferred.

Primary direction:

> APOLLO owns truth. CLI/API expose truth. Frontends consume truth. No UI-owned
> formulas. No public-stakes widening until scale and policy are explicit.

## Role Boundaries

This planning chat owns:

- strategic planning
- packet decomposition
- sequencing and risk gating
- runtime contract reasoning
- CLI/API-first demo readiness
- mapping work back to current launch-expansion truth
- generating worker packets

This planning chat does not own:

- implementation
- runtime file edits
- test execution
- commits or pushes
- deployment changes

Implementation belongs in worker chats after this chat produces a bounded
packet.

## Current Truth

- `3B.11` through `3B.20.1` are closed.
- `3B-LC` is closed as a verification-only trust-spine closeout.
- Milestone 3.0 is closed for bounded APOLLO/ATHENA deploy smoke, APOLLO
  metrics export, Prometheus scrape proof, and compatibility matrix truth.
- APOLLO remains the source of competition, rating, public projection, and game
  identity truth.
- Hestia consumes public/member-safe APOLLO contracts only.
- Themis consumes internal APOLLO contracts only.
- OpenSkill remains comparison-only; the active read path remains the
  legacy-engine policy-wrapped APOLLO projection.
- Gate 8 Scale numeric ceilings are declared and locally/runtime-proved for
  APOLLO rating recompute, public readiness, public leaderboard projection,
  game identity projection, and CLI/API smoke paths. This is not full
  production load validation, and deployed truth is unchanged.
- CLI Demo Spine is closed locally in APOLLO repo/runtime: APOLLO CLI/API can
  prove the competition spine through service-backed public/member projections,
  command dry-run/apply, result lifecycle, ARES preview generation, analytics-
  backed projection reads, session/tournament reads, and manager/internal
  safety reads without frontend dependency. Deployed truth is unchanged.
- Rating Policy Wrapper is closed locally in APOLLO repo/runtime: APOLLO active
  legacy-engine rating projection now records `apollo_rating_policy_wrapper_v1`,
  calibration/provisional/ranked status, fifth-match ranked transition,
  inactivity sigma-inflation metadata, and climbing-cap metadata. OpenSkill
  remains comparison-only, public tournaments remain blocked, and deployed truth
  is unchanged.
- Rating Policy Simulation / Golden Expansion is closed locally in APOLLO
  repo/runtime: APOLLO now has deterministic rating policy fixtures, local CLI
  JSON output, legacy baseline deltas, OpenSkill sidecar deltas,
  accepted/rejected scenario classification, cutover blockers, and policy risk
  output. OpenSkill remains comparison-only, public tournaments remain blocked,
  and deployed truth is unchanged.
- Game Identity Policy Tuning Loop is closed locally in APOLLO repo/runtime:
  APOLLO now has deterministic CP, badge, rivalry, and squad fixtures, local
  CLI JSON/text output, accepted/rejected policy findings, policy risk output,
  blockers, and optional DB-backed local projection-row analysis. Active game
  identity policy versions and public/member output behavior remain unchanged;
  no real local DB population evidence was claimed in the worker environment,
  and deployed truth is unchanged.

Primary source documents:

- APOLLO launch expansion audit:
  [`../../apollo/docs/launch-expansion-audit.md`](../../apollo/docs/launch-expansion-audit.md)
- APOLLO roadmap:
  [`../../apollo/docs/roadmap.md`](../../apollo/docs/roadmap.md)
- Implementation board:
  [`IMPLEMENTATION-BOARD.md`](IMPLEMENTATION-BOARD.md)
- Frontend route/API contract matrix:
  [`FRONTEND-ROUTE-API-CONTRACT-MATRIX.md`](FRONTEND-ROUTE-API-CONTRACT-MATRIX.md)
- Milestone 3.0 evidence:
  [`milestones/milestone-3.0-evidence/README.md`](milestones/milestone-3.0-evidence/README.md)
- Competition system audit:
  [`audits/2026-04-29-competition-system-audit.md`](audits/2026-04-29-competition-system-audit.md)

## Core Packet Stack

| Order | Packet | Purpose | Status in this chat |
| --- | --- | --- | --- |
| 1 | Scale Gate Numeric Ceilings | Declare and prove row-count, latency, recompute, projection, and CLI/API smoke ceilings | Closed locally/runtime in worker packet; not production load validation |
| 2 | CLI Demo Spine | Make the competition spine agent-operable from CLI/API end to end | Closed locally/runtime in worker packet; not deployed truth |
| 3 | Rating Policy Wrapper | Add calibration, decay, caps, provisional status, and policy versions without OpenSkill cutover | Closed locally/runtime in worker packet; not deployed truth |
| 4 | Rating Policy Simulation / Golden Expansion | Stress policy behavior against fixtures and historical/synthetic scenarios | Closed locally/runtime in worker packet; not deployed truth |
| 5 | Frontend Route/API Contract Matrix | Freeze Hestia/Themis route, API, auth, state, and stub status | Closed as docs truth in worker packet; runtime/deployed truth unchanged |
| 6 | Game Identity Policy Tuning Loop | Tune CP/badge/rivalry/squad rules against real data without new social surface | Closed locally/runtime in worker packet; active behavior and deployed truth unchanged |
| 7 | ATHENA Real Ingress Bridge | Strengthen physical truth for persistent teams, XP, and reliability | Next worker-executable packet |
| 8 | Live Destructive Probe Plan | Plan controlled live mutation and SIGTERM proof | Later deploy/ops safety packet |
| 9 | Public Tournament Readiness | Only after scale, policy, safety, and probe gates | Blocked |
| 10 | OpenSkill Read-Path Cutover | Only after wrapper, simulation, rollback, and scale proof | Blocked |

This stack is the Core One Part Two map only. It is not the whole
launch-expansion roadmap.

## Packet Production Rules

Produce one packet at a time.

Every full packet must use this 14-section format:

1. Ruling
2. Goal
3. Scope
4. Current Truth
5. Why This Packet Now
6. Hard Stops
7. Gate 0 Audit
8. Agent Orchestration
9. Worker Packet
10. Verification Matrix
11. Commit Ladder
12. Truth Boundaries
13. Residual Risk
14. Next Packet

Every worker packet must contain exactly four prompts:

1. Intake / Gate 0 / No-Duplicate Audit
2. Backend / Runtime
3. Frontend / Ops Shell
4. Docs / Closeout / Push

Planning packets must preserve:

- CLI/API-first demo readiness
- APOLLO source-truth ownership
- shared service or command-handler routing for CLI/API paths
- frontend consumption of real APOLLO contracts only
- explicit runtime/deploy/deferred truth boundaries

## Hard Non-Touches

Do not open these by implication:

- OpenSkill active read path
- public tournaments
- messaging or chat
- broad public social graph
- public profiles or scouting
- public/member safety report, block, or reliability details
- booking, commercial, quote, payment, or proposal workflows
- public self-booking or public self-edit/rebook
- project-wide SemVer governance
- Hestia staff controls
- Themis public surface
- UI-owned formulas for ratings, analytics, matchmaking, tournaments, safety,
  public projections, or game identity

## Worker Packet Standard

Worker packets must be decision-complete enough for a worker chat to execute
without inventing scope.

Minimum worker packet requirements:

- name exact repos in scope
- state whether APOLLO, Hestia, Themis, Prometheus, or platform are touched
- require Gate 0 repo-state checks before edits
- require current truth reads before implementation
- distinguish repo/runtime truth from deployed truth
- include verification commands by repo
- include a commit ladder with only relevant files
- require docs updates only after runtime verification passes
- require closeout reporting for files changed, commits, verification, push
  result, final git status, repo/runtime truth, deployed truth, deferred truth,
  and residual risk

If Gate 0 finds dirty unrelated work, the worker must not revert it. The worker
may proceed only when the packet can be completed by touching scoped files
without overwriting existing user or generated changes.

## Next Ask

Ask this planning chat for one packet at a time.

The current next worker-executable ask is:

```md
Produce the worker-ready execution handoff for:

ATHENA Real Ingress Bridge

Preserve APOLLO/ATHENA source-truth ownership, docs/runtime/deployed truth
separation, no fake ATHENA truth claims, no frontend-owned formulas, no new
public product surface, no OpenSkill read-path switch, no public tournaments,
no messaging/chat, no broad social graph, no public/member safety detail UI,
and no booking/commercial/proposal workflow.
```
