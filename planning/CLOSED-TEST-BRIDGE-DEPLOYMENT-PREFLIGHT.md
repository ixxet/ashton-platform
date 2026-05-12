# Closed-Test Bridge Deployment Preflight

Status: read-only preflight captured on 2026-05-12.

This packet prepares APOLLO/ATHENA operational presence bridge deploy readiness for a later closed-test deploy and smoke packet. It does not deploy the bridge, create or update secrets, mutate GitOps, run destructive probes, create fixtures, restart pods, kill pods, write databases, or expose a public product surface.

## Verdict

Closed-Test Bridge Deploy + Smoke is not approved yet.

The read-only preflight cleared local deploy inventory for the candidate Talos target, but live cluster capture is blocked because the current Kubernetes API endpoint did not respond during read-only checks. The next packet cannot proceed until an operator confirms the target, live access works, required bridge env/secret wiring is approved, and rollback ownership is named.

## Current Truth Boundaries

- Repo/runtime truth: ATHENA has an internal-token-gated `GET /api/v1/presence/ingress-bridge`, and APOLLO can consume it with `apollo presence athena-gate --athena-url`.
- GitOps inventory truth: local Prometheus inventory contains candidate APOLLO/ATHENA manifests and the public ATHENA edge proxy configuration.
- Live read-only truth: current kube context is known, but live APOLLO/ATHENA resources, images, health, metrics, and logs were not readable because the API server timed out.
- Deployed truth: unchanged by this packet. No closed-test bridge deploy or smoke was run.
- Public product truth: unchanged. No frontend, public tournament, OpenSkill read path, XP, teams, reliability scoring, messaging, social graph, or booking/commercial path was opened.

## Gate 0 Findings

### Repository State

- Platform repo: `main...origin/main`, with pre-existing unrelated dirty files outside this packet.
- APOLLO repo: `main...origin/main`, with pre-existing unrelated dirty docs and generated test artifacts.
- ATHENA repo: `main...origin/main`, with pre-existing generated test artifacts.
- Hestia repo: clean.
- Themis repo: clean.

Only this preflight artifact is in Packet scope for the platform commit.

### Candidate Target Environment

- Current kube context: `admin@talos-tower`.
- Minified kube cluster/context: `talos-tower`.
- Candidate GitOps cluster path: `/Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/clusters/talos-tower`.
- Candidate APOLLO namespace from inventory: `agents`.
- Candidate ATHENA namespace from inventory: `athena`.
- Candidate observability namespace from inventory: `observability`.

This is candidate/inventory truth only. It is not approved execution truth until the operator explicitly confirms `admin@talos-tower` / `talos-tower` as the closed-test target.

### GitOps Inventory Status

Read-only inventory paths were present:

- `/Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops`
- `/Users/zizo/Personal-Projects/Computers/Talos/homelab-gitops`
- `/Users/zizo/Personal-Projects/Computers/ops`
- `/Users/zizo/Personal-Projects/Computers/ops/talos`

These paths were not Git repositories in this workspace, so clean/dirty Git status is not applicable. No inventory files were edited.

### Candidate Images From Local Inventory

APOLLO candidate manifest image:

```text
ghcr.io/ixxet/apollo:sha-6b27618@sha256:6c2d4ef8c67d0ea21d3b41963eb9709d0114b53bfea56c30d456cafbb677515a
```

ATHENA candidate manifest image:

```text
ghcr.io/ixxet/athena:v0.8.2@sha256:6524994c2c30bfd227b684b9144b95a798e3e46e600ac5da46df2a600dfd271d
```

Live APOLLO and ATHENA images were not captured because the Kubernetes API server timed out.

### Candidate Services And Deployments

From local inventory only:

- APOLLO deployment: `apollo`
- APOLLO service: `apollo.agents.svc.cluster.local`
- ATHENA deployment: `athena`
- ATHENA service: `athena.athena.svc.cluster.local`
- ATHENA edge proxy service: `athena-edge-proxy.athena.svc.cluster.local`

Expected APOLLO-to-ATHENA internal base URL:

```text
http://athena.athena.svc.cluster.local
```

APOLLO must call the ATHENA internal service, not the public ATHENA edge proxy.

### Public Edge Proxy Exposure

Local GitOps proxy configuration exposes:

- `POST, OPTIONS /api/v1/edge/tap`
- `/api/v1/health`
- default `404` for all other paths

The local proxy configuration does not expose `/api/v1/presence/ingress-bridge`.

Live public edge behavior was not fully confirmed:

- `GET https://tap.lintellabs.net/api/v1/health` returned HTTP `530`.
- `GET https://tap.lintellabs.net/api/v1/presence/ingress-bridge?facility=preflight` timed out with no HTTP response.

