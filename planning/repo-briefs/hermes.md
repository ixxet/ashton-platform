# hermes

Read-only staff operations runtime over upstream ASHTON service truth.

HERMES is now real, but intentionally narrow. Its first executable slice is a
single CLI question:

```bash
hermes ask occupancy --facility <id>
```

That command reads ATHENA's public occupancy surface and returns structured,
source-backed truth. HERMES does not own occupancy data, does not write back to
ATHENA or APOLLO, and does not introduce a private staff-side truth model.

## Current Runtime Truth

- `hermes ask occupancy --facility <id>` is real
- `--format json|text` is supported
- `--athena-base-url` and `--timeout` can override runtime config explicitly
- HERMES reads ATHENA's public
  `GET /api/v1/presence/count?facility=<id>` surface only
- HERMES returns structured output with:
  - `facility_id`
  - `current_count`
  - `observed_at`
  - `source_service`
  - optional `notes`
- missing facility input, invalid base URLs or timeout overrides, upstream
  non-200s, malformed JSON, invalid timestamps, and unavailable upstream
  connections all fail clearly

## Ownership Boundary

- `ATHENA` owns physical truth and occupancy counts
- `HERMES` owns the staff-facing read wrapper only
- `HERMES` must not read private databases directly
- `HERMES` must not mutate upstream systems in this slice
- `HERMES` must not fabricate answers when upstream truth is unavailable

## Current Repo Shape

```text
hermes/
├── cmd/hermes/                # CLI entrypoint
├── internal/athena/           # strict ATHENA upstream client
├── internal/command/          # hermes ask occupancy and version commands
├── internal/config/           # env and CLI override validation
├── internal/ops/              # read-only occupancy service
└── docs/                      # roadmap, runbook, growing pains
```

## Verified Truth

- verified local and deployed truth
- live HERMES deployment truth exists as a bounded internal runner slice in `agents`
- no gateway, chat UX, LangGraph agent, write actions, or approvals exist yet

## Hardening Notes

- missing facility input, invalid timeout overrides, malformed upstream data,
  and unavailable upstream connections are expected destructive checks, not
  happy-path regressions
- the happy path is a valid occupancy read against a healthy ATHENA runtime
- Tracer 14 observability is now real for the current occupancy slice:
  low-noise structured request / result / outcome logs are part of current
  runtime truth
- deployment proof is now real, but the deployed shape stays a bounded
  internal runner and not a broader assistant service
- broader operator reporting or reconciliation maturity is still deferred to
  later work; this brief does not imply Tracer 17 is complete

## Deferred On Purpose

- richer cross-service staff questions
- identity-level roster answers
- write actions and human approval flows
- gateway or agent orchestration

## Next Ladder

| Line | Focus | Hard stop |
| --- | --- | --- |
| `v0.1.1` / `Tracer 14` | low-noise structured observability for the existing occupancy slice | do not widen into richer questions or writes |
| `v0.1.2` only if runtime changes, otherwise deploy-only / `Milestone 1.7` | bounded live deployment proof for the same occupancy slice in a bounded internal runner | do not imply write authority, broad assistant maturity, or public ingress |
| `v0.2.0` / `Tracer 17` | one richer read-only staff question over stable public upstream truth | do not invent identity-level answers or add overrides |
