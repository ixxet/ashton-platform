# hermes

Read-only staff operations runtime over upstream ASHTON service truth.

HERMES is now real, but intentionally narrow. Its current executable slices are
one thin occupancy question and one richer reconciliation question:

```bash
hermes ask occupancy --facility <id>
hermes ask reconciliation --facility <id> --window <duration> --bin <duration>
```

Those commands read ATHENA's public occupancy surface plus one bounded
privacy-safe stable-history surface and return structured, source-backed truth.
HERMES does not own occupancy data, does not write back to ATHENA or APOLLO,
and does not introduce a private staff-side truth model.

## Current Runtime Truth

- `hermes ask occupancy --facility <id>` is real
- `hermes ask reconciliation --facility <id> --window <duration> --bin <duration>` is real
- `--format json|text` is supported
- `--athena-base-url` and `--timeout` can override runtime config explicitly
- HERMES reads ATHENA's public
  `GET /api/v1/presence/count?facility=<id>` surface and one bounded history
  surface:
  `GET /api/v1/presence/history?facility=<id>&since=<rfc3339>&until=<rfc3339>`
- HERMES returns structured output with:
  - source-backed current occupancy
  - occupancy report fields over the requested history window
  - heat-map-style buckets over the requested history window
  - one bounded `inspect_next` pointer for operator follow-up
- missing facility input, invalid base URLs or timeout overrides, upstream
  non-200s, malformed JSON, invalid timestamps, invalid history windows, and
  unavailable upstream connections all fail clearly

## Ownership Boundary

- `ATHENA` owns physical truth and occupancy counts
- `ATHENA` may expose bounded privacy-safe history reads to support HERMES
- `HERMES` owns the staff-facing read wrapper only
- `HERMES` must not read private databases directly
- `HERMES` must not mutate upstream systems in this slice
- `HERMES` must not fabricate answers when upstream truth is unavailable

## Current Repo Shape

```text
hermes/
├── cmd/hermes/                # CLI entrypoint
├── internal/athena/           # strict ATHENA occupancy + history client
├── internal/command/          # hermes ask occupancy/reconciliation and version commands
├── internal/config/           # env and CLI override validation
├── internal/ops/              # read-only occupancy + reconciliation services
└── docs/                      # roadmap, runbook, growing pains
```

## Verified Truth

- verified local/runtime truth for occupancy and reconciliation
- verified deployed truth for occupancy only
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
- Tracer 17 is now real as one richer reconciliation answer with occupancy
  reports and heat-map-style buckets over stable history
- deployment proof is now real, but the deployed shape stays a bounded
  internal runner and not a broader assistant service

## Deferred On Purpose

- broader cross-service staff questions beyond occupancy + reconciliation
- identity-level roster answers
- write actions and human approval flows
- gateway or agent orchestration

## Next Ladder

| Line | Focus | Hard stop |
| --- | --- | --- |
| `v0.1.1` / `Tracer 14` | low-noise structured observability for the existing occupancy slice | do not widen into richer questions or writes |
| `v0.1.2` only if runtime changes, otherwise deploy-only / `Milestone 1.7` | bounded live deployment proof for the same occupancy slice in a bounded internal runner | do not imply write authority, broad assistant maturity, or public ingress |
| `v0.2.0` / `Tracer 17` | one richer read-only reconciliation question over stable public upstream truth | do not invent identity-level answers, add overrides, or split into multiple operator sub-products |
