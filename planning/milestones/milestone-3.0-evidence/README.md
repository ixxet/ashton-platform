# Milestone 3.0 Evidence Pack

Verified at: 2026-04-29T17:06:16Z

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

## Evidence Artifact Map

- F01-F30 files in this directory are the per-flow smoke ledger.
- A01-A15, T01-T07, G01-G05, H01-H04, and M01-M04 files in this directory are the destructive harness ledgers by scenario ID.
- Go artifacts are left in their repo roots as `coverage.out`, `gotest.json`, and `junit.xml` for APOLLO, ATHENA, and ashton-mcp-gateway.
- Frontend verification used console outputs from `npm` and Playwright; Themis full-suite failures are recorded as residual fixture/test-selector debt and isolated by a passing focused Milestone 3.0 subset.

## Residual Risks

- Live production APOLLO was not seeded with destructive result/tournament/safety mutations. The mutation contract is covered by repo tests and focused UI smoke, not by production DB writes.
- Hestia, Themis, and ashton-mcp-gateway have no cluster deployment proof in the inspected environment.
- Themis full Playwright suite still has unrelated dated fixture/test-selector failures: duplicate text strict locator on the command denial test and a schedule-block cancel expectation mismatch.
- ATHENA edge runbook wording that referenced `v0.7.0` remains documented drift; live image is `v0.8.2`.
