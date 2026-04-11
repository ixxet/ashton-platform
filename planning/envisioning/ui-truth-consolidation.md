# UI Truth Consolidation

This document consolidates:

- the UI product vision
- the 2026-04-09 UI truth audit
- the current backend/platform boundaries
- the current coaching / nutrition / planner / tracker product direction

This is the working decision doc for what the web product should become without
lying about what the backend already supports.

## Core Rule

Multiply:

- durable state models
- ownership boundaries
- negative-proof coverage
- honest docs

Do not multiply:

- speculative endpoints
- premature public contracts
- convenience surfaces we are not sure we want long term

That rule is the filter for every page, tab, API, and tracer below.

## Consolidated Product Posture

ASHTON should still become one product with two intentional shells:

- `Member shell`
- `Ops shell`

But Phase 2 should not widen both at the same speed.

The member shell already has the richest truthful backend.
The ops shell still needs role/authz and a real staff runtime boundary.

So the consolidation decision is:

1. keep member-facing work centered in `apollo`
2. keep facility and presence truth centered in `athena`
3. keep future staff reconciliation/search/read logic centered in `hermes`
4. keep the gateway narrow until there is a real product reason to widen it
5. do not invent shared contracts in `ashton-proto` before a real tracer needs
   them

## What The Current Platform Can Honestly Support

### Ready Now

- member login and signed session flow
- member profile summary and bounded profile inputs
- explicit workout tracking
- deterministic workout recommendation
- planner substrate APIs
- competition substrate APIs
- facility occupancy reads
- facility catalog, hours, zones, and closure reads
- bounded read-only staff occupancy and reconciliation truth

### Partial Now

- member shell beyond the current thin APOLLO UI
- planner UI
- competition UI
- facility info screens
- tap-in driven member product UX
- ops dashboards using occupancy/facility truth

### Missing Now

- role model for `supervisor`, `manager`, `owner`
- booking / rental / calendar conflict engine
- meal and nutrition runtime
- badges / avatars / public profile identity
- member-facing tap history and streak model
- staff web runtime
- public competition reads and social layer

## Safe Product Strategy

The safe Phase 2 strategy is:

1. expose more of the real backend truth that already exists
2. add only the next missing durable state models
3. avoid broad public product promises until the data model is stable
4. use honest placeholders in UI only where the underlying future state model is
   already agreed

An honest placeholder is allowed.

Example:

- `Meals` tab can exist if it clearly says the runtime is not yet active and if
  the future state model is already intended

A dishonest placeholder is not allowed.

Example:

- fake meal recommendations with no meal model, no nutrition profile, and no
  guardrail language

## Member Shell Consolidation

## Navigation

Keep the member bottom nav at five tabs:

- `Meals`
- `Workouts`
- `Home`
- `Tournaments`
- `Settings`

Keep `Profile` behind the top-right avatar.

That remains the right call.

## Member Page Breakdown

