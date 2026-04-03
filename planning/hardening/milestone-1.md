# Milestone 1 Hardening

## Audited Claim

Milestone 1 claims:

- Tracers 0-4 form a release-trustworthy first platform layer
- deployed truth is the live ATHENA read-path slice only
- local truth includes the shared-contract arrival path and APOLLO member-state
  runtime

## Exact Rerun Commands

```bash
docker run --rm -v /Users/zizo/Personal-Projects/ASHTON/ashton-proto:/workspace -w /workspace bufbuild/buf lint
docker run --rm -v /Users/zizo/Personal-Projects/ASHTON/ashton-proto:/workspace -w /workspace bufbuild/buf generate
cd /Users/zizo/Personal-Projects/ASHTON/ashton-proto && go test ./...
cd /Users/zizo/Personal-Projects/ASHTON/ashton-proto && go test -count=5 ./events ./tests/...

cd /Users/zizo/Personal-Projects/ASHTON/athena && go test ./...
cd /Users/zizo/Personal-Projects/ASHTON/athena && go test -count=2 ./...
cd /Users/zizo/Personal-Projects/ASHTON/athena && go test -count=5 ./internal/publish ./cmd/athena
cd /Users/zizo/Personal-Projects/ASHTON/athena && go build ./cmd/athena
cd /Users/zizo/Personal-Projects/ASHTON/athena && docker build -t athena:milestone1 .
cd /Users/zizo/Personal-Projects/ASHTON/athena && ATHENA_ADAPTER='bad' ./athena serve

cd /Users/zizo/Personal-Projects/ASHTON/apollo && go test ./...
cd /Users/zizo/Personal-Projects/ASHTON/apollo && go test -count=2 ./...
cd /Users/zizo/Personal-Projects/ASHTON/apollo && go test -count=5 ./internal/consumer
cd /Users/zizo/Personal-Projects/ASHTON/apollo && go test -count=5 ./internal/server -run '^(TestRegistrationVerificationAndProfileRoundTrip|TestAuthAndProfileEndpointsRejectTokenAndSessionEdgeCases|TestLobbyEligibilityRoundTripFollowsExplicitProfileStateInsteadOfVisitHistory|TestLobbyEligibilityEndpointRejectsMissingTamperedExpiredAndRevokedSessions)$'
cd /Users/zizo/Personal-Projects/ASHTON/apollo && go build ./cmd/apollo
cd /Users/zizo/Personal-Projects/ASHTON/apollo && ./apollo serve
cd /Users/zizo/Personal-Projects/ASHTON/apollo && APOLLO_DATABASE_URL='postgres://postgres:postgres@127.0.0.1:55434/apollo_smoke?sslmode=disable' APOLLO_SESSION_COOKIE_SECRET='short' ./apollo serve

kubectl kustomize /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/athena
kustomize build /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/athena
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl config current-context
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig flux get kustomizations -A
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl get ns athena observability
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl get deploy,pods,svc -n athena -o wide
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl rollout status -n athena deployment/athena --timeout=120s
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl get servicemonitor -n observability athena -o yaml
```

## Expected Destructive Failures Vs Real Regressions

Expected negative or boundary results during hardening:

- invalid ATHENA adapter config fails fast
- short APOLLO session cookie secret fails fast
- unauthenticated and invalid-session APOLLO auth and eligibility cases fail
  clearly
- duplicate arrival replay resolves as `duplicate`

Real regressions would have been:

- claiming the live cluster event boundary without proving it
- claiming a live APOLLO product deployment without proving it
- allowing visit history to imply workouts or lobby intent

## Verified Truth

- repo-local truths aligned across `ashton-proto`, `athena`, `apollo`, and
  `ashton-platform`
- shared-contract arrival behavior was proven locally with real NATS and
  Postgres
- APOLLO auth, profile, and explicit eligibility behavior were proven locally
- live cluster proof covered the narrow ATHENA read-path slice:
  - health
  - occupancy count
  - metrics
  - rollout and live image verification

## Unverified Truth

- live in-cluster `ATHENA -> NATS -> APOLLO` was not part of Milestone 1
- no broad APOLLO cluster deployment was proven

## Carry-Forward Gaps

- live event-boundary deployment proof was deferred and later closed in
  Milestone 1.5
- APOLLO deployment truth remained intentionally deferred at Milestone 1 close

## Final Verdict

Milestone 1 is release-trustworthy on the narrow Option A deployment claim.
