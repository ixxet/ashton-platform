# ASHTON Stack Audit — 2026-04-04

## Purpose

This audit measures the ASHTON application stack as it exists now, not as the
architecture essays imagined it would exist later.

Scoring uses five dimensions:

- `Completeness`: how much of the repo's intended role is real now
- `Code / mechanics`: implementation quality, tests, and runtime discipline
- `Docs`: clarity, honesty, and front-door usability
- `Inter-connectedness`: real cross-repo integration maturity
- `Currentness`: whether docs, tags, and runnable truth still agree

## Executive Verdict

ASHTON is real, but it is not yet broadly flyable as a product stack.

What is real now:

- `athena` is a real Go service with a credible physical-truth boundary.
- `apollo` is a real backend slice for auth, profile state, visits, workouts,
  and deterministic recommendations.
- `hermes` is a real but very small read-only CLI tracer.
- `ashton-mcp-gateway` is a real but very small routed-tool proof.
- `ashton-proto` is a real shared-contract repo, but still too hand-stitched.
- `ashton-platform` is a strong human control plane, not a machine-enforced one.

What is not true yet:

- the stack is not ready to claim a broad member product
- the stack is not ready to claim a mature staff assistant
- the gateway is not yet ready to claim a full control plane
- cross-repo governance is still driven more by documentation than automation

## Repo Scores

| Repo | Completeness | Code / Mechanics | Docs | Inter-connectedness | Currentness | Flyable now? | Short verdict |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `ashton-platform` | `78/100` | `56/100` | `84/100` | `73/100` | `70/100` | No | Strong source-of-truth repo, but governance is still markdown-mediated |
| `ashton-proto` | `64/100` | `70/100` | `54/100` | `45/100` | `58/100` | No | Real shared-contract slice, but release truth and one-source-of-truth mechanics still drift |
| `athena` | `47/100` | `82/100` | `84/100` | `56/100` | `91/100` | No | Best service boundary in the stack; real and growing, not yet production-complete |
| `apollo` | `46/100` | `81/100` | `67/100` | `55/100` | `86/100` | No | Usable backend slice, but still short of APOLLO the product |
| `hermes` | `27/100` | `82/100` | `58/100` | `24/100` | `68/100` | No | Credible read-only tracer, not yet a broad staff runtime |
| `ashton-mcp-gateway` | `34/100` | `78/100` | `61/100` | `26/100` | `81/100` | No | Good routed-tool proof, still far from a mature control plane |

## Cross-Repo Findings

### 1. The service boundaries are real

This is the strongest architectural property in the stack:

- `ATHENA` owns physical truth
- `APOLLO` owns member intent
- `HERMES` is staff-only
- `ashton-proto` owns shared contracts
- `ashton-mcp-gateway` owns routed tool access, not domain truth

That separation is not cosmetic. It already changes how data moves and where
future features belong.

### 2. The multirepo tax is also real

The repos are not the problem. The missing automation around them is.

Current cross-repo coordination still relies too heavily on:

- release-line tables
- roadmap discipline
- tracer closure notes
- human memory

ASHTON should stay multirepo for now, but it needs monorepo-grade automation in
its contract checks, release discipline, and repo-to-repo verification.

### 3. Current truth still drifts in a few critical places

The biggest examples found in this audit:

- `ashton-proto` documents `v0.3.1` as current even though the latest tag is
  `v0.3.0`
- `ashton-mcp-gateway` documents `v0.1.0` as the active line while the latest
  shipped tag is `v0.0.1`
- `BUILD-ORDER.md` still reads like active truth even though it is historical
- some standalone Mermaid sources overstated future systems compared with the
  actual repo READMEs

### 4. The stack is strongest where it is narrowest

The most credible repos are the ones that kept scope tight:

- `athena`: one real physical-truth service boundary
- `apollo`: one real backend slice with strong tests
- `hermes`: one bounded staff CLI question
- `ashton-mcp-gateway`: one manifest-backed routed read

The weakest areas are where documentation or schema scope ran ahead of runtime.

