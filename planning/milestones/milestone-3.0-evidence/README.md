# Milestone 3.0 Evidence Pack

Verified at: 2026-04-29T17:06:16Z

This directory intentionally contains one Markdown file. The original per-ID sidecar files were consolidated because they were too small to be useful as separate documents. Flow IDs and harness IDs are preserved below as stable section/table keys.

## Verdict

Milestone 3.0 is valid with documented residual risk. Gate 6 telemetry moved to Pass after APOLLO exported the required counters, Prometheus scraped `up{job="apollo"} == 1`, and APOLLO alert examples were committed. Gate 10 moved to Pass for the inspected deployed/repo composition through the compatibility matrix. Gate 8 ceilings are declared in APOLLO `docs/launch-expansion-audit.md` without claiming full scale validation.

Production-destructive mutation probes were not executed against live APOLLO data. Mutating trust-spine behavior is proved by repo integration tests and focused frontend smoke tests; live cluster proof is limited to deployment, health, route presence, redacted public readiness, telemetry export, and Prometheus scrape.

## Runtime Truth

| Surface | Evidence |
| --- | --- |
| APOLLO deploy | `agents/apollo` image is `ghcr.io/ixxet/apollo:sha-6b27618@sha256:6c2d4ef8c67d0ea21d3b41963eb9709d0114b53bfea56c30d456cafbb677515a`; rollout completed; pod ready 1/1 |
| APOLLO health | `GET /api/v1/health` returned `{"service":"apollo","status":"ok","consumer_enabled":true}` |
| APOLLO public readiness | `GET /api/v1/public/competition/readiness` returned contract `apollo_public_competition_v1`, projection `competition_analytics_v1`, status `unavailable`, and no raw IDs |
| APOLLO metrics | `GET /metrics` returned required competition/rating/ARES/booking/safety/reliability/game-identity metric names |
| Prometheus scrape | Prometheus query `up{job="apollo"}` returned a vector value of `1` for pod `apollo-5959ff8bc7-5p2j7` |
| ATHENA deploy | `athena/athena` image is `ghcr.io/ixxet/athena:v0.8.2@sha256:6524994c2c30bfd227b684b9144b95a798e3e46e600ac5da46df2a600dfd271d` |
| ATHENA health/metrics | Health returned `{"service":"athena","status":"ok","adapter":"edge-projection"}`; `athena_current_occupancy 82`; `presence/count?facility=ashtonbee` returned count 82 |
| Hestia/Themis/gateway deploy | No live deployment found in inspected cluster/GitOps app set; repo-local truth only |

## Verification Commands

| Repo | Commands | Outcome |
| --- | --- | --- |
| APOLLO | `sqlc generate -f db/sqlc.yaml`; `git diff --check`; `go test -json -coverprofile=coverage.out ./... > gotest.json`; JUnit XML generated at `junit.xml`; `go test -race ./internal/...`; `go test -count=1 ./internal/competition ./internal/rating ./internal/ares ./internal/authz`; `go test -count=1 ./internal/server -run 'TestCompetition'`; `go vet ./...`; `go build ./cmd/apollo` | Passed; coverage total 40.9% |
| ATHENA | `git diff --check`; `go test -json -coverprofile=coverage.out ./... > gotest.json`; JUnit XML generated at `junit.xml`; `go test -race ./internal/...`; `go vet ./...`; `go build ./cmd/athena` | Passed; coverage total 67.4% |
| ashton-mcp-gateway | `git diff --check`; `go test -json -coverprofile=coverage.out ./... > gotest.json`; JUnit XML generated at `junit.xml`; `go test -race ./internal/...`; `go vet ./...`; `go build ./cmd/ashton-mcp-gateway` | Passed; coverage total 68.9% |
| Hestia | `git diff --check`; `npm run check`; `npm test`; `npm run build`; `npx playwright test --project=desktop --project=mobile` | Passed; 40 Playwright tests passed |
| Themis | `git diff --check`; `npm run check`; `npm test`; `npm run build`; full Playwright desktop/mobile; focused Milestone 3.0 Playwright subset | Check/test/build passed; focused subset passed 12 tests; full suite has existing dated fixture/test-selector debt outside Milestone 3.0 |
| Prometheus GitOps | `git diff --check`; `kustomize build infrastructure/observability`; `kustomize build apps/agents/apollo` | Passed |
| ashton-platform | `git diff --check` | Passed before commit |

