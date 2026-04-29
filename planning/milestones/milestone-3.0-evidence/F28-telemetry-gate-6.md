# F28 - Telemetry Gate 6

Status: pass.

Evidence:
- APOLLO live `/metrics` exports required metrics, including `competition_result_write_attempt_total`, `competition_result_write_reject_total`, `competition_consensus_vote_total{tier="not_active",outcome="deferred"}`, `rating_update_duration_seconds`, `ares_preview_total`, `trusted_surface_failure_total`, booking metrics, tournament metrics, safety/reliability metrics, and `game_identity_projection_duration_seconds`.
- Prometheus ServiceMonitor committed at `infrastructure/observability/apollo-service-monitor.yaml`.
- PrometheusRule committed at `infrastructure/observability/apollo-competition-alerts.yaml`.
- Prometheus query `up{job="apollo"}` returned value `1`.
- Prometheus query for `competition_result_write_attempt_total` returned APOLLO series.

Ruling: Gate 6 moves from Partial to Pass.
