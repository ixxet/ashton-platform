# Milestone 1.6 Hardening

## Audited Claim

Milestone 1.6 claims:

- a bounded live `ATHENA -> NATS -> APOLLO` departure-close path is proven
  in-cluster
- replay of the same live departure event remains idempotent
- the proven deployment truth is intentionally narrow
- live source-backed ATHENA ingress deployment remained deferred in Milestone
  1.6 itself and only closed later as a separate ATHENA edge deployment
  workstream
- broad APOLLO product deployment remains deferred

## Exact Rerun Commands

```bash
export KUBECONFIG=/Users/zizo/Personal-Projects/Computers/Talos/tower-bootstrap/kubeconfig
kubectl config current-context
kubectl cluster-info
flux check

git -C /Users/zizo/Personal-Projects/Computers/Prometheus status --short
git -C /Users/zizo/Personal-Projects/Computers/Prometheus branch --show-current
git -C /Users/zizo/Personal-Projects/Computers/Prometheus rev-parse HEAD
git -C /Users/zizo/Personal-Projects/ASHTON/ashton-platform status --short
git -C /Users/zizo/Personal-Projects/ASHTON/ashton-platform branch --show-current
git -C /Users/zizo/Personal-Projects/ASHTON/ashton-platform rev-parse HEAD

kustomize build /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/athena >/tmp/m16-athena-render.yaml
kubectl kustomize /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/athena >/tmp/m16-athena-kubectl-render.yaml
kustomize build /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/agents >/tmp/m16-agents-render.yaml
kubectl kustomize /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps >/tmp/m16-apps-kubectl-render.yaml
docker buildx imagetools inspect ghcr.io/ixxet/athena:0.4.0

flux reconcile source git flux-system -n flux-system
flux reconcile kustomization infra-postgres -n flux-system --with-source
flux reconcile kustomization apps -n flux-system --with-source
flux get kustomizations -A
kubectl rollout status -n athena deployment/athena --timeout=300s
kubectl rollout status -n agents deployment/apollo --timeout=180s
kubectl rollout status -n agents deployment/nats --timeout=180s
kubectl get deploy,pods -n athena
kubectl get deploy,pods,svc -n agents
kubectl get deploy -n athena athena -o jsonpath='{.spec.template.spec.containers[0].image}{"\n"}{range .spec.template.spec.containers[0].env[*]}{.name}={.value}{"\n"}{end}'
kubectl get deploy -n agents apollo -o yaml | rg 'APOLLO_DATABASE_URL|APOLLO_SESSION_COOKIE_SECRET|secretKeyRef|name: apollo-runtime'

kubectl exec -n agents postgres-0 -- psql -U langgraph -d langgraph -c "SELECT current_user, current_database();"
kubectl exec -n agents postgres-0 -- psql -U langgraph -d langgraph -c "SELECT v.id, u.student_id, v.facility_key, v.source_event_id, v.departure_source_event_id, v.arrived_at, v.departed_at FROM apollo.visits v JOIN apollo.users u ON u.id=v.user_id WHERE u.student_id='tracer2-student-001' ORDER BY v.arrived_at DESC; SELECT u.student_id, u.preferences::text AS preferences, count(ct.*) FILTER (WHERE ct.is_active) AS active_claimed_tags FROM apollo.users u LEFT JOIN apollo.claimed_tags ct ON ct.user_id=u.id WHERE u.student_id='tracer2-student-001' GROUP BY u.student_id, u.preferences; SELECT count(*) AS workouts FROM apollo.workouts;"

kubectl exec -n athena deploy/athena -- /usr/local/bin/athena presence --help
kubectl exec -n athena deploy/athena -- /usr/local/bin/athena presence publish-identified-departures --format json
kubectl logs -n athena deployment/athena --since=5m | rg 'identified presence published|mock-out-001|mock-in-001'
kubectl logs -n agents deployment/apollo --since=5m | rg 'identified departure handled|identified departure visit close failed|mock-out-001|identified presence handled|consumer enabled'

kubectl -n agents port-forward svc/nats 18222:8222
curl -sS http://127.0.0.1:18222/varz
curl -sS 'http://127.0.0.1:18222/connz?subs=0'

kubectl exec -n athena deploy/athena -- /usr/local/bin/athena presence publish-identified-departures --format json
kubectl exec -n athena deploy/athena -- /usr/local/bin/athena presence publish-identified-departures --format json

kubectl -n athena port-forward svc/athena 18082:80
curl -sS -i http://127.0.0.1:18082/api/v1/health
curl -sS -i 'http://127.0.0.1:18082/api/v1/presence/count?facility=ashtonbee'

kubectl -n agents port-forward svc/apollo 18084:80
curl -sS -i http://127.0.0.1:18084/api/v1/health
```

## Expected Destructive Failures Vs Real Regressions

Expected boundary behavior during hardening:

- replay of the same live identified departure resolves as `duplicate`
- the proof remains bounded to departure-close and does not pretend to prove
  live source-backed ingress deployment

Real regressions would have been:

- ATHENA publishing live while APOLLO failed to consume or close the visit
- the wrong visit closing
- replay mutating the visit again
- `apollo.workouts` or sampled unrelated member state changing
- claiming a broad APOLLO product deployment from a bounded visit-close proof

## Verified Truth

- Flux was healthy and reconciled to `main@sha1:70c92a6b`
- live ATHENA was pinned to
  `ghcr.io/ixxet/athena:0.4.0@sha256:8fcf9b9cff28a3c417771d350cfb9d02ecb865507aa48f7c3ac9cc7d4b7cdc19`
- live ATHENA carried explicit identified-exit config through
  `ATHENA_MOCK_IDENTIFIED_EXIT_TAG_HASHES=tag_tracer2_001`
- live APOLLO health stayed green with `consumer_enabled=true`
- live ATHENA published `mock-out-001` on
  `athena.identified_presence.departed`
- live APOLLO closed exactly one matching open visit for
  `tracer2-student-001`
- replay of `mock-out-001` stayed deterministic as `duplicate`
- `apollo.workouts` stayed `0`
- sampled unrelated member state (`preferences`, active claimed tags) stayed
  unchanged

## Unverified Truth In Milestone 1.6 Itself

- live source-backed ATHENA ingress deployment remained unproven in Milestone
  1.6 itself
- broad APOLLO auth, profile, eligibility, workout, recommendation, or UI
  deployment remained unproven
- no claim was proven beyond the bounded live departure-close path

## Carry-Forward Gaps

- the cluster still runs mock-backed ATHENA ingress
- live source-backed ATHENA ingress deployment later closed as a separate
  ATHENA edge deployment workstream
- broad APOLLO product deployment remains a separate workstream

## Final Verdict

Milestone 1.6 proves the bounded live departure-close path and nothing
broader.
