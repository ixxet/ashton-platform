# F06 - Public Readiness Redaction

Status: pass.

Evidence:
- Live `GET /api/v1/public/competition/readiness` returned:

```json
{"contract_version":"apollo_public_competition_v1","projection_version":"competition_analytics_v1","status":"unavailable","result_source":"finalized_or_corrected_canonical_results","rating_source":"legacy_elo_like_active_projection","available_leaderboards":0,"available_canonical_results":0,"deferred":["messaging_chat","public_social_graph","rating_read_path_switch","project_wide_semver"]}
```

- No raw user IDs, result IDs, source IDs, watermarks, safety facts, trusted-surface fields, or OpenSkill internals appeared.
- Hestia `npm run check`, `npm test`, `npm run build`, and Playwright desktop/mobile passed.

Ruling: public readiness contract is redacted and source-backed.
