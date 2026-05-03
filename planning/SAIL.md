# SAIL.md

Worker execution contract for the ASHTON Core One Part Two sprint.

This file is intentionally platform-specific. It is not a Codex skill, not a
replacement for global `AGENTS.md` behavior, and not a runtime or deployment
claim. It exists so worker chats can start cleanly from the same packet
discipline.

## Read First

Every Core One Part Two worker chat starts by reading:

1. [`CORE-ONE-PART-TWO.md`](CORE-ONE-PART-TWO.md)
2. [`../../apollo/docs/launch-expansion-audit.md`](../../apollo/docs/launch-expansion-audit.md)
3. [`../../apollo/docs/roadmap.md`](../../apollo/docs/roadmap.md)
4. [`IMPLEMENTATION-BOARD.md`](IMPLEMENTATION-BOARD.md)
5. [`milestones/milestone-3.0-evidence/README.md`](milestones/milestone-3.0-evidence/README.md)
6. [`audits/2026-04-29-competition-system-audit.md`](audits/2026-04-29-competition-system-audit.md)

If a packet names additional docs, read those after this list.

## Role

Worker chats execute one packet. They do not reinterpret the roadmap.

Workers own:

- Gate 0 audit
- implementation within packet scope
- verification
- scoped docs updates after runtime proof passes
- commits and pushes when the packet asks for them
- closeout truth

Workers do not own:

- changing packet order
- widening scope
- reopening deferred features
- replacing APOLLO source truth with frontend logic
- claiming deployment truth without deployment proof

## Gate 0

Before editing files, the worker must:

- check `git status -sb` in every repo named by the packet
- check `git log --oneline -10` in repos it may modify
- read active files before changing them
- identify unrelated dirty files and leave them untouched
- stop if the packet overlaps unresolved work in the same files
- confirm whether Hestia, Themis, Prometheus, gateway, ATHENA, or platform are
  truly in scope
- confirm whether the packet is implementation, hardening, docs-only, or
  deploy/observability work

Dirty unrelated files are not permission to reset, revert, or overwrite user
work.

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

## Execution Rules

- APOLLO owns competition, rating, public projection, and game identity truth.
- CLI/API paths must route through shared services or command handlers, not an
  independent domain model.
- Frontends consume APOLLO contracts only.
- Runtime verification comes before docs closeout.
- Docs must distinguish repo/runtime truth from deployed truth.
- Deployment claims require explicit deployment proof.
- Add no dependencies without explicit approval.
- Keep commits scoped and stage only relevant files.

## Standard Worker Shape

Every worker packet uses exactly four prompts:

1. Intake / Gate 0 / No-Duplicate Audit
2. Backend / Runtime
3. Frontend / Ops Shell
4. Docs / Closeout / Push

If the packet does not touch frontend, deploy, or docs, the worker still records
that the area was checked and left unchanged.

## Verification Baseline

Use the packet-specific matrix first. The default APOLLO baseline is:

```sh
git diff --check
go test -count=1 ./internal/competition ./internal/rating ./internal/server
go test -race ./internal/...
go vet ./...
go build ./cmd/apollo
go test -count=1 ./...
```

Frontend repos are verified only if touched or if the packet explicitly requires
them:

```sh
npm run check
npm test
npm run build
```

Platform docs baseline:

```sh
git diff --check
```

## Closeout Report

A worker closeout must include:

- verdict: closed or not closed
- findings fixed
- files changed
- commits
- verification commands and results
- push result
- final git status for touched repos
- repo/runtime truth
- deployed truth
- deferred truth
- residual risk

Do not call a packet closed if verification failed, docs overclaim shipped
truth, or the final repo state is unclear.

## Current First Packet

Packet 1 is `Scale Gate Numeric Ceilings`.

Worker handoff summary:

- define and prove numeric ceilings for APOLLO rating recompute, public
  readiness/leaderboard projections, game identity projections, and CLI/API
  smoke proof
- preserve CLI/API-first demo readiness
- preserve APOLLO source-truth ownership
- add no new product surface
- do not switch OpenSkill onto the active read path
- do not touch public tournaments, messaging/chat, broad social graph, or
  booking/commercial/proposal workflows

After Packet 1 closes, return to the planning chat for Packet 2:
`CLI Demo Spine`.