| Page | Status | Backend truth | Consolidated decision |
| --- | --- | --- | --- |
| Login splash | `Ready` | APOLLO auth/session is real | Build now over real auth; polish is a frontend concern |
| Tap-in entry | `Partial` | ATHENA edge tap exists; APOLLO visit ingest exists | Build only after the bounded member-facing tap-link / streak model lands in `Tracer 27` |
| QR e-tap confirmation | `Partial` | physical truth exists, member linking does not | Keep as a Phase 2 target only if explicit link state lands in `Tracer 27` |
| Home dashboard | `Partial` | workouts, recommendation, membership, profile, occupancy/facility reads exist in pieces | Build now as a composition page over real reads; keep cards honest |
| Profile home | `Partial` | bounded profile state exists | Build over current profile truth, but keep identity features narrow |
| Badges / achievements | `Missing` | no runtime | Do not invent yet; hold the slot in profile IA only |
| Avatar / profile photo | `Missing` | no runtime | Defer until profile identity widening is real |
| Workout tracker | `Ready-ish` | real workout APIs and current shell | Safe to widen now |
| Workout history | `Ready-ish` | real workout list/detail | Safe to widen now |
| Workout planner | `Partial` | planner APIs are real | Safe to build now over existing planner substrate |
| Coaching switcher | `Partial` | recommendation is real, broader coaching is not | Build as IA only if clearly split into current recommendation vs future coaching |
| Meals landing | `Missing` | no meal runtime | Allowed only as an honest placeholder, not as a fake feature |
| Meal tracker | `Missing` | no meal log model | Requires bounded backend widening first |
| Meal planner | `Missing` | no nutrition plan model | Requires bounded backend widening first |
| Nutrition coaching | `Missing` | no nutrition engine | Requires bounded backend widening first |
| Tournaments landing | `Partial` | competition substrate is real | Build only over self-scoped/internal truths first |
| Queue / join flow | `Partial` | APOLLO queue state exists, but current semantics are owner-scoped | Needs backend mutation before honest member self-service queue UX |
| Session / match detail | `Partial` | session/team/roster/match runtime exists | Possible, but current access semantics need review |
| Standings / leaderboard | `Missing` | internal standings exist, public competition reads do not | Defer public leaderboards until intentional widening |
| Competition stats | `Partial` | self-scoped member stats exist | Safe to expose as self-only, not public |
| Settings home | `Partial` | app settings are mostly frontend; account controls are limited | Build now with honest scope |
| Account export / delete | `Missing` | no documented runtime | Needs bounded account-management widening |
| Signed waiver | `Missing` | no waiver runtime | Do not fake |
| Notification history | `Missing` | no notification runtime | Do not fake |
| Facility hours / contact | `Partial` | ATHENA facility truth exists | Build as read-only composition over facility truth |
| FAQ / support | `Missing-ish` | no dedicated FAQ runtime yet | Can ship as static/help content before LLM work |

## Member Home Consolidation

The first meaningful expanded member home should be built from real cards only:

- `Tap state`
- `Today workout`
- `Recovery / recommendation`
- `Planner preview`
- `Competition status`
- `Facility hours / closures`

Cards that should not appear until the runtime is real:

- meal macros
- badge streak banners
- public leaderboard rank
- AI chat coach

## Workouts Consolidation

The workouts area should become a three-mode surface:

- `Tracker`
- `Planner`
- `Coaching`

That is the correct consolidation.

But the modes must map to honest backend truth.

### Tracker

Safe now:

- current workout
- add/edit exercises
- sets / reps / load / notes
- finish workout
- workout history
- progression graphs from workout history

### Planner

Safe after UI widening over current planner APIs:

- exercise catalog browse
- equipment catalog browse
- reusable templates / loadouts
- week-rooted planning

Not safe yet:

- planner auto-instantiating workouts
- planner silently changing recommendations
- planner pretending to know live equipment availability

### Coaching

Current truthful version:

- deterministic workout recommendation
- explanation of current recommendation evidence if added later

Future widened version:

- conservative load suggestions based on profile + workout history + effort
  feedback
- progression suggestions
- warning language that this is guidance, not medical advice

The coaching mode should not start as a chatbot.
It should start as a structured recommendation surface.

## Meals Consolidation

The meals area should eventually mirror workouts conceptually:

- `Tracker`
- `Planner`
- `Guidance`

But unlike workouts, none of this is runtime-real yet.

That means the correct consolidation decision is:

- keep the IA now
- do not fake the backend
- build the state model before building the polished meal product

## Tournaments Consolidation

The tournaments area should also become a multi-surface family:

- `Overview`
- `Queue`
- `Sessions`
- `Stats`
- `Standings`

But current backend truth supports only a narrower first version.

### Safe First Version

- self-scoped stats
- current or owned sessions if semantics remain owner-scoped
- session detail views for truthful session objects

### Unsafe Early Version

- public ladder / rivalry feeds
- visible enemy systems
- public streak boards
- organization-wide standings without explicit product widening

## Profile And Settings Consolidation

Keep the split:

- `Profile` = identity and public-facing self
- `Settings` = controls, legal, support, account, app behavior

That split is still correct.

### Profile Should Eventually Hold

