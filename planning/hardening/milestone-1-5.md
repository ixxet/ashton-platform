# Milestone 1.5 Hardening

## Audited Claim

Milestone 1.5 claims:

- a bounded live `ATHENA -> NATS -> APOLLO` arrival path is proven in-cluster
- the proven deployment truth is intentionally narrow
- live departure-close and broad APOLLO product deployment remain deferred

## Exact Rerun Commands

```bash
cd /Users/zizo/Personal-Projects/ASHTON/apollo && go test ./db/migrations
cd /Users/zizo/Personal-Projects/ASHTON/apollo && go test -count=3 ./db/migrations
cd /Users/zizo/Personal-Projects/ASHTON/apollo && go build ./cmd/apollo
cd /Users/zizo/Personal-Projects/ASHTON/apollo && docker build -t apollo:milestone15 .

kustomize build /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/athena
kubectl kustomize /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/athena
kustomize build /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/agents
kubectl kustomize /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps

KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig flux reconcile source git flux-system -n flux-system
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig flux reconcile kustomization infra-postgres -n flux-system --with-source
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig flux reconcile kustomization infra-dns -n flux-system --with-source
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig flux reconcile kustomization infra-observability -n flux-system --with-source
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig flux reconcile kustomization apps -n flux-system --with-source
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl rollout status -n athena deployment/athena --timeout=180s
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl rollout status -n agents deployment/apollo --timeout=180s

KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl get deploy -n athena athena -o jsonpath='{.spec.template.spec.containers[0].image}{"\n"}{range .spec.template.spec.containers[0].env[*]}{.name}={.value}{"\n"}{end}'
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl get deploy -n agents apollo -o jsonpath='{.spec.template.spec.containers[0].image}{"\n"}{range .spec.template.spec.containers[0].env[*]}{.name}={.value}{"\n"}{end}'
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl logs -n athena deployment/athena --tail=100
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl logs -n agents deployment/apollo --tail=120

KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl -n athena port-forward svc/athena 18082:80
curl -i --max-time 10 http://127.0.0.1:18082/api/v1/health
curl -i --max-time 10 'http://127.0.0.1:18082/api/v1/presence/count?facility=ashtonbee'
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl -n agents port-forward svc/apollo 18084:80
curl -i --max-time 10 http://127.0.0.1:18084/api/v1/health
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl -n agents port-forward svc/nats 18222:8222
curl -sS http://127.0.0.1:18222/varz
curl -sS http://127.0.0.1:18222/connz

KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl exec -i -n agents postgres-0 -- env PGPASSWORD='mQszyrawMn4/AXTbxTX9LKvPOT8tLT3C' psql -U langgraph -d langgraph < /tmp/milestone15_seed.sql
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl exec -n athena deploy/athena -- /usr/local/bin/athena presence publish-identified --format json
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl exec -n agents deploy/apollo -- /usr/local/bin/apollo visit list --student-id tracer2-student-001 --format json
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl exec -n agents postgres-0 -- env PGPASSWORD='mQszyrawMn4/AXTbxTX9LKvPOT8tLT3C' psql -U langgraph -d langgraph -c "SELECT facility_key, source_event_id, departure_source_event_id, arrived_at, departed_at FROM apollo.visits ORDER BY arrived_at DESC;"
KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig kubectl exec -n agents postgres-0 -- env PGPASSWORD='mQszyrawMn4/AXTbxTX9LKvPOT8tLT3C' psql -U langgraph -d langgraph -c "SELECT count(*) AS workouts FROM apollo.workouts;"
```

## Expected Destructive Failures Vs Real Regressions

Expected boundary behavior during hardening:

- replay of the same live identified arrival resolves as `duplicate`
- the proof remains bounded to arrival; it does not pretend to cover live
  departure-close

Real regressions would have been:

- green manifests with a dead runtime path
- ATHENA publishing live while APOLLO failed to consume or persist
- claiming a broad APOLLO product deployment from an arrival-only proof

## Verified Truth

- manifests rendered cleanly
- live images were pinned and inspectable
- live ATHENA published to in-cluster NATS
- live APOLLO consumed the event and persisted exactly one visit row
- replay remained deterministic
- workouts stayed `0`

## Unverified Truth

- live departure-close behavior remained unproven
- broad APOLLO auth, profile, eligibility, workout, or recommendation product
  deployment remained unproven

## Carry-Forward Gaps

- live departure-close proof remained deferred
- broad APOLLO product deployment remained deferred

## Final Verdict

Milestone 1.5 proves the bounded live arrival path and nothing broader.