This means the local inventory is safe-shaped, but live public edge confirmation remains blocked until the public endpoint is healthy enough to read.

### Live Cluster Access

Read-only live Kubernetes preflight was attempted and blocked:

```text
kubectl --request-timeout=5s get --raw=/readyz
```

Result:

```text
Unable to connect to the server: request canceled while waiting for connection
```

Earlier read-only `kubectl get` resource checks for APOLLO/ATHENA deployments, services, and pods also timed out against the API server. No mutating command was run.

Unavailable live evidence:

- APOLLO live deployment image.
- ATHENA live deployment image.
- APOLLO live service/pod readiness.
- ATHENA live service/pod readiness.
- APOLLO health.
- ATHENA health.
- APOLLO metrics.
- ATHENA metrics.
- Bounded recent APOLLO logs.
- Bounded recent ATHENA logs.

## Bridge Env And Secret Wiring Plan

This is a ready-to-apply plan for a later approved deploy packet. Do not apply it in this preflight packet.

### ATHENA Required Env

ATHENA must receive:

```text
ATHENA_INTERNAL_READ_TOKEN
```

Recommended wiring:

- Store the value in a SOPS-managed Kubernetes Secret, either as a dedicated internal bridge read secret or as an explicitly approved key in the existing ATHENA runtime secret.
- Reference the secret key from the ATHENA deployment env.
- Do not print the token in logs, docs, shell output, Git commit messages, or evidence.
- The token must be non-empty in the closed-test environment before the bridge read can return `200`.

### APOLLO Required Env

APOLLO must receive:

```text
APOLLO_ATHENA_INTERNAL_READ_TOKEN
APOLLO_ATHENA_BASE_URL
```

Recommended wiring:

- Store `APOLLO_ATHENA_INTERNAL_READ_TOKEN` in a SOPS-managed Kubernetes Secret.
- Set `APOLLO_ATHENA_BASE_URL` to `http://athena.athena.svc.cluster.local`.
- Confirm the APOLLO token value matches ATHENA's `ATHENA_INTERNAL_READ_TOKEN`.
- Do not route APOLLO through the public ATHENA edge proxy.

### Missing Wiring In Current Local Inventory

The inspected APOLLO manifest does not include:

- `APOLLO_ATHENA_INTERNAL_READ_TOKEN`
- `APOLLO_ATHENA_BASE_URL`

The inspected ATHENA manifest does not include:

- `ATHENA_INTERNAL_READ_TOKEN`

Closed-test deploy remains blocked until these are approved and applied by an operator in a future packet.

## Blocker-By-Blocker Readiness Plan

| Blocker | Current state | Required before deploy/smoke |
| --- | --- | --- |
| Operator approval | Not present in this packet | Explicit approval text naming target, scope, commands, and rollback owner |
| Target environment | Candidate `admin@talos-tower` / `talos-tower` only | Operator confirms this is the closed-test target |
| Kubernetes API access | Timed out on read-only checks | `kubectl --request-timeout=5s get --raw=/readyz` succeeds |
| Live image capture | Blocked | Capture live APOLLO/ATHENA images and compare to intended deploy inputs |
| Live health capture | Blocked | Read APOLLO and ATHENA `/api/v1/health` without mutation |
| Live metrics access | Blocked | Read APOLLO and ATHENA metrics endpoints or Prometheus scrape targets without dumping sensitive labels |
| Bounded logs access | Blocked | Read recent APOLLO/ATHENA logs with bounded tails and no sensitive payload dumps |
| Bridge token secret | Missing from local inventory | SOPS-managed secret key is created or updated in approved deploy packet |
| APOLLO base URL | Missing from local inventory | `APOLLO_ATHENA_BASE_URL=http://athena.athena.svc.cluster.local` is applied |
| Public bridge exposure | Local proxy config is safe-shaped; live public check inconclusive | Confirm public `/api/v1/presence/ingress-bridge` is not exposed |
| Rollback owner | Not named | Owner accepts responsibility for GitOps revert and live verification |
| Rollback path | Not confirmed | Exact revert path for env/secret/deploy change is documented |
| Closed-test fixture/report data | Not confirmed | Canary facility/time window selected, or smoke expects an empty redacted report |

## Fixture And Data Requirements

The closed-test bridge smoke should avoid real member/customer/facility/account identity exposure.

Before valid-token bridge smoke, choose one:

- A disposable canary facility/node/account fixture with bounded time window.
- A confirmed empty canary facility/time window where an empty redacted report is the expected result.

Do not create fixtures in this packet.

The smoke output must not commit or print:

- raw account IDs
- member names
- customer names
- tokens
- unsafe identity hashes
- raw production log lines containing PII

## Rollback And Backup Questions

These must be answered before the next packet can apply deploy changes:

