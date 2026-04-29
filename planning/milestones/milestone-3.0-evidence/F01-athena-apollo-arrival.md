# F01 - ATHENA to APOLLO Arrival

Status: repo/local pass; live destructive publish not run.

Evidence:
- ATHENA tests passed with `go test -json -coverprofile=coverage.out ./... > gotest.json`; JUnit emitted to `ATHENA/junit.xml`.
- APOLLO tests passed with `go test -json -coverprofile=coverage.out ./... > gotest.json`; JUnit emitted to `APOLLO/junit.xml`.
- APOLLO live health returned `{"service":"apollo","status":"ok","consumer_enabled":true}`.
- APOLLO live logs showed identified presence consumer enabled for `athena.identified_presence.arrived` and `.departed`.

Ruling: accepted for Milestone 3.0 as consumer/deploy proof with residual risk that a production NATS arrival was not injected.