## Flow Evidence

| ID | Flow | Evidence | Ruling |
| --- | --- | --- | --- |
| F01 | ATHENA to APOLLO arrival | ATHENA/APOLLO tests passed; APOLLO live health returned consumer enabled; APOLLO logs showed identified presence subjects enabled. | Repo/deploy proof accepted; no live NATS arrival injected. |
| F02 | ATHENA arrival replay | APOLLO/ATHENA coverage, JUnit, and race suites passed; APOLLO consumer enabled live. | Idempotency repo-proved; no live replay injected. |
| F03 | Result finalize public projection | APOLLO competition/server targeted tests passed; Hestia/Themis smoke passed; live readiness returned versioned unavailable contract. | Source-truth behavior repo-proved; no production result mutation. |
| F04 | Result correction canonical view | APOLLO competition/server targeted tests passed; APOLLO race passed; Themis lifecycle subset passed. | Correction behavior repo-proved; no live destructive correction. |
| F05 | Rating dual run | APOLLO rating/competition/ARES/authz tests passed; rating metrics exported; no OpenSkill read-path switch introduced. | Legacy remains active; OpenSkill remains comparison/deferred. |
| F06 | Public readiness redaction | Live readiness returned `apollo_public_competition_v1` and no raw IDs/source IDs/watermarks/safety/trusted-surface/OpenSkill internals. | Public readiness is redacted and source-backed. |
| F07 | Public leaderboards redaction | APOLLO competition/server tests passed; Hestia Playwright passed; live readiness reported zero available leaderboards. | Redaction repo/browser-proved; non-empty live rows unavailable. |
| F08 | Public game identity redaction | APOLLO game-identity tests included in passing runs; `game_identity_projection_duration_seconds` exported; Hestia Playwright passed. | Public game identity remains redacted; non-empty live rows unavailable. |
| F09 | Member self scope | APOLLO authz/competition targeted tests passed; Hestia private/member route and proxy tests passed. | Member reads remain self-scoped in tested contracts. |
| F10 | Command readiness | APOLLO server competition tests passed; Themis supervisor readiness smoke passed. | Command readiness remains APOLLO-owned. |
| F11 | Command dry-run | APOLLO competition/server tests passed; Themis manager dry-run smoke passed. | Dry-run remains non-mutating in tested paths. |
| F12 | Command expected-version guards | APOLLO competition/server tests and race passed. | Expected-version behavior repo-proved; no live stale mutation sent. |
| F13 | Themis trusted-surface roles | Themis check/test/build passed; focused Playwright passed for manager safety, supervisor denials, and trusted-surface flows; APOLLO authz tests passed. | Staff routes require role and server-side trust in tested paths. |
| F14 | Themis proxy header strip | Themis focused proxy/trusted-surface subset passed; check/test/build passed. | Browser-supplied trusted-surface headers are not accepted as APOLLO trust in tested paths. |
| F15 | Hestia public proxy denial | Hestia Playwright desktop/mobile and check/test/build passed. | Hestia does not expose a browser APOLLO public proxy tunnel in tested paths. |
| F16 | Hestia unavailable state | Live readiness returned `status: "unavailable"`; Hestia Playwright passed unavailable behavior. | Unavailable state is explicit; no fake records. |
| F17 | Hestia public contract only | Hestia server/public competition tests and Playwright passed; no Hestia changes made. | Hestia remains a public-contract consumer, not truth owner. |
| F18 | Gateway routed read audit | ashton-mcp-gateway coverage/JUnit/race/vet/build passed. | Routed-read/audit behavior repo-proved; no live gateway deploy found. |
| F19 | Gateway manifest escape | Gateway test/race/vet/build passed; manifest containment covered by suite. | Manifest escape handling repo-proved. |
| F20 | Cross-version compatibility | Compatibility matrix published; deployed and HEAD versions recorded; Hestia/Themis/gateway marked repo-only. | Gate 10 documented without hidden deploy claims. |
| F21 | Tournament final result bound | APOLLO competition/server tests passed; Themis competition ops subset passed. | Tournament guard behavior repo-proved; no live advancement mutation. |
| F22 | Safety trusted surface | APOLLO authz tests passed; Themis safety/denial subset passed; safety/trust metrics exported. | Safety review remains staff/trusted-surface gated. |
| F23 | Reliability private attribution | APOLLO competition/authz/server tests passed; Hestia public smoke passed; live readiness exposed no reliability details. | Reliability details remain internal; no live reliability mutation. |
| F24 | ARES preview no mutation | APOLLO `./internal/ares` tests passed; ARES metrics exported. | ARES remains preview/proposal truth, not result/rating truth. |
| F25 | Analytics deterministic internal | APOLLO competition/server tests passed; live readiness exposed no analytics internals. | Analytics remain APOLLO-owned internal projection truth. |
| F26 | APOLLO deploy truth | Live image moved from `sha-bf3119b` to `sha-6b27618`; rollout passed; health/readiness/metrics passed. | APOLLO 3B deploy drift resolved for Milestone 3.0. |
| F27 | ATHENA deploy truth | Live image is `v0.8.2`; proxy/tunnel images recorded; health, metrics, and presence count passed. | ATHENA live truth is v0.8.2; older v0.7.0 runbook wording is drift. |
| F28 | Telemetry Gate 6 | APOLLO `/metrics` exports required counters; ServiceMonitor and PrometheusRule committed; `up{job="apollo"} == 1`. | Gate 6 moves from Partial to Pass. |
| F29 | Public leak corpus | Live readiness contained no forbidden fields; Hestia Playwright and Themis focused proxy/trust subset passed. | Leak corpus passed for available surfaces; non-empty live leaderboard/identity rows unavailable. |
| F30 | Graceful shutdown | APOLLO/ATHENA/gateway race runs passed; APOLLO rollout completed and served health/metrics after restart. | Accepted with residual risk: no explicit in-flight production SIGTERM. |

