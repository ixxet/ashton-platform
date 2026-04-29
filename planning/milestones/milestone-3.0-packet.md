# Milestone 3.0 Packet — Deploy Smoke Proof + Cross-Repo Hardening
Date: 2026-04-29
Status: packet authored, not yet executed

Post-closeout evidence note: the executed Milestone 3.0 evidence pack is consolidated into `planning/milestones/milestone-3.0-evidence/README.md`. Flow and harness IDs are preserved as sections/tables in that single file instead of one Markdown sidecar per ID.

## Executive Verdict

Milestone 3.0 proves that the currently shipped ASHTON competition trust spine can survive cluster-level deploy smoke and cross-repo compatibility probes without widening product behavior: APOLLO remains source truth, ATHENA remains ingress/source-observation truth, Themis remains internal ops, Hestia remains public/member-safe, the gateway remains read-only routed tooling, and Prometheus/GitOps truth is stated with evidence. This milestone does not ship new competition features, public surfaces, policy math, tournament formats, OpenSkill read paths, or demo behavior; it moves Gate 6 Telemetry and Gate 10 Cross-Repo Compatibility from Partial to Pass, declares Gate 8 Scale ceilings, and keeps every public-facing surface at its current allowlisted boundary.

## Context (versions, gates, residual risks)

| Repo | Current repo HEAD tag | Last shipped tag | Currently deployed version (if known) | Drift status (none / documented / hidden) |
| --- | --- | --- | --- | --- |
| APOLLO | `v0.19.1-110-g46d2425` | `v0.19.1` | Prometheus GitOps pins `ghcr.io/ixxet/apollo:sha-bf3119b@sha256:ed3f3681b65a889ee563e8e0917fa3caba17cbceddb26a89393882ee287a3748`; current 3B.11-3B.20 repo truth is not proved live | documented |
| ATHENA | `v0.8.2-2-ga19fe02` | `v0.8.2` | Prometheus GitOps pins `ghcr.io/ixxet/athena:v0.8.2@sha256:6524994c2c30bfd227b684b9144b95a798e3e46e600ac5da46df2a600dfd271d`; older deploy runbook still says `v0.7.0` | documented |
| Hestia | `66010ae` | no tag found | No live deploy manifest found in Prometheus apps; public/member-safe frontend truth remains repo-local until verified otherwise | documented |
| Themis | `0231e02` | no tag found | No live deploy manifest found in Prometheus apps; internal ops shell truth remains repo-local until verified otherwise | documented |
| ashton-mcp-gateway | `v0.2.1-1-gdf18570` | `v0.2.1` working line, `v0.2.0` README-released line | No live gateway deploy manifest found; gateway live deployment proof remains deferred | documented |
| ashton-platform | `v0.0.36-44-g8cdd046` | `v0.0.36` | Planning/docs repo, not a runtime deploy | none |
| ashton-proto | `v0.4.0-2-ge49e627` | `v0.4.0` | Consumed by APOLLO, ATHENA, and gateway; no independent deploy | documented |
| Prometheus | `v0.0.3-27-g02033bd` | `v0.0.3` | GitOps source contains APOLLO `sha-bf3119b`, ATHENA `v0.8.2`, ATHENA ServiceMonitor, no Hestia/Themis/gateway manifests found | none |

The 2026-04-29 competition system audit carries one primary residual risk into this packet: deploy smoke is still not proved at cluster level. Milestone 3.0 addresses that deploy proof, telemetry export proof, compatibility proof, and explicit ATHENA/APOLLO deploy drift truth. It does not address game identity policy hardening beyond current projections, full scale validation beyond declared ceilings, Themis dated fixture/test-selector cleanup, public tournament surfaces, public safety detail exposure, OpenSkill read-path switching, messaging/chat, or broad public social graph work.

## Hard Scope Locks

- No public tournament surfaces.
- No OpenSkill read-path switch.
- No policy wrapper expansion; calibration, decay, climbing caps, and related rating-policy behavior stay deferred.
- No cross-sport transfer.
- No ATHENA Postgres event-store or schema widening beyond currently shipped/deployed ATHENA truth.
- No Hestia/Themis dated fixture or test-selector debt cleanup; this is explicitly out of scope.
- No new product capability of any kind.
- No demo work.
- No fuzz testing unless Controller scopes a real corpus, real target, and real duration; no 30-second fuzz theater.
- No fake deploy claims.
- No branch-only closure claims.
- No messaging/chat.
- No broad public social graph, public profiles, public scouting, spectator feed, quests, XP, MVP, drafts, drills, or onboarding game loops.
- No public safety report, safety block, reliability event, safety review, manager note, or moderation detail exposure.
- No public ARES internals, OpenSkill mu/sigma/comparison rows, analytics internals, command internals, trusted-surface fields, source result IDs, projection watermarks, or raw user IDs.
- No booking/commercial/proposal workflow expansion, including public self-booking, public edit/rebook, payments, quotes, invoices, deposits, checkout, proposal workflow, or approval mutations.
- No new tournament formats.
- No recurrence, broad hours editor, schedule-governance expansion, court splitting, or recurring schedule line.
- No project-wide SemVer governance conversion.
- No APOLLO ownership drift: APOLLO owns competition source truth and derived projections.
- No UI-owned truth: Themis and Hestia may consume contracts only.
- No Themis public-truth role and no Hestia internal-ops role.
- No gateway write path, HITL approval path, rate limiting, APOLLO routing, or HERMES routing unless a separate gateway packet approves it.
- No silent deployment roll-forward; Controller must approve any deploy movement and Verifier must prove it.

## Classification Buckets

- `must_fix_now`: A proven trust-spine, privacy, deploy, compatibility, telemetry, authz, idempotency, panic, or source-truth regression that blocks Milestone 3.0 closeout.
- `must_prove_now`: A required smoke, harness, telemetry, compatibility, or deploy-truth claim that may be correct but lacks committed evidence.
- `can_patch_if_clean`: A bounded, non-product hardening patch that is approved by the Controller after Auditor evidence shows it is low-risk and directly supports this milestone.
- `defer`: Real work outside Milestone 3.0, including policy hardening, scale validation beyond ceilings, product expansion, fixture-debt cleanup, or future strategic lines.
- `already_false`: A claim that current repo/deploy truth already disproves; it must be corrected in the ledger, not converted into implementation work.

The Worker may only act on `must_fix_now` items and explicitly approved `can_patch_if_clean` items. `already_false` claims do not become work.

## Cross-Repo Smoke Matrix