- avatar
- display identity
- favorite sports
- badges
- achievement highlights
- shareable QR/profile card

### Settings Should Eventually Hold

- account management
- privacy controls
- notification preferences
- support / issue reporting
- waiver and legal documents
- facility switcher
- theme and opening-page preferences

## Ops Shell Consolidation

The ops shell is still valid as a product goal, but it is not yet backend-ready
in the same way as the member shell.

The biggest backend blocker is not styling.
It is role and authorization truth.

## Ops Page Breakdown

| Page | Status | Backend truth | Consolidated decision |
| --- | --- | --- | --- |
| Ops login entry | `Missing` | no supervisor/manager/owner authz model | Needs `Tracer 28` backend widening first |
| Supervisor live dashboard | `Missing` | no staff web runtime | Design now, do not implement as real product yet |
| Live occupancy board | `Partial` | ATHENA occupancy is real | Can build as read-only internal UI if access path is intentional |
| Reconciliation page | `Partial` | HERMES reconciliation truth is real but CLI-only | Needs HTTP/session layer before honest web runtime |
| Facility catalog page | `Partial` | ATHENA facility truth is real | Can build as internal read-only page if config is guaranteed |
| Closure management | `Missing` | no write path | Defer |
| Calendar / schedule page | `Missing` | no derived scheduling engine | Needs new backend layer |
| Booking requests | `Missing` | no booking runtime | Needs new backend layer |
| Competition control | `Partial` | APOLLO competition runtime exists but is owner-scoped | Needs authz mutation before staff UX is honest |
| Match intervention | `Partial` | some competition state mutation exists | Needs staff role semantics and safe workflow design |
| Member lookup | `Missing` | no staff member-search runtime | Do not fake |
| Owner-only private stats view | `Missing` | no owner role model | Needs explicit role/authz widening |
| FAQ / content admin | `Missing` | no content runtime | Defer until content ownership is real |
| Role management | `Missing` | no role model | Needs backend widening first |
| Audit / routed call logs | `Partial` | gateway audit is real, but narrow and internal | Internal-only for now |

## Calendar And Booking Consolidation

The desired calendar is correct as a product concept.

But current backend truth does not support it yet.

Current raw ingredients:

- facility hours
- closure windows
- facility zones
- competition facility/zone references

Missing ingredients:

- derived schedule state
- event model that joins facility operations with tournament/event time blocks
- booking request model
- booking conflict model
- quote model
- role-aware visibility rules

So the correct consolidation decision is:

1. do not put booking or scheduling logic into `athena`
2. create a separate scheduling / booking composition layer when the time comes
3. let that layer own typed HTTP and CLI surfaces over Postgres-backed booking,
   quote, conflict, and schedule truth
4. use raw ATHENA facility truth as an input, not the full answer
5. use calendar libraries as presentation only; do not let a plugin or
   third-party booking engine become the system of record unless it can meet
   the same typed, inspectable, AI-safe contract standard

## Tracer 24 Through Tracer 28 Consolidation

The official Phase 2 split should be:

- `Tracer 24`: deterministic coaching substrate
- `Tracer 25`: conservative nutrition substrate
- `Tracer 26`: explanation and bounded AI helper layer
- `Tracer 27`: member presence / tap-link / streak substrate
- `Tracer 28`: role/authz and staff runtime boundary substrate

## Official Split

### Tracer 24

`Deterministic coaching substrate`

Make real:

- richer coaching profile inputs
- effort / fatigue / recovery feedback capture
- conservative starting-load logic
- structured progression suggestions
- explicit explanation fields

Do not include:

- meal runtime
- macro engine
- chatbot UI
- opaque LLM-driven decision core

### Tracer 25

`Conservative nutrition substrate`

Make real:

- nutrition profile
- dietary restrictions
- cuisine preferences
- cooking ability / time / budget fields
- meal log
- conservative macro / calorie range logic
- safe suggestion framing

Do not include:

- medical diagnosis
- unrestricted supplement advice
- freeform chatbot as the primary runtime

### Tracer 26