## APOLLO Harness Evidence

| ID | Scenario | Evidence | Ruling |
| --- | --- | --- | --- |
| A01 | Oversized mutating JSON | APOLLO full, targeted server/competition, race, vet, and build passed. | Repo/local covered; live destructive payload not sent. |
| A02 | Malformed JSON/unknown fields | APOLLO full, targeted server/competition, race, vet, and build passed. | Repo/local covered; live destructive payload not sent. |
| A03 | SIGTERM during HTTP/NATS | APOLLO race passed; deployment roll-forward completed; health/metrics served afterward. | Rollout proof plus repo race; no direct in-flight production SIGTERM. |
| A04 | Duplicate arrival replay | APOLLO/ATHENA tests passed; APOLLO consumer live-enabled. | Repo/local covered; live replay not injected. |
| A05 | Duplicate departure replay | APOLLO consumer subjects include departure; APOLLO/ATHENA tests passed. | Repo/local covered where active; live replay not injected. |
| A06 | Stale result version | APOLLO targeted competition/server tests and race passed. | Repo/local pass; live stale mutation not sent. |
| A07 | Stale tournament version | APOLLO targeted tests passed; Themis competition subset passed. | Repo/local pass; live stale mutation not sent. |
| A08 | Safety missing trusted-surface | APOLLO authz tests passed; Themis safety boundary subset passed; `trusted_surface_failure_total` exported. | Pass. |
| A09 | Member on staff route | APOLLO authz tests passed; Themis role denial subset passed. | Pass. |
| A10 | Supervisor on safety review | APOLLO authz tests passed; Themis supervisor safety denial passed. | Pass. |
| A11 | Manager without trusted-surface | APOLLO authz tests passed; Themis trusted-surface enforcement passed. | Pass. |
| A12 | Giant workout payload | APOLLO full test, race, vet, and build passed. | Repo/local covered; live destructive workout write not sent. |
| A13 | Malformed arrival proto | APOLLO/ATHENA tests passed; APOLLO consumer live-enabled. | Repo/local covered; live malformed NATS payload not sent. |
| A14 | Concurrent finalize | APOLLO targeted competition/server tests and race passed. | Repo/local pass; live destructive concurrency not run. |
| A15 | Public output leak corpus | Live readiness had no forbidden public leak fields; Hestia Playwright and Themis focused subset passed. | Pass for available live surfaces and repo/browser smoke. |

