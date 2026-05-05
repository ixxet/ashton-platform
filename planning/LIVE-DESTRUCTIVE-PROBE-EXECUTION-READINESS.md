# Live Destructive Probe Execution Readiness

Status: prerequisite checklist only
Date: 2026-05-04
Parent plan: [`LIVE-DESTRUCTIVE-PROBE-PLAN.md`](LIVE-DESTRUCTIVE-PROBE-PLAN.md)

This artifact prepares the missing prerequisites for a later Live Destructive
Probe Execution Gate. It does not authorize execution and does not record any
new deployed truth.

No live mutation, pod kill, rollout restart, DB write, destructive `curl`,
fixture creation, deploy/GitOps change, or public/product widening is allowed
from this checklist.

## Current Gate Verdict

Execution remains blocked.

The local deploy inventory identifies candidate live resources, but the required
execution prerequisites are still missing:

- explicit operator approval
- exact target environment confirmation
- disposable APOLLO and ATHENA fixture identities
- concrete DB backup/restore path
- named rollback owner and rollback path
- current live image capture
- current health/readiness/metrics/log baseline
- accepted abort gates

## Candidate Target Environment

This section identifies the candidate environment from read-only local inventory.
It is not a deployed-truth claim and is not enough to execute probes.

| Item | Candidate from local inventory | Must be confirmed before execution |
| --- | --- | --- |
| GitOps inventory path | `/Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops` | Operator must confirm this is the intended live source. |
| Flux cluster path | `clusters/talos-tower` | Operator must confirm the current kube context points at the intended cluster. |
| Flux app path | `./homelab-gitops/apps` | Operator must confirm current live revision and reconciliation state. |
| APOLLO namespace | `agents` | Operator must confirm live `Deployment/apollo`, `Service/apollo`, and current pod. |
| ATHENA namespace | `athena` | Operator must confirm live `Deployment/athena`, `Service/athena`, and current pod. |
| Observability namespace | `observability` | Operator must confirm Prometheus access and ServiceMonitor scrape state. |
| Postgres namespace | `agents` | Operator must confirm shared `StatefulSet/postgres`, PVC, backup method, and restore path. |
| APOLLO manifest image | `ghcr.io/ixxet/apollo:sha-6b27618@sha256:6c2d4ef8c67d0ea21d3b41963eb9709d0114b53bfea56c30d456cafbb677515a` | Must be recaptured from the live deployment before approval. |
| ATHENA manifest image | `ghcr.io/ixxet/athena:v0.8.2@sha256:6524994c2c30bfd227b684b9144b95a798e3e46e600ac5da46df2a600dfd271d` | Must be recaptured from the live deployment before approval. |
| APOLLO internal service | `apollo.agents.svc.cluster.local`, service port `80`, container port `8081` | Must be verified with read-only cluster commands. |
| ATHENA internal service | `athena.athena.svc.cluster.local`, service port `80`, container port `8080` | Must be verified with read-only cluster commands. |
| ATHENA edge proxy service | `athena-edge-proxy.athena.svc.cluster.local`, service port `8080` | Must be verified before any edge-path probe. |

Execution approval must name one target scope:

- APOLLO-only
- ATHENA-only
- split APOLLO then ATHENA, with separate stop/go decisions

Combined APOLLO and ATHENA execution stays blocked unless the same operator,
backup owner, fixture owner, and rollback owner explicitly accept the combined
blast radius.

## Blocker-By-Blocker Readiness Plan

