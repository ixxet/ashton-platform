# F18 - Gateway Routed Read Audit

Status: repo/local pass.

Evidence:
- ashton-mcp-gateway `go test -json -coverprofile=coverage.out ./... > gotest.json` passed.
- ashton-mcp-gateway JUnit emitted to `junit.xml`; race/vet/build passed.
- No live gateway deployment was found in the inspected cluster/GitOps app set.

Ruling: routed-read/audit behavior is repo-proved; live gateway deployment proof is deferred.