1. Who is the named operator for the closed-test bridge deploy?
2. Who is the named rollback owner?
3. Which GitOps inventory path is authoritative for the deployment?
4. Which SOPS secret files will hold the ATHENA and APOLLO token keys?
5. Is the rollback path a Git revert, manual manifest revert, or image/env rollback?
6. What exact command sequence will verify rollback after revert?
7. Are database backup/restore paths needed for this non-destructive bridge smoke, or only for later destructive probes?
8. If valid-token smoke reads existing bridge reports, what fixture or empty canary window prevents private truth exposure?

Destructive probes remain separately blocked until DB backup/restore owner and path are documented.

## Read-Only Command Checklist

These commands are safe for preflight when run with redaction discipline. They do not apply config or write cluster state.

### Repo Status

```sh
git -C /Users/zizo/Personal-Projects/ASHTON/ashton-platform status --branch --short
git -C /Users/zizo/Personal-Projects/ASHTON/apollo status --branch --short
git -C /Users/zizo/Personal-Projects/ASHTON/athena status --branch --short
git -C /Users/zizo/Personal-Projects/ASHTON/hestia status --branch --short
git -C /Users/zizo/Personal-Projects/ASHTON/themis status --branch --short
```

### GitOps Inventory

```sh
rg -n "image:|ATHENA_INTERNAL_READ_TOKEN|APOLLO_ATHENA_INTERNAL_READ_TOKEN|APOLLO_ATHENA_BASE_URL|ingress-bridge|edge/tap|api/v1/health" \
  /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/athena \
  /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps/agents/apollo
```

Do not print decrypted secret values.

### Kubernetes Context And Readiness

```sh
kubectl config current-context
kubectl config view --minify -o jsonpath='{.contexts[0].context.cluster}{"\n"}'
kubectl --request-timeout=5s get --raw=/readyz
```

### Live Resources

Run only after `/readyz` succeeds:

```sh
kubectl --request-timeout=5s -n agents get deploy apollo -o wide
kubectl --request-timeout=5s -n athena get deploy athena -o wide
kubectl --request-timeout=5s -n agents get svc apollo -o wide
kubectl --request-timeout=5s -n athena get svc athena athena-edge-proxy -o wide
kubectl --request-timeout=5s -n agents get pods -l app.kubernetes.io/name=apollo -o wide
kubectl --request-timeout=5s -n athena get pods -l app.kubernetes.io/name=athena -o wide
```

### Live Image Capture

Run only after the API is reachable:

```sh
kubectl --request-timeout=5s -n agents get deploy apollo -o jsonpath='{.spec.template.spec.containers[*].image}{"\n"}'
kubectl --request-timeout=5s -n athena get deploy athena -o jsonpath='{.spec.template.spec.containers[*].image}{"\n"}'
```

### Health, Metrics, And Logs

Run only after services and pods are readable:

```sh
kubectl --request-timeout=5s get --raw='/api/v1/namespaces/agents/services/http:apollo:80/proxy/api/v1/health'
kubectl --request-timeout=5s get --raw='/api/v1/namespaces/athena/services/http:athena:80/proxy/api/v1/health'
kubectl --request-timeout=5s get --raw='/api/v1/namespaces/agents/services/http:apollo:80/proxy/metrics'
kubectl --request-timeout=5s get --raw='/api/v1/namespaces/athena/services/http:athena:80/proxy/metrics'
kubectl --request-timeout=5s -n agents logs deploy/apollo --tail=80
kubectl --request-timeout=5s -n athena logs deploy/athena --tail=80
```

Evidence should record access success and relevant metric names only. Do not commit raw logs if they contain private data.

## Future-Only Closed-Test Smoke Sequence

Do not run these commands in this packet. They require explicit approval after env/secret wiring is applied.

Use a short-lived local port-forward or approved internal runner to reach the ATHENA internal service. Do not use the public edge proxy for APOLLO-to-ATHENA bridge reads.

### 1. ATHENA Health

```sh
curl --fail --silent --show-error http://127.0.0.1:<athena_port>/api/v1/health
```

Expected: healthy response.

Abort if: connection fails, response is unhealthy, or the endpoint is only reachable through the public edge path.

### 2. APOLLO Health

```sh
curl --fail --silent --show-error http://127.0.0.1:<apollo_port>/api/v1/health
```

Expected: healthy response.

Abort if: connection fails or response is unhealthy.

### 3. ATHENA Bridge Missing Token

```sh
curl --silent --show-error --output /tmp/athena-bridge-missing-token.json --write-out '%{http_code}\n' \
  'http://127.0.0.1:<athena_port>/api/v1/presence/ingress-bridge?facility=<canary_facility>&since=<iso8601>&until=<iso8601>&session_limit=50'
```

Expected: `401`.