| Blocker | Current status | Readiness work required | Completion evidence |
| --- | --- | --- | --- |
| Operator approval | Missing | Name an operator of record and approve a single execution scope. | Approval text from this document, filled with concrete values. |
| Target environment | Candidate only | Confirm kube context, cluster, namespace, base URLs, Flux source revision, pod targets, and services. | Redacted read-only command output in the evidence ledger. |
| Disposable APOLLO fixtures | Missing | Create isolated fixture aliases for APOLLO competition/result and, if used, booking/request proof. Fixture creation is future-only and requires approval. | Fixture ledger with aliases, isolation proof, owner, setup time, and cleanup path. |
| Disposable ATHENA fixtures | Missing | Create isolated facility/node/account/subject fixture aliases. Fixture creation is future-only and requires approval. | Fixture ledger proving no real facility/account truth is targeted. |
| Backup/restore path | Missing | Choose and rehearse DB backup/restore or point-in-time recovery for APOLLO and ATHENA data in shared Postgres. | Backup identifier, restore command path, restore owner, and dry-run or rehearsal proof. |
| Rollback owner/path | Missing | Name a rollback owner and define image rollback, fixture cleanup, and DB restore decisions. | Rollback decision tree with owner and maximum recovery window. |
| Current images | Not captured now | Capture live APOLLO, ATHENA, and Postgres images from the cluster immediately before approval. | Redacted image capture output with timestamp. |
| Health/readiness baseline | Not captured now | Capture APOLLO and ATHENA health, readiness, and pod state. | Redacted command output with timestamp. |
| Metrics baseline | Not captured now | Capture `/metrics` and Prometheus scrape liveness for APOLLO and ATHENA. | Redacted metric names and required values only; no private labels committed. |
| Log baseline | Not captured now | Capture bounded recent logs for APOLLO and ATHENA. | Redacted logs showing no panic, migration failure, or credential leak. |
| Abort gates | Known but not accepted | Operator must accept the abort gates before the first mutating command. | Approval text includes abort-gate acceptance. |
| Evidence redaction | Required | Define where raw outputs live and what can be committed. | Evidence ledger uses aliases only. |

## Fixture Creation Requirements

Fixture creation is itself mutating setup work. Do not create fixtures from this
checklist. A later setup or execution packet must authorize fixture creation
explicitly.

### APOLLO Fixture Bundle

Required for APOLLO destructive execution:

- `apollo_fixture_scope`: canary-only context name and owner.
- `apollo_fixture_actor`: staff/manager/owner actor alias authorized for the
  fixture only.
- `apollo_fixture_trusted_surface`: runtime-only trusted-surface proof path; no
  proof value may be written to docs.
- `apollo_fixture_competition`: disposable competition/session alias.
- `apollo_fixture_participants`: disposable participant aliases that are not
  real members.
- `apollo_fixture_match`: disposable match alias.
- `apollo_fixture_result`: disposable result alias with known current version.
- `apollo_fixture_idempotency_key`: canary key for duplicate/replay proof.
- `apollo_fixture_booking_option`: only if booking/request probes are included.
- `apollo_fixture_booking_request`: only if booking/request probes are included.
- `apollo_fixture_cleanup`: API/CLI cleanup path or DB restore decision.

APOLLO fixture hard rules:

- Do not use real member, customer, competition, booking, safety, or public
  projection data.
- Do not use real customer contact information.
- Do not expose raw APOLLO IDs in committed docs.
- Do not use fixture setup to open public tournaments, OpenSkill read-path
  switch, public self-booking, public self-edit/rebook, or commercial workflow.
- Do not run accepted result lifecycle probes until stale/conflict and rejection
  probes have passed or been explicitly skipped by the operator.

### ATHENA Fixture Bundle

Required for ATHENA destructive execution:

- `athena_fixture_scope`: canary-only context name and owner.
- `athena_fixture_facility`: disposable facility alias.
- `athena_fixture_zone`: disposable zone alias.
- `athena_fixture_node`: disposable node alias.
- `athena_fixture_node_token`: runtime-only token path; no token value may be
  written to docs.
- `athena_fixture_account`: disposable account or subject alias.
- `athena_fixture_policy`: disposable policy alias for accepted/denied paths.
- `athena_fixture_tap_payloads`: redacted accepted and denied payload files.
- `athena_fixture_expected_projection`: expected count/history/analytics delta.
- `athena_fixture_cleanup`: replay/restore or fixture cleanup path.

ATHENA fixture hard rules:

- Do not target real facility, workstation, account, or student truth.
- Do not commit node tokens, hash salts, raw identity hashes, or tap payloads
  that can identify a real person.
- Source `fail` truth must stay immutable.
- Accepted-presence truth must stay separate from source-pass sessions.
- Accepted-presence session cutover remains blocked.
- Do not open operator UI, public report/export, XP, teams, reliability scoring,
  or prediction.

## Backup And Rollback Questions

These questions must be answered before approval.

### Shared Postgres Backup

- What exact database or schemas contain APOLLO data?
- What exact database or schemas contain ATHENA data?
- Is APOLLO and ATHENA data stored in the same Postgres database, separate
  schemas, or separate databases?
- What is the backup method: `pg_dump`, physical volume snapshot, WAL/PITR, or
  another documented mechanism?