| Flow ID | Repos involved | Probe description | Pass criteria | Failure mode | Required evidence artifact |
| --- | --- | --- | --- | --- | --- |
| F01 | ATHENA, APOLLO, ashton-proto, Prometheus | ATHENA `POST /api/v1/edge/tap` accepted `pass in` publishes `athena.identified_presence.arrived`; APOLLO `internal/consumer/identified_presence.go` parses via ashton-proto and records a visit through the visit arrival service. | ATHENA returns accepted/published, NATS logs show one arrival event, APOLLO logs one handled arrival, APOLLO visit row exists with source event ID. | APOLLO misses event, stores anonymous/private data incorrectly, or double-records visit. | `planning/milestones/milestone-3.0-evidence/F01-athena-apollo-arrival.md` |
| F02 | ATHENA, APOLLO, ashton-proto | Replay the same ATHENA accepted arrival event through NATS/APOLLO consumer. | APOLLO visit write is idempotent; second replay is ignored/conflict-safe with stable outcome and no duplicate visit. | Duplicate visit, panic, or hidden mutation on replay. | `planning/milestones/milestone-3.0-evidence/F02-athena-apollo-arrival-replay.md` |
| F03 | APOLLO, Themis, Hestia | Manager uses APOLLO routes `POST /api/v1/competition/sessions/{sessionID}/matches/{matchID}/result` then `/result/finalize`; services `RecordMatchResult`, `FinalizeMatchResult`, `PublicCompetitionReadiness`, and `ListPublicCompetitionLeaderboard` are probed. | Finalized canonical result updates public readiness/leaderboard projections while preserving staff-only source IDs internally. | Public projection stale, missing finalization event, or leak fields appear. | `planning/milestones/milestone-3.0-evidence/F03-result-finalize-public-projection.md` |
| F04 | APOLLO, Themis, Hestia | Record and finalize result, then call `/result/dispute` and `/result/correct` through APOLLO lifecycle services `DisputeMatchResult` and `CorrectMatchResult`; read member/public history afterward. | Canonical view supersedes prior result, lifecycle events are ordered, public/member projections show corrected safe outcome only. | Hidden result mutation, stale canonical view, or previous source result remains publicly visible. | `planning/milestones/milestone-3.0-evidence/F04-result-correction-canonical.md` |
| F05 | APOLLO | Finalize a result and inspect `internal/rating/legacy.go`, `internal/rating/openskill.go`, and APOLLO rating persistence. | Legacy rating remains active read path; OpenSkill comparison row is written for comparison only. | OpenSkill becomes active read path, comparison row missing, or rating policy version unrecorded. | `planning/milestones/milestone-3.0-evidence/F05-rating-dual-run.md` |
| F06 | APOLLO, Hestia | `GET /api/v1/public/competition/readiness` through APOLLO and Hestia `src/lib/server/public-competition.ts`. | Readiness reflects latest finalized safe aggregate and contains only public fields. | Raw user IDs, match IDs, result IDs, sample sizes, watermarks, safety facts, command internals, or staff details leak. | `planning/milestones/milestone-3.0-evidence/F06-public-readiness-redaction.md` |
| F07 | APOLLO, Hestia | `GET /api/v1/public/competition/leaderboards?sport_key=...&mode_key=...&stat_type=...&team_scope=...` via APOLLO `ListPublicCompetitionLeaderboard` and Hestia public competition page. | Rows are allowlisted/redacted and stable; no private IDs or internal rating/OpenSkill details. | Public leaderboard leaks raw identifiers, internal stats, or unbounded rows. | `planning/milestones/milestone-3.0-evidence/F07-public-leaderboards-redaction.md` |
| F08 | APOLLO, Hestia | `GET /api/v1/public/competition/game-identity` via APOLLO `PublicGameIdentity` and Hestia public page. | CP, badge, rivalry, and squad projections are public-safe and redacted; no source IDs or policy internals. | Game identity exposes raw users, canonical result IDs, sample sizes, OpenSkill/ARES/safety facts, or watermarks. | `planning/milestones/milestone-3.0-evidence/F08-public-game-identity-redaction.md` |
| F09 | APOLLO, Hestia | Authenticated member calls `GET /api/v1/competition/member-stats`, `/history`, and `/game-identity`; attempt to read another member through query/path/body manipulation. | Member reads are self-scoped to `principal.UserID`; other-member access is impossible or denied. | Member can read another member's competition facts or source IDs. | `planning/milestones/milestone-3.0-evidence/F09-member-self-scope.md` |
| F10 | APOLLO, Themis | `GET /api/v1/competition/commands/readiness` through APOLLO `CompetitionReadiness` and Themis ops. | Returns versioned supported commands/features and capability-aware disabled state. | Missing version/capability flags, UI invents readiness, or unsupported commands appear active. | `planning/milestones/milestone-3.0-evidence/F10-command-readiness.md` |
| F11 | APOLLO, Themis | `POST /api/v1/competition/commands` with `dry_run=true` through `ExecuteCommand`. | Returns plan, actor, required capability, expected versions, and `mutated=false`; no DB writes. | Dry-run mutates, omits plan/version, or requires trusted-surface when it should not. | `planning/milestones/milestone-3.0-evidence/F11-command-dry-run.md` |
| F12 | APOLLO, Themis | `POST /api/v1/competition/commands` apply on guarded queue/result/tournament paths without or with stale `expected_*_version`. | Apply rejects with structured outcome and actual/expected version details; no hidden mutation. | Stale mutation succeeds, error is unstructured, or version field is optional on guarded paths. | `planning/milestones/milestone-3.0-evidence/F12-command-expected-version.md` |
| F13 | APOLLO, Themis | Themis server-side client injects `X-Apollo-Trusted-Surface` and token for staff routes; member token hits staff route; supervisor hits safety route. | Manager trusted request passes; member is 403; supervisor is 403 on `competition_safety_review`. | Browser or wrong role reaches safety/structure mutation, or trusted-surface not required. | `planning/milestones/milestone-3.0-evidence/F13-themis-trusted-surface-roles.md` |
| F14 | Themis, APOLLO | Browser calls Themis proxy `src/routes/api/v1/[...path]/+server.ts` with trusted-surface headers set by client. | Themis strips `x-apollo-trusted-surface` and `x-apollo-trusted-surface-token`; APOLLO does not receive browser-supplied trust. | Browser can forge trusted-surface through proxy. | `planning/milestones/milestone-3.0-evidence/F14-themis-proxy-header-strip.md` |
| F15 | Hestia, APOLLO | Browser calls Hestia proxy `/api/v1/public/competition/readiness` and `/api/v1/public/*` variants. | Hestia proxy denies public APOLLO paths; public pages call APOLLO server-side only. | Browser proxy becomes public APOLLO tunnel or leaks headers/cookies. | `planning/milestones/milestone-3.0-evidence/F15-hestia-public-proxy-denial.md` |
| F16 | Hestia, APOLLO | Hestia `/competition` renders when APOLLO public readiness source is absent/unavailable. | Unavailable state is explicit; no fake records, no invented stats, no misleading live claims. | UI fabricates competition facts or hides unavailable source state. | `planning/milestones/milestone-3.0-evidence/F16-hestia-unavailable-state.md` |
| F17 | Hestia, APOLLO | Hestia `/competition` consumes only `GET /api/v1/public/competition/readiness`, `/leaderboards`, and `/game-identity` via `src/lib/server/public-competition.ts`. | No staff/internal APOLLO route is called; no UI computes rating, analytics, safety, tournament, or identity truth. | Hestia consumes internal contracts or derives source truth. | `planning/milestones/milestone-3.0-evidence/F17-hestia-public-contract-only.md` |
| F18 | ashton-mcp-gateway, ATHENA, Prometheus | `POST /mcp/v1/tools/call` routes `athena.get_current_occupancy` and `athena.get_current_zone_occupancy` to ATHENA `GET /api/v1/presence/count`; audit row persists; bad caller rejected. | First routed read writes sanitized audit row; second routed read works; missing/bad caller is rejected before route. | Audit missing, caller bypass, undeclared arg accepted, or upstream path widens. | `planning/milestones/milestone-3.0-evidence/F18-gateway-routed-read-audit.md` |
| F19 | ashton-mcp-gateway, ashton-proto | Configure `GATEWAY_MANIFEST_DIR` with symlink directory/file and path escape. | `internal/manifest.LoadDir` rejects symlink dir/file and non-regular manifest files. | Manifest escape loads, APOLLO/HERMES route becomes routable, or unsafe file read occurs. | `planning/milestones/milestone-3.0-evidence/F19-gateway-manifest-escape.md` |
| F20 | APOLLO, ATHENA, Hestia, Themis, ashton-mcp-gateway, ashton-proto, Prometheus | Cross-version composition: every repo at currently deployed version and every repo at HEAD is listed in `planning/compatibility-matrix.md`; smoke current deployed pairings. | Matrix states deployed and HEAD versions, last verified time, compatible-with versions, and known drift. | Hidden drift, untested compatibility claim, or branch-only closure. | `planning/milestones/milestone-3.0-evidence/F20-cross-version-compatibility.md` |
| F21 | APOLLO, Themis | Tournament flow uses `POST /api/v1/competition/tournaments/{tournamentID}/rounds/advance` after a finalized canonical result. | Advancement consumes finalized canonical result; tournament version guards hold; result is not mutated by advancement. | Tournament advances on draft/disputed/void result or mutates result truth. | `planning/milestones/milestone-3.0-evidence/F21-tournament-final-result-bound.md` |
| F22 | APOLLO, Themis | `GET /api/v1/competition/safety/review` and safety writes with member, supervisor, manager without trusted-surface, manager with trusted-surface. | Member rejected, supervisor rejected, manager without trusted-surface rejected, manager with trusted-surface accepted. | Safety details leak or role/trusted-surface gate fails. | `planning/milestones/milestone-3.0-evidence/F22-safety-trusted-surface.md` |
| F23 | APOLLO, Themis, Hestia | `POST /api/v1/competition/reliability/events` records event with staff attribution; then probe public readiness, leaderboards, game identity, Hestia page. | Reliability event exists internally with attribution; no public/member surface exposes event details. | Reliability facts leak publicly or attribution missing internally. | `planning/milestones/milestone-3.0-evidence/F23-reliability-private-attribution.md` |
| F24 | APOLLO | `GET /api/v1/lobby/match-preview` through `internal/ares/competition.go` with active queue/rating data. | ARES v2 returns proposal preview using legacy active rating path and OpenSkill comparison policy metadata only; no match/result/rating mutation. | ARES mutates lifecycle/rating or becomes a result source. | `planning/milestones/milestone-3.0-evidence/F24-ares-preview-no-mutation.md` |
| F25 | APOLLO | Regenerate analytics projections through `internal/competition/analytics.go` from finalized canonical results twice. | Projection output is deterministic and internal; no public route exposes analytics internals. | Nondeterministic analytics, untrusted input ownership, or public leak. | `planning/milestones/milestone-3.0-evidence/F25-analytics-deterministic-internal.md` |
| F26 | APOLLO, Prometheus | Probe APOLLO `/api/v1/health`, migration/init path, and currently deployed image `ghcr.io/ixxet/apollo:sha-bf3119b`; compare to repo HEAD `v0.19.1-110-g46d2425`. | Deploy truth ledger explicitly states APOLLO live image and whether 3B.11-3B.20 surfaces are absent, present, or rolled forward under Controller approval. | Worker claims APOLLO 3B is live without proof. | `planning/milestones/milestone-3.0-evidence/F26-apollo-deploy-truth.md` |
| F27 | ATHENA, Prometheus | Probe ATHENA deployed image `v0.8.2`, `/api/v1/health`, `/metrics`, and edge proxy allowlist. | Manifest/runbook mismatch is documented; live route proof matches actual image; external proxy exposes only health and edge tap. | Hidden ATHENA drift or external access to internal reads/metrics. | `planning/milestones/milestone-3.0-evidence/F27-athena-deploy-truth.md` |
| F28 | APOLLO, Prometheus | Gate 6 telemetry smoke: APOLLO exports required competition metrics and Prometheus scrape config/alert rule exists. | Metrics names are exported, scrape target is configured or equivalent, alert example is committed. | Telemetry remains test-only but Gate 6 is claimed Pass. | `planning/milestones/milestone-3.0-evidence/F28-telemetry-gate-6.md` |
| F29 | APOLLO, Hestia, Themis | Negative leak corpus hits public readiness, leaderboards, game identity, Hestia `/competition`, Hestia proxy, and Themis proxy. | Forbidden strings/fields never appear: raw `user_id`, request IDs, match IDs, canonical result IDs, watermarks, sample sizes, OpenSkill values, ARES internals, safety facts, command internals, trusted-surface fields. | Any forbidden field appears in public/member browser surface. | `planning/milestones/milestone-3.0-evidence/F29-public-leak-corpus.md` |
| F30 | APOLLO, ATHENA, gateway | SIGTERM/graceful-shutdown smoke for APOLLO HTTP/NATS, ATHENA HTTP/publisher, and gateway HTTP during in-flight reads/writes. | Processes drain or shut down within documented timeout; no hang; partial requests fail cleanly. | Panic, hang, double publish/write, or unbounded shutdown. | `planning/milestones/milestone-3.0-evidence/F30-graceful-shutdown.md` |