Abort if: status is `200`, private data is returned, or the route is reachable on the public edge proxy.

### 4. ATHENA Bridge Invalid Token

```sh
curl --silent --show-error --output /tmp/athena-bridge-invalid-token.json --write-out '%{http_code}\n' \
  -H 'X-Ashton-Internal-Read-Token: invalid-closed-test-token' \
  'http://127.0.0.1:<athena_port>/api/v1/presence/ingress-bridge?facility=<canary_facility>&since=<iso8601>&until=<iso8601>&session_limit=50'
```

Expected: `403`.

Abort if: status is `200` or response includes sensitive data.

### 5. ATHENA Bridge Valid Token

```sh
curl --silent --show-error --output /tmp/athena-bridge-valid-token.json --write-out '%{http_code}\n' \
  -H "X-Ashton-Internal-Read-Token: ${ATHENA_INTERNAL_READ_TOKEN}" \
  'http://127.0.0.1:<athena_port>/api/v1/presence/ingress-bridge?facility=<canary_facility>&since=<iso8601>&until=<iso8601>&session_limit=50'
```

Expected: `200` with redacted bridge JSON for the approved fixture or empty canary window.

Abort if: token is printed, raw account IDs/names appear, unsafe identity hashes appear, or response shape is malformed.

### 6. APOLLO Runtime Consumer

```sh
APOLLO_ATHENA_INTERNAL_READ_TOKEN="${ATHENA_INTERNAL_READ_TOKEN}" \
apollo presence athena-gate \
  --athena-url http://127.0.0.1:<athena_port> \
  --facility <canary_facility> \
  --since <iso8601> \
  --until <iso8601> \
  --session-limit 50 \
  --format json
```

Expected: APOLLO consumes ATHENA runtime output and emits redacted operational presence bridge results.

Abort if: APOLLO logs or output expose tokens, raw account IDs, names, unsafe hashes, or if any XP/team/reliability/public tournament/OpenSkill behavior is invoked.

## Evidence Ledger

The next packet should capture:

- operator approval text
- rollback owner
- rollback path
- target kube context
- intended GitOps path
- APOLLO manifest image
- ATHENA manifest image
- APOLLO live image before deploy
- ATHENA live image before deploy
- APOLLO live image after deploy, if deploy happens
- ATHENA live image after deploy, if deploy happens
- health checks before and after deploy
- metrics access before and after deploy
- bounded log access before and after deploy
- public edge bridge exposure check
- missing-token status
- invalid-token status
- valid-token status
- APOLLO consumer status
- redaction review result
- aborts or rollback events

Do not commit secret values, bearer tokens, internal read tokens, edge node tokens, database URLs, raw member/customer/facility/account IDs, or raw production logs containing PII.

## Exact Operator Approval Text

The next packet may proceed only after an operator provides text equivalent to:

```text
I approve Closed-Test Bridge Deploy + Smoke for target kube context admin@talos-tower / cluster talos-tower.

Scope approved:
- apply only the APOLLO/ATHENA internal bridge env/secret wiring required for the closed-test bridge;
- keep APOLLO pointed at http://athena.athena.svc.cluster.local, not the public edge proxy;
- run non-destructive health, auth rejection, valid-token read, and APOLLO consumer smoke commands only;
- do not run destructive probes, pod kills, rollout restarts outside the approved deploy mechanism, DB writes, fixture creation, public endpoint exposure, public tournaments, OpenSkill cutover, XP, teams, reliability scoring, messaging, social graph, or booking/commercial flows.

Rollback owner: <name>.
Rollback path: <GitOps revert or explicit rollback command sequence>.
Approved fixture/window: <canary facility or empty canary window>.
```

Without this approval, the next packet remains blocked.

## Remaining Blockers

- Live Kubernetes API access timed out.
- APOLLO and ATHENA live images were not captured.
- APOLLO and ATHENA health, metrics, and logs were not captured.
- Public edge proxy live exposure was not conclusively confirmed because public health returned `530` and bridge path timed out.
- `ATHENA_INTERNAL_READ_TOKEN` is missing from local ATHENA deploy inventory.
- `APOLLO_ATHENA_INTERNAL_READ_TOKEN` and `APOLLO_ATHENA_BASE_URL` are missing from local APOLLO deploy inventory.
- Operator approval is absent.
- Rollback owner is absent.
- Rollback path is not confirmed.
- Closed-test canary fixture or empty canary time window is not confirmed.

## Next Packet Readiness

The next packet can be Closed-Test Bridge Deploy + Smoke only after the remaining blockers above are cleared. That packet may apply approved config/secret/GitOps changes and run non-destructive smoke only with explicit operator approval.

Do not start Private Daily Presence Credit Runtime until the closed-test bridge deploy path is proven or consciously deferred.