- Where is the backup written?
- Who can read the backup, and how is credential material protected?
- How is restore rehearsed without overwriting live data?
- What is the maximum accepted data loss window?
- What is the maximum accepted recovery time?
- If restore affects shared Postgres, which other services are impacted?
- Is fixture cleanup preferred over full restore for rejection-only probes?
- What proof shows that restore can return APOLLO and ATHENA to the pre-probe
  state?

### APOLLO Rollback

- Who owns APOLLO rollback?
- What is the image rollback path if the APOLLO pod fails after probe execution?
- Is rollback a GitOps revert, rollout undo, image repin, or restore-only path?
- Which APOLLO tables or projections can the probe touch?
- Which APOLLO readback commands prove fixture-only mutation?
- Which APOLLO metrics prove post-probe recovery?
- What is the abort threshold for APOLLO health/readiness degradation?

### ATHENA Rollback

- Who owns ATHENA rollback?
- What is the image rollback path if the ATHENA pod fails after probe execution?
- What replay or restore path rebuilds ATHENA projection truth?
- Which ATHENA tables can accepted tap probes touch?
- How will source observations, accepted presence, and source-pass sessions be
  read back separately?
- What is the abort threshold for ATHENA readiness or projection recovery?
- If an edge proxy or tunnel is involved, who owns reverting that path?

## Exact Operator Approval Language

Use this exact approval shape. Do not treat informal approval as sufficient.
Approval is scoped to one execution phase and one first destructive command.

```md
I, <operator_name>, approve the Live Destructive Probe Execution Gate for:

Scope:
- <APOLLO-only | ATHENA-only | split APOLLO-then-ATHENA>

Target:
- kube context: <context>
- cluster/environment: <environment>
- namespace(s): <namespaces>
- service(s): <services>
- execution window UTC: <start> to <end>

Fixtures:
- APOLLO fixture aliases: <aliases or N/A>
- ATHENA fixture aliases: <aliases or N/A>
- fixture owner: <owner>
- fixture cleanup path: <path>

Backup and rollback:
- backup identifier: <backup-id>
- restore rehearsal evidence: <evidence-ref>
- rollback owner: <owner>
- rollback path: <path>
- maximum recovery window: <duration>

Current live preflight:
- image capture evidence: <evidence-ref>
- health/readiness evidence: <evidence-ref>
- metrics evidence: <evidence-ref>
- log evidence: <evidence-ref>

Abort gates:
- I accept the abort gates in LIVE-DESTRUCTIVE-PROBE-PLAN.md and
  LIVE-DESTRUCTIVE-PROBE-EXECUTION-READINESS.md.
- I understand execution must stop if any non-fixture data changes, any secret
  or private ID leaks, health/readiness fails to recover, metrics scrape fails,
  rollback becomes ambiguous, or the probe widens beyond the approved scope.

First destructive command:
- I approve exactly this first destructive command and no other:
  <exact command>
```

The operator must provide a new approval block before:

- first accepted APOLLO mutation
- first accepted ATHENA tap
- first in-flight SIGTERM proof
- first rollout restart
- any rollback command that mutates live state

## Read-Only Command Checklist

These commands are read-only or local render commands. They are still future
preflight commands because this artifact does not access the live cluster.
Do not commit raw output containing private labels, tokens, cookies, internal
hostnames, raw IDs, or credentials.

### Repo And Inventory Status

```sh
# Read-only.
git -C /Users/zizo/Personal-Projects/ASHTON/ashton-platform status --short --branch
git -C /Users/zizo/Personal-Projects/ASHTON/apollo status --short --branch
git -C /Users/zizo/Personal-Projects/ASHTON/athena status --short --branch

# Read-only local render. Does not contact the cluster.
kubectl kustomize /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/apps
kubectl kustomize /Users/zizo/Personal-Projects/Computers/Prometheus/homelab-gitops/infrastructure
```

### Cluster Context And Target Capture

```sh
# Read-only.
kubectl config current-context
kubectl cluster-info
kubectl get ns agents athena observability
kubectl -n flux-system get kustomization apps -o wide
kubectl -n flux-system get gitrepository flux-system -o wide
```

### Current Image Capture