## Destructive Harness Catalog

Hard rule: if any harness panics instead of failing cleanly, that is a `must_fix_now` finding for the next milestone and must not be silently re-classified.

### APOLLO harnesses

| Harness | Scenario | Expected behavior | Failure mode that becomes `must_fix_now` |
| --- | --- | --- | --- |
| A01 oversized-mutating-json | Oversized JSON body to every mutating route, including competition commands, session/match/result/tournament/safety routes, booking public requests, and workout writes. | Rejected before parse with structured outcome/error and no mutation. | Panic, partial mutation, unstructured 500, or body accepted. |
| A02 malformed-json-unknown-fields | Malformed JSON and unknown fields on mutating APOLLO routes. | Rejected with structured 400-compatible error and no mutation. | Panic, silent field acceptance where strict contract is required, or mutation. |
| A03 sigterm-http-nats | SIGTERM during in-flight HTTP and NATS consumer handling. | Graceful shutdown without hang, bounded drain, no double write. | Hang, panic, lost audit, or duplicate mutation. |
| A04 duplicate-arrival-replay | Duplicate identified arrival replay through `internal/consumer/identified_presence.go`. | Idempotent visit outcome. | Duplicate visit or panic. |
| A05 duplicate-departure-replay | Duplicate identified departure replay once departure consumer/path is active or explicitly record `already_false` if no departure consumer exists. | Idempotent or honestly marked not active. | Hidden departure claim or duplicate mutation. |
| A06 stale-result-version | Stale `expected_result_version` on result record/finalize/dispute/correct/void. | Rejected with structured outcome and no mutation. | Stale version mutates or returns ambiguous success. |
| A07 stale-tournament-version | Stale `expected_tournament_version` on tournament seed/lock/bind/advance. | Rejected with structured outcome and no mutation. | Stale version mutates tournament. |
| A08 safety-missing-trusted-surface | Missing trusted-surface on safety readiness/review/report/block/reliability routes. | 403 and no information leak. | Any safety detail disclosed or mutation accepted. |
| A09 member-on-staff-route | Member token on staff competition route. | 403 and no internal details. | Member accesses staff route or learns private facts. |
| A10 supervisor-on-safety-review | Supervisor token on `competition_safety_review` route. | 403. | Supervisor reads safety review or writes safety/reliability. |
| A11 manager-no-trusted-surface | Manager token on safety route without trusted-surface. | 403. | Manager bypasses trusted-surface requirement. |
| A12 giant-workout-payload | Giant workout payload to APOLLO workout writes. | Conservative rejection before dangerous parse or memory growth. | OOM risk, panic, or accepted giant payload. |
| A13 malformed-arrival-proto | Malformed identified-arrival proto payload. | Rejected, logged, no panic, no visit mutation. | Panic or visit mutation from invalid payload. |
| A14 concurrent-finalize | Concurrent finalize calls on the same result. | Only one wins; stale loser rejected; lifecycle event audited. | Double-finalize, missing audit, or race. |
| A15 public-output-leak-corpus | Public readiness, leaderboards, and game-identity output scanned for raw `user_id`, internal request IDs, match IDs, canonical result IDs, projection watermarks, sample sizes, OpenSkill mu/sigma/comparison, ARES internals, safety facts, command internals, trusted-surface fields. | Forbidden fields absent from every public/member-safe output. | Any forbidden field appears. |

### ATHENA harnesses