`Explanation and bounded AI helper layer`

Make real:

- explanation surfaces for coaching and nutrition guidance
- structured suggestion rewriting
- optional bounded ask-why / ask-for-variation helper flows
- future chat doorway if and only if the deterministic substrates are already
  stable

This keeps the LLM subordinate to the state model instead of letting it become
the source of truth.

### Tracer 27

`Member presence / tap-link / streak substrate`

Make real:

- explicit facility-presence linking from ATHENA truth into member-visible
  product truth
- member-facing tap-link state
- streak state and streak events
- no-fake-presence product reads

Do not include:

- hidden visit inference beyond approved link logic
- broad gamified identity
- staff authz or ops workflow expansion

### Tracer 28

`Role/authz and staff runtime boundary substrate`

Make real:

- explicit `member`, `supervisor`, `manager`, and `owner` roles
- capability checks and actor attribution
- trusted-device or trusted-surface primitives for elevated actions
- safe staff-runtime boundary for future ops UI and later agent approvals

Do not include:

- polished ops product
- broad admin manipulation tools
- speculative shared-contract widening

## LLM Architecture Consolidation

The right instinct is:

- AI-based
- not chatbot-first
- conservative
- domain-bounded

That is exactly the right posture.

## Correct Role For The LLM

The LLM should not own:

- durable member state
- load progression truth
- macro range truth
- meal log truth
- legal or medical safety policy

The LLM can own:

- explanation
- wording
- substitutions
- preference-aware variants
- clarifying suggestions
- FAQ and support assistance

## Correct Phase 2 Data Models Before LLM Heavy UX

### Coaching-Side Durable Models

- `coaching_profile`
- `training_goal`
- `effort_feedback`
- `recovery_feedback`
- `progress_metric`
- `personal_record`
- `coaching_recommendation`
- `coaching_explanation`

### Nutrition-Side Durable Models

- `nutrition_profile`
- `dietary_restriction`
- `meal_preference`
- `budget_preference`
- `cooking_capability`
- `meal_log`
- `nutrition_recommendation`
- `nutrition_explanation`

If those models are not real, then the LLM layer should stay narrow.

## Chat Surface Guidance

Do not launch Phase 2 coaching or nutrition as a generic chat bot.

Better first pattern:

- structured recommendation card
- `Why this?`
- `Give me an easier version`
- `Give me a cheaper meal option`
- `Swap for vegetarian`
- `Adjust for no kitchen access`

That leaves the door open for chat later without making chat the core product.

## Phase 3 Definition

Phase 3 should not mean:

- everything becomes public
- every idea becomes an app feature
- the platform suddenly turns into a social network

Phase 3 should mean:

- the trusted Phase 2 substrates are now productized into a real web experience
- the member shell becomes polished, broad, and coherent
- the ops shell becomes real over a stable role/authz model
- booking/scheduling composition becomes real if Phase 2 laid the right state
  models
- the AI layer becomes a visible helper over already-trusted deterministic cores
- distribution, branding, and later native packaging become worthwhile
- when a real page proves a data blocker, the narrow backend or storage line
  should land before the polished surface that depends on it

## Suggested Phase 3 Shape

Shared Phase 3 substrate lines may land before shell polish when needed:

- ATHENA Postgres-backed observation and derived-session analytics for
  manager-grade occupancy, flow, and mapping views
- a separate scheduling and booking substrate above ATHENA facility truth for
  calendars, requests, quotes, and conflict checks

### Phase 3A

`Real member web product`

- polished member shell
- coherent planner / tracker / coaching surfaces
- truthful competition shell
- installable PWA quality

### Phase 3B

`Real ops product`

- supervisor dashboard
- manager scheduling and competition control
- owner analytics and policy surfaces

### Phase 3C

`Distribution and brand hardening`

- white-labeling / theming system
- stronger notification and support flows
- native packaging if justified

## What Should Be Stable Before Stopping Phase 2

Phase 2 is ready to stop when these are true:

1. member auth/session truth is stable
2. workout tracker + planner + recommendation substrate are stable
3. visits / tap-link / streak truth is real
4. competition truth is honest about who can see and control what
5. role/authz model is real because ops UI is expected soon after `Milestone 2.0`
6. coaching and nutrition substrates are split or scoped tightly enough to stay
   conservative
7. scheduling/booking is either backed by a real derived model or explicitly
   deferred
8. docs and proposal/apply posture match runtime truth

## Immediate Consolidation Decisions

These are the decisions this document supports right now:

1. Expand the member shell before building a real ops shell.
2. Add member-facing visits/tap-link/streak truth before pretending tap-in is a
   mature product feature.
3. Keep coaching, nutrition, AI-helper, presence, and role/authz work split
   across `Tracer 24` through `Tracer 28`.
4. Keep the LLM as a bounded helper over deterministic substrates, not the core
   engine.
5. Keep scheduling and booking domain logic out of ATHENA; let ATHENA own
   facility, presence, history, and analytics inputs while a separate
   scheduling layer owns booking truth.
6. Add a real role/authz model before building supervisor/manager/owner product
   workflows.
7. If the first demo must sell manager value, land a real manager-data
   substrate before charts, booking dashboards, or analytics copy.
8. Keep public competition and social features deferred until the competition
   substrate is stable enough to deserve them.
9. Defer any AI-written occupancy or booking summary until real storage,
   derived session facts, and bounded analytics reads exist underneath it.

## Decision Status

This is the current UI/product decision doc.

Use it to:

- map page ideas to truthful backend readiness
- identify backend gaps exposed by UI flow work
- decide whether a surface belongs in Phase 2, Phase 3, or later

Do not let it overrule runtime truth in repo-local hardening or release docs.

## Next Pass

The next useful pass should break this into explicit screen specs:

- one spec per member page
- one spec per ops page
- each spec labeled:
  - `build now`
  - `build over current backend truth`
  - `needs backend widening first`
  - `defer on purpose`

## Screen Spec Contract

For the next screen-by-screen breakdown, use this exact posture:

- treat [ui-product-vision.md](/Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/envisioning/ui-product-vision.md)
  as gap discovery, not truth
- arbitrate every screen against
  [ui-truth-consolidation.md](/Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/envisioning/ui-truth-consolidation.md)
  and
  [2026-04-09-ui-truth-audit.md](/Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/audits/2026-04-09-ui-truth-audit.md)

For each screen, label it only as:

- `build now`
- `build over current backend truth`
- `needs backend widening first`
- `Phase 3 only`

For each gap found, always name:

- required durable model
- owning repo
- likely tracer
- whether the surface is honest placeholder only

The next pass must not invent:

- meal intelligence before `Tracer 25`
- staff ops truth before `Tracer 28`
- booking/calendar truth before a real scheduling layer
- public competition/social surfaces
- chat as the core runtime

That keeps the UI workflow useful.
It becomes a gap detector and IA shaper, not a fantasy contract generator.

## Agentic Readiness

The backend is not legacy.
It is structurally good for agentic use, but it is not fully agent-native yet.

Current read:

- read/propose readiness: fairly good
- safe autonomous write readiness: not there yet
- agentic-first web readiness: partial, not complete

### Why It Is Promising

- strong repo boundaries
- real service layers
- deterministic slices already exist
- `apollo` already has real HTTP and CLI seams
- docs are unusually honest

### What Is Still Missing Before Agents Should Mutate Broadly

- role/authz truth beyond member and owner-style boundaries
- explicit proposal/apply models for assistant actions
- broader auditability for AI-originated writes
- stable helper read models for agents
- gateway widening only if there is a real product reason
- clearer approval workflow for plan changes

So yes, agents can already inspect and help modularly.
No, they should not yet be allowed to freely shift things around as autonomous
write actors.

`Tracer 26` is the right place for helper reads, explanations, and structured
proposal shapes to start becoming real in a bounded way.
`Tracer 28` is the right place for capability, actor, and approval rails to
make broader safe mutation honest.
`Milestone 2.0` should prove the combined inspect/propose posture across the
full Phase 2 ladder.