## ATHENA Harness Evidence

| ID | Scenario | Evidence | Ruling |
| --- | --- | --- | --- |
| T01 | Bad node token | ATHENA full tests, race, vet, and build passed. | Repo/local pass; live bad-token publish not sent. |
| T02 | Malformed tap payload | ATHENA full tests, race, vet, and build passed. | Repo/local pass; live malformed edge tap not sent. |
| T03 | Repeated/stale observations | ATHENA full tests passed; live presence count available at 82 during verification. | Repo/local pass. |
| T04 | History append failure | ATHENA full tests, race, vet, and build passed. | Repo/local pass. |
| T05 | Publisher transient failure | ATHENA full tests, race, vet, and build passed. | Repo/local pass. |
| T06 | Restart history replay | ATHENA full tests passed; live health and metrics stayed healthy. | Repo/local pass. |
| T07 | SIGTERM during publish | ATHENA race, vet, and build passed. | Repo/local pass; direct live SIGTERM not run. |

## Gateway Harness Evidence

| ID | Scenario | Evidence | Ruling |
| --- | --- | --- | --- |
| G01 | Bad caller token | Gateway full tests, race, vet, and build passed. | Repo/local pass; no live gateway deployment found. |
| G02 | Undeclared argument | Gateway full tests, race, vet, and build passed. | Repo/local pass. |
| G03 | Wrong-type optional argument | Gateway full tests, race, vet, and build passed. | Repo/local pass. |
| G04 | Audit persistence failure | Gateway full tests, race, vet, and build passed. | Repo/local pass. |
| G05 | Manifest path/symlink escape | Gateway manifest tests passed inside full test/race/vet/build runs. | Repo/local pass. |

## Hestia Harness Evidence

| ID | Scenario | Evidence | Ruling |
| --- | --- | --- | --- |
| H01 | Public competition unavailable | Hestia Playwright desktop/mobile passed; live APOLLO readiness returned unavailable without fake records. | Pass. |
| H02 | Member proxy blocks public | Hestia Playwright desktop/mobile passed for proxy boundaries. | Pass. |
| H03 | Private route denial | Hestia check/test/build and Playwright passed. | Pass. |
| H04 | No trusted-surface browser | Hestia Playwright and server/browser tests passed; no Hestia code changes made. | Pass. |

## Themis Harness Evidence

| ID | Scenario | Evidence | Ruling |
| --- | --- | --- | --- |
| M01 | Supervisor command read | Themis focused desktop/mobile subset passed for supervisor competition readiness. | Pass. |
| M02 | Manager safety trusted | Themis focused desktop/mobile subset passed for manager safety trusted-surface behavior. | Pass. |
| M03 | Public proxy denial | Themis focused desktop/mobile subset passed for APOLLO public proxy denial. | Pass. |
| M04 | Manager competition E2E | Themis focused desktop/mobile subset passed for dry-run and result lifecycle; full suite residual is unrelated fixture/test-selector debt. | Pass with documented residual. |

## Residual Risks

- Live production APOLLO was not seeded with destructive result/tournament/safety mutations. The mutation contract is covered by repo tests and focused UI smoke, not by production DB writes.
- Hestia, Themis, and ashton-mcp-gateway have no cluster deployment proof in the inspected environment.
- Themis full Playwright suite still has unrelated dated fixture/test-selector failures: duplicate text strict locator on the command denial test and a schedule-block cancel expectation mismatch.
- ATHENA edge runbook wording that referenced `v0.7.0` remains documented drift; live image is `v0.8.2`.

## External Evidence Artifacts

- APOLLO, ATHENA, and ashton-mcp-gateway retain generated Go artifacts in repo roots: `coverage.out`, `gotest.json`, and `junit.xml`.
- Frontend verification used `npm` and Playwright console outputs; Hestia full Playwright passed and Themis focused Milestone 3.0 Playwright passed.
- Compatibility truth is published at `planning/compatibility-matrix.md`.
