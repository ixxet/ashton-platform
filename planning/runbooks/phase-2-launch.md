# Phase 2 Launch

## Purpose

Lock one control-plane note before the repo-by-repo Phase 2 audits begin.

This document does four things:

1. states the organizer contract
2. records the ATHENA edge-deployment drift ruling
3. formalizes milestone naming for the current ladder
4. turns `semver-lite` into an explicit pre-`1.0.0` semantic versioning policy

## Canonical Organizer Contract

- `v2` is the standing master/worker organizer contract
- `v1` remains the source map and directory priority reference
- `v3` remains the exact bounded worker brief for `Tracer 14`
- file truth beats chat memory
- worker chats own one bounded line only
- the master chat owns sequencing, arbitration, and closeout truth

## Bootstrap Read Order Vs Conflict Arbitration Order

Use two different orders for two different jobs.

Bootstrap read order for a fresh master chat:

1. `planning/IMPLEMENTATION-BOARD.md`
2. `planning/STARSHOT-VISION.md`
3. `planning/repo-briefs/`
4. `planning/sprints/TRACER-MATRIX.md`
5. repo-local `README.md` and `docs/roadmap.md`
6. `Prometheus` only when deployment truth is in scope

Conflict arbitration order once you are already working a repo or line:

1. `planning/repo-briefs/`
2. repo-local `README.md` and `docs/roadmap.md`
3. repo-local ADRs
4. repo-local migrations and schemas
5. `planning/sprints/TRACER-MATRIX.md`
6. `planning/audits/`
7. `planning/STARSHOT-VISION.md` for strategic horizon only

## ATHENA Edge Drift Consolidation

Primary label:

- ATHENA adapter + live edge deployment workstream closeout

Authoritative ruling:

- this workstream is real
- it layered on top of shipped `athena v0.4.1`
- it did not consume `Tracer 14`
- it did not consume `Tracer 15`
- it did not consume `Tracer 16`
- it created real deployment truth and planning preconditions for `Tracer 16`

What is already real:

- the live TouchNet browser bridge is real
- the current Cloudflare-backed browser-reachable ATHENA ingress path is real
- the current per-node tokens and workstation userscripts are real
- modern Windows and legacy Chromebook Tampermonkey variants are real
- `ms-gym-01` and `ms-gym-02` are proven live node-level posting paths
- direction inference from the TouchNet row text is real
- accepted `pass` rows update live occupancy and identified publish

What is still not real:

- durable append-only edge history
- restart-safe durable session inference
- public operator or report surfaces over that history
- rivalry, teammate, or standings truth

Phase 2 rule for `Tracer 16`:

- keep the same tunnel, token, and userscript contract
- add persistence behind the same `POST /api/v1/edge/tap` handler
- start with fail-open shadow-write posture
- do not let the first durable rollout break live tap collection

ATHENA-only signal boundary:

- co-presence, dwell, regularity, and routine signals may become internal analytics after durable history exists
- rivalry, teammate, faced-most, badge, and public/social product signals stay later in APOLLO

## Milestone Naming Rule

Historical names stay as history:

- `Milestone 1`
- `Milestone 1.5`
- `Milestone 1.6`

Current interpretation:

- whole-number milestone = a broader platform proof plateau
- decimal milestone = a bounded deployment or hardening deepening pass inside the same milestone family
- `Milestone 1.5` and `Milestone 1.6` may keep their historical aliases `Deployment Workstream A` and `Deployment Workstream B`
- `Milestone 1.7` is the next bounded deployment deepening pass in the Milestone 1 family
- do not renumber historical artifacts retroactively

Create a new decimal milestone only when all of the following are true:

- the pass deepens already-earned truth
- the pass stays narrower than a new tracer or new product plateau
- the pass primarily proves deployment, hardening, or closure quality
- the pass does not invent a broader feature claim

## Pre-1.0 Semantic Versioning Policy

ASHTON now treats `semver-lite` as historical shorthand only.

Effective policy: ASHTON uses formal pre-`1.0.0` semantic versioning.

Release rules:

