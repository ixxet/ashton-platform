# F05 - Rating Dual Run

Status: pass.

Evidence:
- APOLLO `go test -count=1 ./internal/competition ./internal/rating ./internal/ares ./internal/authz` passed.
- APOLLO telemetry exports `rating_update_duration_seconds{policy_version="apollo_legacy_rating_v1"}` and `rating_policy_delta_total{legacy,openskill}`.
- No OpenSkill active read-path switch was introduced in the APOLLO telemetry patch.

Ruling: legacy rating remains active truth; OpenSkill remains comparison/deferred truth.
