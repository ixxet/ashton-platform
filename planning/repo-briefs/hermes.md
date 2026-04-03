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

- verified local truth only
- no live HERMES deployment claim exists yet
- no gateway, chat UX, LangGraph agent, write actions, or approvals exist yet

## Deferred On Purpose

- live deployment and GitOps proof
- richer cross-service staff questions
- identity-level roster answers
- write actions and human approval flows
- gateway or agent orchestration
