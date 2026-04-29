# A03 - SIGTERM During HTTP/NATS

Status: rollout proof plus repo race pass; direct in-flight production SIGTERM not run.

Evidence: APOLLO `go test -race ./internal/...` passed. APOLLO deployment rolled from `sha-bf3119b` to `sha-6b27618`; rollout completed and health/metrics served afterward.