## Repo-by-Repo Gaps

### ashton-platform

Top gaps:

- historical planning docs still looked active
- source-of-truth ordering still elevated stale build-order material
- GitHub-safe navigation was weaker than local navigation
- governance is still human and prose-heavy

### ashton-proto

Top gaps:

- JSON Schemas are not independently publishable enough
- the same contract is still manually maintained across proto, schema, helper,
  fixtures, and MCP
- release truth drifted from git tags
- maintainer decision rules were too implicit

### athena

Top gaps:

- container entrypoint currently launches the binary without `serve`, so the
  default container start does not run the service
- persistence is authored but not integrated
- schema IDs and runtime IDs are not fully aligned
- event publish dedupe is process-local
- health and metrics surfaces are still optimistic

### apollo

Top gaps:

- verification delivery is effectively dev-first
- there is no runtime path to manage `claimed_tags`
- the member product surface is still backend-only
- ARES and recommendation persistence are mostly schema-authored, not runtime
- docs still assume too much internal vocabulary

### hermes

Top gaps:

- only one staff question is real
- there is no live deployment proof
- there is no server/runtime boundary beyond the CLI
- observability is thin
- the repo only clearly earns its own boundary if a second real slice lands

### ashton-mcp-gateway

Top gaps:

- one routed ATHENA read is real, but the control-plane vision is still future
- routing is not yet generic enough to claim broad manifest-driven maturity
- there is no caller identity, persisted audit, or approval runtime
- docs and source diagrams drifted on current vs planned state
- local runbooks assumed personal filesystem paths

## What Needs To Exist Before The Stack Can Fly

The minimum credible "fly" threshold is not "everything is built." It is:

1. `athena` must be operably truthful.
2. `apollo` must complete a real member path, not just backend internals.
3. `hermes` must either prove a second real slice or stay intentionally tiny.
4. `ashton-mcp-gateway` must earn caller identity and persisted audit before it
   calls itself a control plane.
5. `ashton-proto` must reduce contract drift and tighten release truth.

That means the next practical path is:

1. harden `athena`
2. give `apollo` a minimal real member shell
3. give `hermes` observability and deployment proof
4. then return to gateway maturity

## Architecture Judgments

### Multirepo vs monorepo

Recommendation: stay multirepo for now.

Reasoning:

- the boundaries are real, not arbitrary
- `ATHENA`, `APOLLO`, `HERMES`, and the gateway widen at different speeds
- separate repos keep those boundaries honest

But:

- the current pain is real
- the fix is not "collapse everything immediately"
- the fix is stronger cross-repo automation, shared release discipline, and
  better contract derivation

Revisit monorepo consolidation only if atomic multi-repo changes become the
dominant development mode across most weeks.

### Protobuf

Recommendation: keep protobuf, but stop pretending it is the whole answer.

Reasoning:

- protobuf is justified for shared enums, occupancy types, generated clients,
  and future multi-consumer bindings
- today, the most active cross-repo surface is still the JSON event layer plus
  Go helpers
- MCP manifests are not derived from proto yet

So protobuf is useful, but only partially earned today. It becomes fully
justified when more of the active contract surface is mechanically derived from
it instead of manually shadowed beside it.

### GitHub org

Recommendation: yes, institute an org now.

Reasoning:

- ASHTON is now a true multi-repo platform, not a set of unrelated personal
  repos
- an org makes repo grouping, shared policies, and external credibility better
- it gives you room for branch protection, CODEOWNERS, issue templates, and
  future cross-repo automation under one namespace

Suggested split:

- one org for the ASHTON application stack
- keep infrastructure repos grouped there only if you want the full platform
  narrative visible together

## Documentation Outcome

This audit should keep one rule in force across the stack:

- current runtime truth belongs in repo READMEs, repo roadmaps, ADRs,
  migrations, schemas, and tracer evidence
- future architecture belongs in background essays, planned release lines, and
  explicitly marked deferred sections

If those two layers disagree, current runtime truth wins.
