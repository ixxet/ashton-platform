# F27 - ATHENA Deploy Truth

Status: pass with documented drift.

Evidence:
- Live ATHENA image is `ghcr.io/ixxet/athena:v0.8.2@sha256:6524994c2c30bfd227b684b9144b95a798e3e46e600ac5da46df2a600dfd271d`.
- Edge proxy image is `nginx:1.27-alpine`; edge tunnel image is `cloudflare/cloudflared:2026.3.0`.
- Health returned `{"service":"athena","status":"ok","adapter":"edge-projection"}`.
- `/metrics` included `athena_current_occupancy 82`.
- `GET /api/v1/presence/count?facility=ashtonbee` returned current count 82.

Ruling: ATHENA live truth is v0.8.2; older runbook v0.7.0 wording is documented drift.