| Harness | Scenario | Expected behavior | Failure mode that becomes `must_fix_now` |
| --- | --- | --- | --- |
| T01 bad-node-token | Bad node token on `POST /api/v1/edge/tap`. | Rejected with 401/403 structured error and no publish. | Accepted bad node or leaked token details. |
| T02 malformed-tap-payload | Malformed tap payload, unknown fields, invalid direction/result/time. | Rejected, no panic, no publish. | Panic or invalid publish. |
| T03 repeated-and-stale-observations | Repeated `in`, repeated `out`, stale observation, and `fail` observation. | Deterministic observed/accepted/published behavior. | Occupancy drift, duplicate accepted session, or nondeterminism. |
| T04 history-append-failure | File/Postgres history append failure under configured history path/store. | Behavior matches documented design and is observable. | Silent loss or hidden success. |
| T05 publisher-transient-failure | NATS publisher transient failure. | Retry/backoff exercised and bounded; failure observable. | Unbounded retry, dropped accepted event without evidence, or panic. |
| T06 restart-history-replay | Restart with configured history/Postgres path. | Replays committed pass observations before serving. | Occupancy/session state lost or replay duplicates. |
| T07 sigterm-during-publish | SIGTERM during publish. | Graceful shutdown; no hang; publish outcome observable. | Hang, duplicate publish, or panic. |

### Gateway harnesses

| Harness | Scenario | Expected behavior | Failure mode that becomes `must_fix_now` |
| --- | --- | --- | --- |
| G01 bad-caller-token | Bad `X-Gateway-Trusted-Caller-Token` or unknown `X-Gateway-API-Key` on `POST /mcp/v1/tools/call`. | Rejected before routing. | Upstream route reached or caller ambiguity accepted. |
| G02 undeclared-argument | Undeclared argument in tool call. | Rejected as invalid argument and audited if crossing route boundary. | Argument accepted or passed upstream. |
| G03 wrong-type-optional-argument | Wrong-type optional argument. | Rejected before ATHENA. | Type confusion or upstream call. |
| G04 audit-persistence-failure | Audit store unavailable/failing. | Follows declared fail-closed policy and is observable. | Tool succeeds without audit. |
| G05 manifest-path-symlink-escape | Manifest path/symlink escape. | Rejected by manifest loader. | Unsafe manifest loaded. |

### Hestia harnesses (Playwright)

| Harness | Scenario | Expected behavior | Failure mode that becomes `must_fix_now` |
| --- | --- | --- | --- |
| H01 public-competition-unavailable | Public competition page renders with APOLLO source absent. | Explicit unavailable state, no fake records. | Fake stats or misleading live state. |
| H02 member-proxy-blocks-public | Member proxy blocks `/api/v1/public/*` paths. | Denied through browser proxy. | Public APOLLO proxy tunnel exposed. |
| H03 private-route-denial | Private route denial for unauthenticated user. | Redirect/deny without private data. | Private route renders. |
| H04 no-trusted-surface-browser | Browser bundle/local storage/header capture checked for trusted-surface headers. | Trusted-surface headers absent from browser bundle and local storage. | Browser can see or set trusted-surface secret/header. |

### Themis harnesses (Playwright)

| Harness | Scenario | Expected behavior | Failure mode that becomes `must_fix_now` |
| --- | --- | --- | --- |
| M01 supervisor-command-read | Supervisor reads competition commands/readiness without structure-apply controls. | Read-only controls only; no structure/safety apply. | Supervisor sees or can trigger structure/safety apply. |
| M02 manager-safety-trusted | Manager reads safety review only with trusted-surface. | Server-side trusted-surface injects; browser cannot forge. | Manager browser bypasses server trust boundary. |
| M03 public-proxy-denial | Public proxy denial works. | `/api/v1/public/*` denied through Themis proxy. | Themis becomes public proxy. |
| M04 manager-competition-e2e | Competition readiness, command dry-run/apply, result lifecycle e2e for manager session. | Uses APOLLO internal ops contracts only; no UI-owned truth. | Themis computes source truth or bypasses APOLLO contract. |

## Evidence Pack Requirements

For Go repos APOLLO, ATHENA, and ashton-mcp-gateway, the Worker must produce:

- `coverage.out` from `go test -coverprofile=coverage.out ./...`
- JUnit XML from `gotestsum --junitfile junit.xml ./...` or an equivalent parser-readable JUnit report
- destructive harness logs, named per scenario ID
- race-detector run output from `go test -race ./internal/...`
- vet/build output from `go vet ./...` and `go build ./...`

For Hestia and Themis, the Worker must produce:

- `npm run check` output
- `npm test` output
- `npm run build` output
- Playwright report/output for the required desktop and mobile harnesses
- proxy-boundary evidence showing browser-denied paths and stripped trusted-surface headers

Cross-repo artifacts must be saved under:

- `ashton-platform/planning/milestones/milestone-3.0-evidence/`

Each smoke flow must have one evidence artifact named with its Flow ID. Evidence may be kubectl logs, curl output, DB query extracts with secrets redacted, Playwright reports, or structured command output. Compatibility matrix must be published at:

- `ashton-platform/planning/compatibility-matrix.md`

The compatibility matrix must include columns: `Repo | Deployed version | Repo HEAD version | Last verified at | Compatible with versions`.

Coverage and mutation score are informational only. Do not gate on a hard percentage. Reject only if the artifact is missing, malformed, or irrelevant; do not reject because the number is low.

Telemetry counter export for Gate 6 closure requires exported APOLLO metrics covering the launch-expansion-audit telemetry list:

- `competition_result_write_attempt_total`
- `competition_result_write_reject_total{reason}`
- `competition_consensus_vote_total{tier,outcome}` when explicit consensus voting ships; until then record as deferred/not active, not missing
- `competition_dispute_open_total{tier,reason}`
- `competition_dispute_resolution_duration_seconds`
- `rating_update_duration_seconds{policy_version}`
- `rating_rebuild_rows_scanned_total`
- `rating_policy_delta_total{legacy,openskill}`
- `ares_preview_total{sport,mode,tier}`
- `ares_queue_assignment_failure_total{reason}`
- `trusted_surface_failure_total{reason}`
- `booking_public_submit_total`
- `booking_idempotency_conflict_total`
- `booking_approval_conflict_total`
- `public_leak_test_failure_total`
- `tournament_advancement_total{format}`
- `tournament_seed_lock_total`
- `safety_report_open_total{category}`
- `safety_block_active_total`
- `reliability_event_total{kind}`
- `game_identity_projection_duration_seconds`

Gate 6 also requires one Prometheus scrape config or equivalent and one alert rule example committed to deploy docs. Minimum alert examples are rating update p95 ceiling, dispute queue age, trusted-surface failure spike, public receipt not-found spike, result duplicate reject spike, and game identity projection duration regression.

## Commit Ladder

| Slice | Repo focus | Intended commit | Purpose | Hard stop |
| --- | --- | --- | --- | --- |
| 1 | apollo: telemetry counters export | `feat: export competition trust telemetry counters` | Close Gate 6 runtime metrics export without changing product behavior. | Stop if metrics require new policy behavior or public surface. |
| 2 | apollo: cluster smoke harness scaffolding | `test: add APOLLO cluster competition smoke harness` | Add test/harness code only for deploy smoke and trust-spine probes. | Stop if production behavior changes are needed in this slice. |
| 3 | apollo: auditor `must_fix_now` boundary hardening | `fix: harden APOLLO competition deploy smoke boundary` | Patch only proven APOLLO regressions found by Auditor/Controller. | Stop if fix widens features, switches OpenSkill read path, or exposes private truth. |
| 4 | athena: cluster smoke harness and boundary hardening | `test: add ATHENA ingress smoke harness` or `fix: harden ATHENA ingress boundary` | Prove or patch ingress replay/publish/shutdown boundaries if classified in scope. | Stop if this becomes ATHENA real-ingress redesign or new event-store work. |
| 5 | ashton-mcp-gateway: contract test scaffolding and boundary fixes | `test: add gateway routed-read contract smoke` or `fix: harden gateway route boundary` | Prove gateway read-only, audit, caller identity, and manifest containment. | Stop if APOLLO/HERMES routing, writes, HITL, or rate limiting enters scope. |
| 6 | hestia: public competition and proxy e2e | `test: reinforce Hestia public competition smoke` | Prove public/member-safe APOLLO projections and proxy denial. | Stop if Hestia fixture/test-selector debt cleanup or staff UI enters scope. |
| 7 | themis: staff competition and trusted-surface e2e | `test: reinforce Themis trusted-surface smoke` | Prove internal ops boundary and server-side trusted-surface injection. | Stop if Themis dated fixture/test-selector cleanup or public truth role enters scope. |
| 8 | Prometheus: scrape config and alert rule | `feat: add competition telemetry scrape and alert example` | Wire APOLLO telemetry into deploy docs/config. | Stop if deployment roll-forward was not approved by Controller. |
| 9 | ashton-platform: compatibility matrix | `docs: publish milestone 3.0 compatibility matrix` | Publish exact deployed/head compatibility truth. | Stop if any deployed version is unknown and not marked unknown. |
| 10 | ashton-platform: evidence directory and audit ledger | `docs: record milestone 3.0 smoke evidence` | Commit evidence index, flow ledger, gate movement, and residual risks. | Stop if evidence is branch-only, missing, or contradicts runtime truth. |

