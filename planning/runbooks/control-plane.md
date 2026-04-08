# Control Plane Runbook

Use this runbook to keep ASHTON planning and implementation chats disciplined.

## What This Thread Owns

- source-of-truth decisions
- repo ownership boundaries
- terminology arbitration
- blocker resolution across repos
- tracer planning and closure

For the current Phase 2 launch policy, milestone naming, and pre-`1.0.0`
semantic versioning rules, see [phase-2-launch.md](phase-2-launch.md).

## What Tracer Chats Own

- one vertical outcome
- repo-local implementation work
- tests and smoke checks for that slice
- any contract changes strictly required by that tracer

## Update Order After A Tracer

1. update repo-local `README.md` or `docs/roadmap.md` if the tracer changed reality
2. update repo-local ADRs or migrations
3. update [TRACER-MATRIX.md](../sprints/TRACER-MATRIX.md)
4. record any real mistakes in repo-local `docs/growing-pains.md`
5. only then update long-form architecture docs if the change is broad and stable

If the tracer needed a hardening pass before closure, use
[tracer-closure-hardening-template.md](tracer-closure-hardening-template.md)
to keep the pass narrow, audited, and explicit about deferrals.

## Standalone Hardening Artifact Policy

- standalone hardening artifacts start at Tracer 4 and at Milestone 1 /
  Milestone 1.5
- Tracers 0-3 predate this documentation discipline and should not be given
  fabricated standalone hardening artifacts retroactively
- keep Tracers 0-3 represented through repo docs and the tracer matrix instead
- from Tracer 4 onward, the release sequence is:
  implementation closeout, hardening audit, standalone hardening artifact, then
  tag cut

See [planning/hardening/README.md](../hardening/README.md)
for the current artifact index.

## Required Verification Before Calling A Tracer Done

- tests pass for the changed slice
- any container image starts and answers a smoke check
- any GitOps manifest renders cleanly
- any deploy change is pinned to a real image tag or digest
- unresolved environment blockers are documented explicitly
- deferred features and unresolved bugs are recorded in a closeout table before
  tagging
- the exact verified commands live in a repo-local runbook or closeout doc, not
  only in chat history

## Growing Pains

### Deployment preflight can fail in the shell before the cluster is actually failing

Symptom:

- `kubectl config current-context` reports that no current context is set
- `flux check` falls back to `http://localhost:8080`
- a healthy cluster can look dead from the operator shell

Cause:

- deployment hardening commands were run without an explicit kubeconfig even
  though the Talos kubeconfig already existed on disk

Fix:

- export
  `/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig`
  for the session or prefix deployment commands with it
- verify operator context before treating any Flux or rollout failure as
  runtime truth

Rule:

- shell-local context failure is not the same thing as cluster failure
- prove kubeconfig and current context first, then trust deployment findings