```sh
# Read-only.
kubectl -n agents get deployment apollo \
  -o jsonpath='{.metadata.generation}{"\n"}{.spec.template.spec.initContainers[*].image}{"\n"}{.spec.template.spec.containers[*].image}{"\n"}'

# Read-only.
kubectl -n athena get deployment athena \
  -o jsonpath='{.metadata.generation}{"\n"}{.spec.template.spec.containers[*].image}{"\n"}'

# Read-only.
kubectl -n agents get statefulset postgres \
  -o jsonpath='{.metadata.generation}{"\n"}{.spec.template.spec.containers[*].image}{"\n"}'
```

### Pod, Service, And Readiness Capture

```sh
# Read-only.
kubectl -n agents get deploy apollo -o wide
kubectl -n agents get pods -l app.kubernetes.io/name=apollo -o wide
kubectl -n agents get svc apollo -o wide
kubectl -n agents get endpoints apollo -o wide

# Read-only.
kubectl -n athena get deploy athena -o wide
kubectl -n athena get pods -l app.kubernetes.io/name=athena -o wide
kubectl -n athena get svc athena athena-edge-proxy -o wide
kubectl -n athena get endpoints athena athena-edge-proxy -o wide

# Read-only.
kubectl -n agents get statefulset postgres -o wide
kubectl -n agents get pods -l app.kubernetes.io/name=postgres -o wide
kubectl -n agents get pvc
```

### Internal Health And Metrics Preflight

Port-forwarding is read-only cluster access, but it still opens a local session.
Run it only after the target environment is confirmed.

```sh
# Read-only local port-forward session for APOLLO.
kubectl -n agents port-forward svc/apollo 18081:80

# Read-only HTTP checks through the local APOLLO port-forward.
curl -fsS http://127.0.0.1:18081/api/v1/health
curl -fsS http://127.0.0.1:18081/api/v1/public/competition/readiness
curl -fsS http://127.0.0.1:18081/metrics

# Read-only local port-forward session for ATHENA.
kubectl -n athena port-forward svc/athena 18080:80

# Read-only HTTP checks through the local ATHENA port-forward.
curl -fsS http://127.0.0.1:18080/api/v1/health
curl -fsS 'http://127.0.0.1:18080/api/v1/presence/count?facility=<canary_facility_alias>'
curl -fsS http://127.0.0.1:18080/metrics
```

### Prometheus Scrape Preflight

```sh
# Read-only.
kubectl -n observability get servicemonitor apollo athena -o wide
kubectl -n observability get pods,svc -l app.kubernetes.io/name=prometheus -o wide

# Read-only local port-forward session. Replace service name after discovery.
kubectl -n observability port-forward svc/<prometheus_service> 19090:9090

# Read-only Prometheus API queries. Redact labels before committing evidence.
curl -fsS 'http://127.0.0.1:19090/api/v1/query?query=up{job="apollo"}'
curl -fsS 'http://127.0.0.1:19090/api/v1/query?query=up{job="athena"}'
```

### Log Baseline

```sh
# Read-only. Redact before committing.
kubectl -n agents logs deployment/apollo --since=15m --tail=250

# Read-only. Redact before committing.
kubectl -n athena logs deployment/athena --since=15m --tail=250
```

### Explicitly Future-Only Mutating Classes

Do not run these from readiness prep:

```sh
# FUTURE ONLY. Requires approval block before use.
kubectl -n <namespace> rollout restart deployment/<service>

# FUTURE ONLY. Requires approval block before use.
kubectl -n <namespace> exec <canary_pod> -- sh -c 'kill -TERM 1'

# FUTURE ONLY. Requires approval block before use.
curl -X POST '<service_base_url>/<mutating_route>' ...

# FUTURE ONLY. Requires approval block before use.
<db_backup_or_restore_command>

# FUTURE ONLY. Requires approval block before use.
<fixture_creation_command>
```

## Execution Gate Ordering

The later execution gate must run in this order:

1. Confirm repo and inventory status.
2. Confirm target environment and kube context.
3. Capture current images, pods, services, readiness, metrics, and logs.
4. Prove backup/restore readiness.
5. Register disposable fixture aliases and cleanup path.
6. Obtain exact operator approval language for the first destructive command.
7. Run only the approved first destructive command.
8. Capture readback, metrics, logs, and abort-gate status.
9. Stop for operator review before the next destructive command.

## No Deployed Truth Claim

This readiness checklist changes repo/docs truth only. It does not prove live
mutation safety, does not prove in-flight SIGTERM behavior, and does not update
deployed truth.

Actual live destructive execution, in-flight production SIGTERM proof, public
tournament readiness, and OpenSkill read-path cutover remain blocked.
