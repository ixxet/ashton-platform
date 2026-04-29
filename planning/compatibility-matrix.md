# ASHTON Milestone 3.0 Compatibility Matrix

Last verified at: 2026-04-29T17:06:16Z

| Repo | Deployed version | Repo HEAD version | Last verified at | Compatible with versions |
| --- | --- | --- | --- | --- |
| APOLLO | `ghcr.io/ixxet/apollo:sha-6b27618@sha256:6c2d4ef8c67d0ea21d3b41963eb9709d0114b53bfea56c30d456cafbb677515a` in `agents/apollo` | `v0.19.2` closeout tag on `6b27618` | 2026-04-29T17:06:16Z | ATHENA `v0.8.2-2-ga19fe02`, ashton-proto `v0.4.0-2-ge49e627`, Hestia `66010ae`, Themis `0231e02`, ashton-mcp-gateway `v0.2.1-1-gdf18570`, Prometheus GitOps `v0.0.4` |
| ATHENA | `ghcr.io/ixxet/athena:v0.8.2@sha256:6524994c2c30bfd227b684b9144b95a798e3e46e600ac5da46df2a600dfd271d` in `athena/athena`; edge proxy `nginx:1.27-alpine`; tunnel `cloudflare/cloudflared:2026.3.0` | `v0.8.2-2-ga19fe02` | 2026-04-29T17:06:16Z | APOLLO `v0.19.2`, ashton-proto `v0.4.0-2-ge49e627`, ashton-mcp-gateway `v0.2.1-1-gdf18570`, Prometheus GitOps `v0.0.4` |
| Hestia | No live Hestia deployment found in the inspected cluster/GitOps app set; repo-only truth for Milestone 3.0 | `66010ae` | 2026-04-29T17:06:16Z | APOLLO public competition contract `apollo_public_competition_v1`; verified by repo tests and focused Playwright, not a cluster deploy |
| Themis | No live Themis deployment found in the inspected cluster/GitOps app set; repo-only truth for Milestone 3.0 | `0231e02` | 2026-04-29T17:06:16Z | APOLLO internal ops contracts at `v0.19.2`; verified by repo tests and focused Playwright, not a cluster deploy |
| ashton-mcp-gateway | No live gateway deployment found in the inspected cluster/GitOps app set; repo-only truth for Milestone 3.0 | `v0.2.1-1-gdf18570` | 2026-04-29T17:06:16Z | ATHENA `v0.8.2`; ashton-proto `v0.4.0-2-ge49e627`; routed-read gateway tests passed locally |
| ashton-platform | Planning/docs repo; no runtime deployment | `v0.1.0` closeout tag on the Milestone 3.0 evidence commit | 2026-04-29T17:06:16Z | Documents APOLLO `v0.19.2`, ATHENA `v0.8.2-2-ga19fe02`, Hestia `66010ae`, Themis `0231e02`, ashton-mcp-gateway `v0.2.1-1-gdf18570`, ashton-proto `v0.4.0-2-ge49e627`, Prometheus GitOps `v0.0.4` |
| ashton-proto | Shared schema repo; no independent deployment | `v0.4.0-2-ge49e627` | 2026-04-29T17:06:16Z | APOLLO `v0.19.2`, ATHENA `v0.8.2-2-ga19fe02`, ashton-mcp-gateway `v0.2.1-1-gdf18570` |
| Prometheus GitOps | Applied cluster state includes APOLLO ServiceMonitor, APOLLO competition PrometheusRule, and APOLLO deployment image `sha-6b27618` | `v0.0.4` closeout tag on `8020388` | 2026-04-29T17:06:16Z | APOLLO `v0.19.2`, ATHENA `v0.8.2-2-ga19fe02`; Prometheus query `up{job="apollo"}` returned `1` |

## Drift Notes

- APOLLO deploy drift is resolved for Milestone 3.0 by rolling the live deployment from `sha-bf3119b` to `sha-6b27618` and verifying health, public readiness, metrics, and Prometheus scrape.
- ATHENA remains deployed at `v0.8.2`; the older edge runbook wording that referenced `v0.7.0` is documented drift, not live truth.
- Hestia, Themis, and ashton-mcp-gateway have no cluster deployment proof in the inspected manifests or live deployment list; their Milestone 3.0 truth is repo-local test truth.
- Prometheus GitOps now carries the APOLLO telemetry scrape and alert examples required for Gate 6.