Compression rule: do not collapse repos. Each slice equals one commit on one repo.

## Agent 1 — Controller / Orchestrator

```text
Role: Controller / Orchestrator
Milestone: 3.0 Deploy Smoke Proof + Cross-Repo Hardening

You coordinate the milestone. You do not implement runtime or UI fixes. You may make tiny platform doc corrections only when they resolve false deploy-truth wording needed for orchestration. Your primary job is to classify, sequence, approve or deny deploy movement, and prevent scope creep.

Hard Scope Locks:
- No public tournament surfaces.
- No OpenSkill read-path switch.
- No policy wrapper expansion; calibration, decay, climbing caps, and related rating-policy behavior stay deferred.
- No cross-sport transfer.
- No ATHENA Postgres event-store or schema widening beyond currently shipped/deployed ATHENA truth.
- No Hestia/Themis dated fixture or test-selector debt cleanup.
- No new product capability of any kind.
- No demo work.
- No fuzz testing unless Controller scopes a real corpus, real target, and real duration; no 30-second fuzz theater.
- No fake deploy claims.
- No branch-only closure claims.
- No messaging/chat.
- No broad public social graph, public profiles, public scouting, spectator feed, quests, XP, MVP, drafts, drills, or onboarding game loops.
- No public safety report, safety block, reliability event, safety review, manager note, or moderation detail exposure.
- No public ARES internals, OpenSkill mu/sigma/comparison rows, analytics internals, command internals, trusted-surface fields, source result IDs, projection watermarks, or raw user IDs.
- No booking/commercial/proposal workflow expansion.
- No new tournament formats.
- No recurrence, broad hours editor, schedule-governance expansion, court splitting, or recurring schedule line.
- No project-wide SemVer governance conversion.
- No APOLLO ownership drift, no UI-owned truth, no Themis public role, no Hestia internal role.
- No gateway writes, HITL, rate limiting, APOLLO routing, or HERMES routing.
- No silent deployment roll-forward.

Classification Buckets:
- must_fix_now: A proven trust-spine, privacy, deploy, compatibility, telemetry, authz, idempotency, panic, or source-truth regression that blocks Milestone 3.0 closeout.
- must_prove_now: A required smoke, harness, telemetry, compatibility, or deploy-truth claim that may be correct but lacks committed evidence.
- can_patch_if_clean: A bounded, non-product hardening patch approved by Controller after Auditor evidence shows it directly supports this milestone.
- defer: Real work outside Milestone 3.0.
- already_false: A claim current repo/deploy truth already disproves; correct it in the ledger, do not convert it into work.

Required first audit questions:
1. What is the live APOLLO image, and does it include any 3B.11-3B.20 competition routes?
2. What is the live ATHENA image, and why does Prometheus GitOps show v0.8.2 while the ATHENA edge runbook still says v0.7.0?
3. Are Hestia, Themis, and ashton-mcp-gateway deployed anywhere, or are they repo-only truth?
4. Which cross-repo smoke flows are currently impossible against live deploy without a Controller-approved roll-forward?
5. Which flows are must_prove_now versus must_fix_now?
6. Is APOLLO telemetry export absent, partially implemented, or already present behind undocumented code?
7. What exact Prometheus scrape config and alert example are required to close Gate 6?
8. Does planning/compatibility-matrix.md already exist, and is it accurate?
9. Should ATHENA deploy truth be rolled forward, left as v0.8.2 manifest truth, or documented as drift pending live inspection?
10. Does APOLLO require deploy roll-forward to prove 3B.11-3B.20, and who approves it?
11. Are Hestia/Themis e2e dated fixture/test-selector failures unrelated debt that must be isolated from this milestone?
12. Is must_fix_now scope small enough for this milestone, or should this become Milestone 3.0.1 hardening?
13. Are any already_false claims present in docs, README, runbooks, or packet interpretation?
14. Are Gates 6 and 10 closable without widening product surface?
15. What Gate 8 ceilings can be declared honestly without claiming full scale validation?

Read-first list:
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/README.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/IMPLEMENTATION-BOARD.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/STARSHOT-VISION.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/DEFERMENTS.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/FLOWS.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/audits/2026-04-29-competition-system-audit.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/apollo.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/athena.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/hermes.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/ashton-mcp-gateway.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/sprints/TRACER-MATRIX.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/runbooks/phase-2-launch.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/milestones/milestone-3.0-packet.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/README.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/launch-expansion-audit.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/roadmap.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/growing-pains.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/hardening/
- /Users/zizo/Personal-Projects/ASHTON/apollo/cmd/apollo/main.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/server/server.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/command.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/service.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/history.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/tournament.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/safety.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/public.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/game_identity.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/analytics.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/rating/legacy.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/rating/openskill.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/ares/competition.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/authz/authz.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/authz/trusted_surface.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/consumer/identified_presence.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/db/migrations/
- /Users/zizo/Personal-Projects/ASHTON/athena/README.md
- /Users/zizo/Personal-Projects/ASHTON/athena/docs/
- /Users/zizo/Personal-Projects/ASHTON/athena/cmd/athena/main.go
- /Users/zizo/Personal-Projects/ASHTON/athena/internal/
- /Users/zizo/Personal-Projects/ASHTON/hestia/README.md
- /Users/zizo/Personal-Projects/ASHTON/hestia/src/routes/
- /Users/zizo/Personal-Projects/ASHTON/hestia/src/lib/server/
- /Users/zizo/Personal-Projects/ASHTON/themis/README.md
- /Users/zizo/Personal-Projects/ASHTON/themis/src/routes/
- /Users/zizo/Personal-Projects/ASHTON/themis/src/lib/server/
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/README.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/internal/
- /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/
- /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/docs/runbooks/

Required read-only version commands:
- git -C /Users/zizo/Personal-Projects/ASHTON/apollo describe --tags --always
- git -C /Users/zizo/Personal-Projects/ASHTON/athena describe --tags --always
- git -C /Users/zizo/Personal-Projects/ASHTON/hestia describe --tags --always
- git -C /Users/zizo/Personal-Projects/ASHTON/themis describe --tags --always
- git -C /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway describe --tags --always
- git -C /Users/zizo/Personal-Projects/ASHTON/ashton-platform describe --tags --always
- git -C /Users/zizo/Personal-Projects/ASHTON/ashton-proto describe --tags --always
- git -C /Users/zizo/Personal-Projects/Computers/Prometheus describe --tags --always 2>/dev/null || echo "no tags"
- git -C /Users/zizo/Personal-Projects/ASHTON/apollo log --oneline -20
- git -C /Users/zizo/Personal-Projects/ASHTON/athena log --oneline -20

Required output format:
1. Status table: Repo | HEAD | Deployed | Drift | Controller ruling
2. Classification ledger: ID | Finding/claim | Bucket | Evidence | Owner | Deadline
3. Commit-ladder ruling: slice-by-slice approve/hold/defer
4. Deploy-truth ruling: APOLLO, ATHENA, Hestia, Themis, gateway, Prometheus
5. Gate ruling: Gate 6, Gate 8, Gate 10 planned movement
6. Final Controller verdict: proceed / split into 3.0.1 / stop
```

