# F30 - Graceful Shutdown

Status: repo/local and rollout pass; direct production SIGTERM harness not run.

Evidence:
- APOLLO `go test -race ./internal/...` passed.
- ATHENA `go test -race ./internal/...` passed.
- ashton-mcp-gateway `go test -race ./internal/...` passed.
- APOLLO deployment roll-forward completed successfully and served health/metrics after restart.

Ruling: graceful shutdown is accepted for Milestone 3.0 with residual risk that explicit in-flight production SIGTERM was not run.