- `MAJOR` stays `0` until a repo has earned `1.0.0`
- `MINOR` = a new bounded runtime capability or an intentional pre-`1.0.0` breaking surface change
- `PATCH` = hardening, docs sync, deployment closeout, observability, bug fixes, or other bounded follow-up on an existing capability line
- control-plane and deployment repos may use patch bumps for ledger or closeout truth even when service repos use minor bumps for runtime growth

Breaking-change rule before `1.0.0`:

- a breaking change still requires at least a `MINOR` bump
- the release note must call out the broken surface explicitly
- the affected surface must name the boundary that changed: CLI, HTTP, event contract, manifest, schema, or migration behavior

## Graduation To 1.0.0

Do not move a repo to `1.0.0` because it feels important.

A repo graduates only when it has all of the following:

- at least one stable public surface that is expected to stay compatible
- explicit compatibility promises for that surface
- reproducible release artifacts
- destructive and smoke proof for the surface
- a current runbook and release note path
- a migration or rollback story when storage is involved
- current docs that agree with shipped tags and deployed truth

## Phase 2 Audit Protocol

Audit one repo at a time.

Use three agents per repo:

1. code/mechanics reviewer
2. docs/source-of-truth reviewer
3. consolidation reviewer

Current audit order:

1. `ashton-platform`
2. `athena`
3. `ashton-proto`
4. `hermes`
5. `ashton-mcp-gateway`
6. `apollo`
7. `Prometheus`

The order is deliberate:

- control-plane truth first
- ATHENA second because the main live-edge drift and `Tracer 16` planning center there
- gateway and APOLLO later because their next lines depend on the earlier arbitration

## Audit Closeout Snapshot

Phase 2 audit status after the repo-by-repo pass:

| Repo | Audit verdict | Main correction already made | Main carry-forward gap |
| --- | --- | --- | --- |
| `ashton-platform` | control-plane truth is now legible enough to steer Phase 2 | milestone naming, semver policy, and ATHENA drift ruling were formalized | several truths are still markdown-mediated rather than machine-enforced |
| `athena` | strongest service boundary today | `Tracer 16` now clearly owns durable history on `v0.5.0` and the bounded `v0.4.1` edge deployment workstream is memorialized as non-tracer truth | durable history, restart-safe rebuild, and fail-open persistence rollout are still deferred |
| `ashton-proto` | real shared-contract slice with clearer release intent | release gate and version consequences are now explicit in repo docs | remote plugin pinning and stronger generated-truth checks remain deferred |
| `hermes` | credible thin staff slice with better local rigor | invalid format now fails before config/upstream work and the repo ladder now matches the control plane | `Tracer 14` observability runtime and `Milestone 1.7` deployment assets are still missing |
| `ashton-mcp-gateway` | narrow routed-read proof is now documented honestly | manifest support is now constrained to the one route the runtime actually serves and server lifecycle hardening is in place | `Tracer 15` identity, persisted audit, and second route are still missing |
| `apollo` | broadest runtime, still with the highest security/product debt | ladder drift is fixed, logout is stricter, and CI now covers pull requests | sensitive read/write boundary issues and a few data-integrity gaps remain before the next widening |
| `Prometheus` | bounded deployment truth is real, but still selective | the ASHTON deployment surface is now treated as narrow live truth rather than generic cluster optimism | no HERMES or gateway deployment manifests exist yet, so `Milestone 1.7` is not deploy-ready there |

## Semver Transition Plan

The policy above is the rule. This is the operating transition:

1. Treat `semver-lite` as historical wording only. New docs, prompts, and release notes should say `formal pre-1.0.0 semantic versioning`.
2. Keep each repo on `0.y.z` until it earns a stable compatibility promise.
3. Require every next-line table to state whether the change is a `minor` capability line or a `patch` hardening / deploy-closeout line.
4. Before tagging, align four truths:
   - repo `README.md`
   - repo `docs/roadmap.md`
   - `ashton-platform/planning/IMPLEMENTATION-BOARD.md`
   - deployment docs if deployed truth changed
5. Every tagged release should call out:
   - changed surface
   - release type (`minor` or `patch`)
   - destructive / smoke proof
   - local, deployed, and deferred truth split
6. Do not graduate any repo to `1.0.0` until it has a stable public surface, an explicit compatibility promise, reproducible artifacts, and a rollback/migration story where storage is involved.