## Agent 2 — Auditor / Gap Mapper

```text
Role: Auditor / Gap Mapper
Milestone: 3.0 Deploy Smoke Proof + Cross-Repo Hardening

You are read-only. Do not fix code, docs, deploy files, tests, or manifests. Your job is to map shipped surface to existing proof, classify gaps, capture baseline coverage and deploy drift, and decide what the Worker may touch.

Hard Scope Locks:
- No public tournament surfaces.
- No OpenSkill read-path switch.
- No policy wrapper expansion; calibration, decay, climbing caps, and related rating-policy behavior stay deferred.
- No cross-sport transfer.
- No ATHENA Postgres event-store or schema widening beyond currently shipped/deployed ATHENA truth.
- No Hestia/Themis dated fixture or test-selector debt cleanup.
- No new product capability of any kind.
- No demo work.
- No fuzz testing unless Controller scopes a real corpus, real target, and real duration; no 30-second fuzz theater.
- No fake deploy claims.
- No branch-only closure claims.
- No messaging/chat.
- No broad public social graph, public profiles, public scouting, spectator feed, quests, XP, MVP, drafts, drills, or onboarding game loops.
- No public safety report, safety block, reliability event, safety review, manager note, or moderation detail exposure.
- No public ARES internals, OpenSkill mu/sigma/comparison rows, analytics internals, command internals, trusted-surface fields, source result IDs, projection watermarks, or raw user IDs.
- No booking/commercial/proposal workflow expansion.
- No new tournament formats.
- No recurrence, broad hours editor, schedule-governance expansion, court splitting, or recurring schedule line.
- No project-wide SemVer governance conversion.
- No APOLLO ownership drift, no UI-owned truth, no Themis public role, no Hestia internal role.
- No gateway writes, HITL, rate limiting, APOLLO routing, or HERMES routing.
- No silent deployment roll-forward.

Classification Buckets:
- must_fix_now: A proven trust-spine, privacy, deploy, compatibility, telemetry, authz, idempotency, panic, or source-truth regression that blocks Milestone 3.0 closeout.
- must_prove_now: A required smoke, harness, telemetry, compatibility, or deploy-truth claim that may be correct but lacks committed evidence.
- can_patch_if_clean: A bounded, non-product hardening patch that may be approved by Controller after your evidence shows it directly supports this milestone.
- defer: Real work outside Milestone 3.0.
- already_false: A claim current repo/deploy truth already disproves; correct it in the ledger, do not convert it into work.

Read-first list:
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/README.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/IMPLEMENTATION-BOARD.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/STARSHOT-VISION.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/DEFERMENTS.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/FLOWS.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/audits/2026-04-29-competition-system-audit.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/apollo.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/athena.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/hermes.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/ashton-mcp-gateway.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/sprints/TRACER-MATRIX.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/runbooks/phase-2-launch.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/milestones/milestone-3.0-packet.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/README.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/launch-expansion-audit.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/roadmap.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/growing-pains.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/hardening/
- /Users/zizo/Personal-Projects/ASHTON/apollo/cmd/apollo/main.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/server/server.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/command.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/service.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/history.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/tournament.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/safety.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/public.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/game_identity.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/analytics.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/rating/legacy.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/rating/openskill.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/ares/competition.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/authz/authz.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/authz/trusted_surface.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/consumer/identified_presence.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/db/migrations/
- /Users/zizo/Personal-Projects/ASHTON/athena/README.md
- /Users/zizo/Personal-Projects/ASHTON/athena/docs/
- /Users/zizo/Personal-Projects/ASHTON/athena/cmd/athena/main.go
- /Users/zizo/Personal-Projects/ASHTON/athena/internal/
- /Users/zizo/Personal-Projects/ASHTON/hestia/README.md
- /Users/zizo/Personal-Projects/ASHTON/hestia/src/routes/
- /Users/zizo/Personal-Projects/ASHTON/hestia/src/lib/server/
- /Users/zizo/Personal-Projects/ASHTON/themis/README.md
- /Users/zizo/Personal-Projects/ASHTON/themis/src/routes/
- /Users/zizo/Personal-Projects/ASHTON/themis/src/lib/server/
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/README.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/internal/
- /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/
- /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/docs/runbooks/

Deeper internal files to skim:
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/*.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/server/*competition*
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/visits/
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/athena/
- /Users/zizo/Personal-Projects/ASHTON/athena/internal/edge/
- /Users/zizo/Personal-Projects/ASHTON/athena/internal/presence/
- /Users/zizo/Personal-Projects/ASHTON/athena/internal/publisher/
- /Users/zizo/Personal-Projects/ASHTON/athena/internal/server/
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/internal/server/
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/internal/gateway/
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/internal/manifest/
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/internal/audit/
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/internal/identity/

Required gap map output:
- shipped surface -> existing test evidence -> gap classification
- cross-repo flow -> existing automated proof -> gap classification
- per repo: current repo HEAD, current deployed version, drift documented or hidden
- each APOLLO 3B.11-3B.20 layer -> owner -> route/service/table evidence -> public/private boundary -> gap
- Themis/Hestia route/proxy boundary -> proof -> gap
- Gateway route/audit/manifest boundary -> proof -> gap
- Prometheus deploy manifest and ServiceMonitor/alert state -> proof -> gap

Coverage baseline capture:
- APOLLO: go test -coverprofile=coverage.out ./...
- ATHENA: go test -coverprofile=coverage.out ./...
- ashton-mcp-gateway: go test -coverprofile=coverage.out ./...
- Record percentages if emitted; do not set a target and do not classify low percentage as a blocker by itself.

Required deploy audit:
- Inspect current APOLLO deployment image, env, health, and routes.
- Inspect current ATHENA deployment image, env, health, edge proxy, metrics, and migrations.
- Inspect current Hestia deployment if any.
- Inspect current Themis deployment if any.
- Inspect current ashton-mcp-gateway deployment if any.
- Confirm or deny each repo's deployed version.
- Record whether deploy drift is documented or hidden.

Required output format:
1. Status table: Repo | HEAD | Deployed | Drift | Evidence
2. Gap ledger: ID | Surface/flow | Existing evidence | Classification | Rationale
3. must_fix_now list
4. must_prove_now list
5. can_patch_if_clean candidates requiring Controller approval
6. defer list
7. already_false list
8. ATHENA deploy-roll-forward ruling
9. APOLLO deploy-roll-forward ruling
10. Final Auditor recommendation: proceed / split / stop
```

## Agent 3 — Worker / Hardening Implementer

