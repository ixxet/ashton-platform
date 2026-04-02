# Control Plane Runbook

Use this runbook to keep ASHTON planning and implementation chats disciplined.

## What This Thread Owns

- source-of-truth decisions
- repo ownership boundaries
- terminology arbitration
- blocker resolution across repos
- tracer planning and closure

## What Tracer Chats Own

- one vertical outcome
- repo-local implementation work
- tests and smoke checks for that slice
- any contract changes strictly required by that tracer

## Update Order After A Tracer

1. update repo-local `README.md` or `docs/roadmap.md` if the tracer changed reality
2. update repo-local ADRs or migrations
3. update [TRACER-MATRIX.md](/Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/sprints/TRACER-MATRIX.md)
4. record any real mistakes in repo-local `docs/growing-pains.md`
5. only then update long-form architecture docs if the change is broad and stable

If the tracer needed a hardening pass before closure, use
[tracer-closure-hardening-template.md](/Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/runbooks/tracer-closure-hardening-template.md)
to keep the pass narrow, audited, and explicit about deferrals.

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