```text
Role: Worker / Hardening Implementer
Milestone: 3.0 Deploy Smoke Proof + Cross-Repo Hardening

Binding: implement only Auditor `must_fix_now` findings and Controller-approved `can_patch_if_clean` items. Do not act on `must_prove_now` except by producing evidence. `already_false` claims do not become work. Do not tag in this pass. Do not silently deploy or roll forward any image. Do not modify files unrelated to the approved slice.

Hard Scope Locks:
- No public tournament surfaces.
- No OpenSkill read-path switch.
- No policy wrapper expansion; calibration, decay, climbing caps, and related rating-policy behavior stay deferred.
- No cross-sport transfer.
- No ATHENA Postgres event-store or schema widening beyond currently shipped/deployed ATHENA truth.
- No Hestia/Themis dated fixture or test-selector debt cleanup.
- No new product capability of any kind.
- No demo work.
- No fuzz testing unless Controller scopes a real corpus, real target, and real duration; no 30-second fuzz theater.
- No fake deploy claims.
- No branch-only closure claims.
- No messaging/chat.
- No broad public social graph, public profiles, public scouting, spectator feed, quests, XP, MVP, drafts, drills, or onboarding game loops.
- No public safety report, safety block, reliability event, safety review, manager note, or moderation detail exposure.
- No public ARES internals, OpenSkill mu/sigma/comparison rows, analytics internals, command internals, trusted-surface fields, source result IDs, projection watermarks, or raw user IDs.
- No booking/commercial/proposal workflow expansion.
- No new tournament formats.
- No recurrence, broad hours editor, schedule-governance expansion, court splitting, or recurring schedule line.
- No project-wide SemVer governance conversion.
- No APOLLO ownership drift, no UI-owned truth, no Themis public role, no Hestia internal role.
- No gateway writes, HITL, rate limiting, APOLLO routing, or HERMES routing.
- No silent deployment roll-forward.

Classification Buckets:
- must_fix_now: A proven trust-spine, privacy, deploy, compatibility, telemetry, authz, idempotency, panic, or source-truth regression that blocks Milestone 3.0 closeout.
- must_prove_now: A required smoke, harness, telemetry, compatibility, or deploy-truth claim that may be correct but lacks committed evidence.
- can_patch_if_clean: A bounded, non-product hardening patch approved by Controller after Auditor evidence shows it directly supports this milestone.
- defer: Real work outside Milestone 3.0.
- already_false: A claim current repo/deploy truth already disproves; correct it in the ledger, do not convert it into work.

Read-first list:
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/README.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/IMPLEMENTATION-BOARD.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/STARSHOT-VISION.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/DEFERMENTS.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/FLOWS.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/audits/2026-04-29-competition-system-audit.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/apollo.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/athena.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/hermes.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/ashton-mcp-gateway.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/sprints/TRACER-MATRIX.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/runbooks/phase-2-launch.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/milestones/milestone-3.0-packet.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/README.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/launch-expansion-audit.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/roadmap.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/growing-pains.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/hardening/
- /Users/zizo/Personal-Projects/ASHTON/apollo/cmd/apollo/main.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/server/server.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/command.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/service.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/history.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/tournament.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/safety.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/public.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/game_identity.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/analytics.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/rating/legacy.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/rating/openskill.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/ares/competition.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/authz/authz.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/authz/trusted_surface.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/consumer/identified_presence.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/db/migrations/
- /Users/zizo/Personal-Projects/ASHTON/athena/README.md
- /Users/zizo/Personal-Projects/ASHTON/athena/docs/
- /Users/zizo/Personal-Projects/ASHTON/athena/cmd/athena/main.go
- /Users/zizo/Personal-Projects/ASHTON/athena/internal/
- /Users/zizo/Personal-Projects/ASHTON/hestia/README.md
- /Users/zizo/Personal-Projects/ASHTON/hestia/src/routes/
- /Users/zizo/Personal-Projects/ASHTON/hestia/src/lib/server/
- /Users/zizo/Personal-Projects/ASHTON/themis/README.md
- /Users/zizo/Personal-Projects/ASHTON/themis/src/routes/
- /Users/zizo/Personal-Projects/ASHTON/themis/src/lib/server/
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/README.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/internal/
- /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/
- /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/docs/runbooks/

Required slices:
1. apollo telemetry counters export
2. apollo cluster smoke harness scaffolding, test code only unless Controller approved
3. apollo boundary hardening only for Auditor `must_fix_now`
4. athena cluster smoke harness and boundary hardening only for Auditor `must_fix_now`
5. ashton-mcp-gateway contract test scaffolding and boundary fixes only for Auditor `must_fix_now`
6. hestia e2e smoke for public competition and member proxy denial
7. themis e2e smoke for staff competition and trusted-surface
8. Prometheus scrape config and alert rule for new metrics, only if deploy movement/config is Controller approved
9. ashton-platform compatibility matrix
10. ashton-platform milestone-3.0-evidence directory and audit ledger

Required tests per repo:
- APOLLO: sqlc generate -f db/sqlc.yaml; git diff --check; go test -coverprofile=coverage.out ./...; gotestsum --junitfile junit.xml ./...; go test -race ./internal/...; go test -count=1 ./internal/competition ./internal/rating ./internal/ares ./internal/authz; go test -count=1 ./internal/server -run 'TestCompetition'; go vet ./...; go build ./cmd/apollo
- ATHENA: git diff --check; go test -coverprofile=coverage.out ./...; gotestsum --junitfile junit.xml ./...; go test -race ./internal/...; go vet ./...; go build ./cmd/athena
- ashton-mcp-gateway: git diff --check; go test -coverprofile=coverage.out ./...; gotestsum --junitfile junit.xml ./...; go test -race ./internal/...; go vet ./...; go build ./cmd/ashton-mcp-gateway
- Hestia: git diff --check; npm run check; npm test; npm run build; focused Playwright desktop/mobile for competition, identity, and proxy boundaries
- Themis: git diff --check if touched; npm run check; npm test; npm run build; focused Playwright for competition ops, safety, and trusted-surface if touched or if existing suite supports it
- Prometheus: git diff --check; kustomize build for touched app/observability paths; do not apply to cluster unless Controller approved
- ashton-platform: git diff --check

Required destructive harnesses:
- APOLLO A01-A15 from the packet Destructive Harness Catalog
- ATHENA T01-T07 from the packet Destructive Harness Catalog
- Gateway G01-G05 from the packet Destructive Harness Catalog
- Hestia H01-H04 from the packet Destructive Harness Catalog
- Themis M01-M04 from the packet Destructive Harness Catalog

Required cross-repo smoke probes:
- Execute or produce evidence for F01-F30 from the packet Cross-Repo Smoke Matrix.
- If a flow is impossible because deploy truth lacks a required surface, record it as must_prove_now or deploy-drift evidence; do not fake success.

Required evidence artifacts:
- coverage.out, junit.xml, race output, vet/build logs for Go repos.
- check/test/build/Playwright reports for Svelte repos.
- destructive harness logs named by scenario ID.
- per-flow smoke artifacts under /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/milestones/milestone-3.0-evidence/
- compatibility matrix at /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/compatibility-matrix.md
- telemetry scrape config/equivalent and alert rule example if Gate 6 is being closed.

Commit rules:
- One slice per commit.
- Do not mix repos in a commit.
- Do not create fix commits if there are no findings.
- Stage only relevant files.
- Patch-only intent.
- Do not cut tags in this pass.
- Do not push unless Controller explicitly instructs the milestone execution run to push.

Required output format:
1. Status table: Slice | Repo | Status | Commit hash if created | Evidence path
2. Fixes by repo
3. Files changed
4. Commands run and outcomes
5. Harness evidence summary
6. Smoke evidence summary
7. Truth split: repo truth / deploy truth / deferred truth
8. Residual risks
9. Items requiring Verifier ruling
```

## Agent 4 — Verifier / Arbiter

```text
Role: Verifier / Arbiter
Milestone: 3.0 Deploy Smoke Proof + Cross-Repo Hardening

You are read-only. Verify the evidence pack, touched files, live deploy truth, platform docs, and gate movement. Decide whether Milestone 3.0 is ready for closeout, valid with documented residual risk, or not yet.

Hard Scope Locks:
- No public tournament surfaces.
- No OpenSkill read-path switch.
- No policy wrapper expansion; calibration, decay, climbing caps, and related rating-policy behavior stay deferred.
- No cross-sport transfer.
- No ATHENA Postgres event-store or schema widening beyond currently shipped/deployed ATHENA truth.
- No Hestia/Themis dated fixture or test-selector debt cleanup.
- No new product capability of any kind.
- No demo work.
- No fuzz testing unless Controller scopes a real corpus, real target, and real duration; no 30-second fuzz theater.
- No fake deploy claims.
- No branch-only closure claims.
- No messaging/chat.
- No broad public social graph, public profiles, public scouting, spectator feed, quests, XP, MVP, drafts, drills, or onboarding game loops.
- No public safety report, safety block, reliability event, safety review, manager note, or moderation detail exposure.
- No public ARES internals, OpenSkill mu/sigma/comparison rows, analytics internals, command internals, trusted-surface fields, source result IDs, projection watermarks, or raw user IDs.
- No booking/commercial/proposal workflow expansion.
- No new tournament formats.
- No recurrence, broad hours editor, schedule-governance expansion, court splitting, or recurring schedule line.
- No project-wide SemVer governance conversion.
- No APOLLO ownership drift, no UI-owned truth, no Themis public role, no Hestia internal role.
- No gateway writes, HITL, rate limiting, APOLLO routing, or HERMES routing.
- No silent deployment roll-forward.

Classification Buckets:
- must_fix_now: A proven trust-spine, privacy, deploy, compatibility, telemetry, authz, idempotency, panic, or source-truth regression that blocks Milestone 3.0 closeout.
- must_prove_now: A required smoke, harness, telemetry, compatibility, or deploy-truth claim that may be correct but lacks committed evidence.
- can_patch_if_clean: A bounded, non-product hardening patch approved by Controller after Auditor evidence shows it directly supports this milestone.
- defer: Real work outside Milestone 3.0.
- already_false: A claim current repo/deploy truth already disproves; correct it in the ledger, do not convert it into work.

Read-first list:
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/README.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/IMPLEMENTATION-BOARD.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/STARSHOT-VISION.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/DEFERMENTS.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/FLOWS.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/audits/2026-04-29-competition-system-audit.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/apollo.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/athena.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/hermes.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/repo-briefs/ashton-mcp-gateway.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/sprints/TRACER-MATRIX.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/runbooks/phase-2-launch.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/milestones/milestone-3.0-packet.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/README.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/launch-expansion-audit.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/roadmap.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/growing-pains.md
- /Users/zizo/Personal-Projects/ASHTON/apollo/docs/hardening/
- /Users/zizo/Personal-Projects/ASHTON/apollo/cmd/apollo/main.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/server/server.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/command.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/service.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/history.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/tournament.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/safety.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/public.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/game_identity.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/competition/analytics.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/rating/legacy.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/rating/openskill.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/ares/competition.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/authz/authz.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/authz/trusted_surface.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/internal/consumer/identified_presence.go
- /Users/zizo/Personal-Projects/ASHTON/apollo/db/migrations/
- /Users/zizo/Personal-Projects/ASHTON/athena/README.md
- /Users/zizo/Personal-Projects/ASHTON/athena/docs/
- /Users/zizo/Personal-Projects/ASHTON/athena/cmd/athena/main.go
- /Users/zizo/Personal-Projects/ASHTON/athena/internal/
- /Users/zizo/Personal-Projects/ASHTON/hestia/README.md
- /Users/zizo/Personal-Projects/ASHTON/hestia/src/routes/
- /Users/zizo/Personal-Projects/ASHTON/hestia/src/lib/server/
- /Users/zizo/Personal-Projects/ASHTON/themis/README.md
- /Users/zizo/Personal-Projects/ASHTON/themis/src/routes/
- /Users/zizo/Personal-Projects/ASHTON/themis/src/lib/server/
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/README.md
- /Users/zizo/Personal-Projects/ASHTON/ashton-mcp-gateway/internal/
- /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/
- /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/docs/runbooks/

Inspect:
- Controller output
- Auditor output
- Worker output
- touched files in APOLLO, ATHENA, Hestia, Themis, ashton-mcp-gateway, Prometheus, ashton-platform, and ashton-proto if touched
- current live deploy images/env/routes
- platform docs, compatibility matrix, and evidence directory

Verification dimensions:
1. Gate 6 moved from Partial to Pass only if telemetry counters are exported and scraped/alerted.
2. Gate 10 moved from Partial to Pass only if compatibility matrix matches actual versions and smoke evidence.
3. Gate 8 ceilings are declared without claiming full scale validation.
4. Every F01-F30 evidence artifact exists, parses, and matches the named flow.
5. Every destructive harness log exists and is tied to the scenario ID.
6. Deploy-truth claims match Prometheus manifests and live cluster checks.
7. ATHENA deploy drift is resolved or documented honestly.
8. APOLLO deploy drift is resolved or documented honestly.
9. No public-facing surface widened.
10. No OpenSkill active read path was introduced.
11. No Hestia/Themis dated fixture/test-selector debt cleanup was mixed in.
12. No fake harnesses, placeholder outputs, or branch-only closure claims.
13. No UI-owned source truth appeared in Hestia or Themis.
14. No gateway write/HITL/rate-limit/broader-routing scope entered.
15. Coverage artifacts exist but no hard percentage threshold was enforced.
16. Compatibility matrix has deployed version, HEAD version, verification time, and compatible-with versions for each repo.

Required verification commands:
- APOLLO: git status -sb; git log --oneline -10; git diff --check; sqlc generate -f db/sqlc.yaml; go test -coverprofile=coverage.out ./...; gotestsum --junitfile junit.xml ./...; go test -race ./internal/...; go test -count=1 ./internal/competition ./internal/rating ./internal/ares ./internal/authz; go test -count=1 ./internal/server -run 'TestCompetition'; go vet ./...; go build ./cmd/apollo
- ATHENA: git status -sb; git log --oneline -10; git diff --check; go test -coverprofile=coverage.out ./...; gotestsum --junitfile junit.xml ./...; go test -race ./internal/...; go vet ./...; go build ./cmd/athena
- ashton-mcp-gateway: git status -sb; git log --oneline -10; git diff --check; go test -coverprofile=coverage.out ./...; gotestsum --junitfile junit.xml ./...; go test -race ./internal/...; go vet ./...; go build ./cmd/ashton-mcp-gateway
- Hestia: git status -sb; git diff --check; npm run check; npm test; npm run build; focused Playwright desktop/mobile for competition and proxy boundaries
- Themis: git status -sb; git diff --check; npm run check; npm test; npm run build; focused Playwright for staff competition and trusted-surface if touched
- Prometheus: git status -sb; git diff --check; kustomize build /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps; kustomize build touched observability path
- ashton-platform: git status -sb; git log --oneline -10; git diff --check

Required deploy checks if deploy changed:
- kubectl rollout status for touched deployments
- kubectl get deploy -o yaml for APOLLO/ATHENA/Hestia/Themis/gateway as applicable
- curl or kubectl exec probes for health and required smoke paths
- Prometheus target/scrape confirmation for new telemetry
- rollback plan documented if a runtime deploy occurred

Evidence pack verification:
- Each artifact exists at the required path.
- Each artifact is non-empty and parser-readable where applicable.
- Each artifact name matches its Flow ID or harness ID.
- coverage.out and junit.xml exist for Go repos where tests ran.
- Playwright/check/build reports exist for frontends where tests ran.
- Compatibility matrix exists and has the required columns.
- Telemetry alert/scrape evidence exists before Gate 6 Pass is approved.

Required output format:
1. Status table: Repo | Evidence present | Runtime truth | Deploy truth | Verdict
2. Audit reconciliation: Controller vs Auditor vs Worker claims
3. Commit-ladder assessment
4. Runtime-truth ruling
5. Deploy-truth ruling
6. Gate 6/8/10 movement ruling
7. Residual risks
8. Final verdict: ready for closeout / valid with documented residual risk / not-yet
```

## Closeout Criteria

- Every `must_fix_now` item is resolved with evidence.
- Every cross-repo smoke flow has probe output committed.
- Compatibility matrix is published and matches reality.
- Telemetry counters are exported and one alert rule is committed.
- ATHENA deploy decision is made and documented honestly: rolled forward and proved, or drift documented in `IMPLEMENTATION-BOARD.md`.
- Gate 6 moves from Partial to Pass.
- Gate 10 moves from Partial to Pass.
- Gate 8 ceilings are declared in `launch-expansion-audit.md`.
- Verifier final verdict is `ready for closeout` or `valid with documented residual risk`.
- `ashton-platform` is tagged at the next minor version.
- Repo-local patch tags are created only where actual code or doc changes landed.
