# Role Page Visual Spec

This document describes the user flows and page visuals for the future product
in text, grounded against the current truth docs.

Use it as an IA and visual-shape reference.
Do not treat it as a contract source.

Arbitrate buildability against:

- [UI Truth Consolidation](/Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/envisioning/ui-truth-consolidation.md)
- [2026-04-09 UI Truth Audit](/Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/audits/2026-04-09-ui-truth-audit.md)

Companion Mermaid flow:

- [role-shell-flows.mmd](/Users/zizo/Personal-Projects/ASHTON/ashton-platform/planning/envisioning/diagrams/role-shell-flows.mmd)

## Scope

This covers these user groups:

- business booker
- member
- supervisor as `member+`
- manager
- developer / system admin

Here, `developer` means the highest internal control surface.
It maps closest to the earlier `owner / system admin / dev mode` concept, not
to a normal customer role.

## Shared Product DNA

The product should feel like one family of software, but not one flattened
layout.

Shared visual DNA:

- warm, physical, slightly tactile color system instead of sterile white SaaS
- clear state chips for `live`, `draft`, `queued`, `checked in`, `needs action`
- generous corner radius and layered cards on mobile
- faster, denser, more squared-off panels on desktop ops
- motion that confirms state changes instead of decorating every click
- obvious freshness indicators on anything tied to real-world state

Shared typography direction:

- member shell should use a more expressive headline face with a cleaner body
  face
- ops shell should tighten the typography and reduce flourish
- both shells should still feel related through color, spacing, and motion

Shared interaction rules:

- one clear primary action per screen
- secondary actions grouped instead of scattered
- destructive actions visually quarantined
- refresh state and source-of-truth age always visible on real-time screens

## Shell Split

The product should still have two shells:

- `Member shell`
- `Ops shell`

The member shell is phone-first.
The ops shell is desktop-first but should degrade cleanly to phone triage.

## Unified Entry

### Visual Feel

The login page should feel atmospheric and deliberate, not like a utility form.

Visual direction:

- large branded splash field in the background
- slow-moving light, mesh, or gradient layer behind the form
- centered action stack with strong hierarchy
- one prominent identity action area
- subtle facility-aware context such as current location, hours, or status line

The screen should not feel like:

- an admin panel login
- a generic B2B auth card
- a chatbot landing page

### Layout

Top:

- wordmark / product mark
- facility selector if relevant
- optional `Open now`, `Closing soon`, or `Closed` status chip

Middle:

- `Tap In`
- `Sign In`
- `Business Booking`

Bottom:

- hours
- support
- privacy / legal links

### Technical Backing

- `APOLLO` owns member auth/session
- `ATHENA` owns physical tap truth
- role routing exists as a design target, but supervisor/manager/developer authz
  is still future backend work

## Main Visual Language By Role

## Business Booker

### Purpose

Business bookers are not entering a fitness tracker.
They are entering a scheduling and request workspace.

### Default Home

`Business booking home`

This screen should feel schedule-first and confidence-first.

### Home Layout

Top bar:

- facility name
- account name
- message center
- account menu

Main canvas:

- left column: booking summary, upcoming reservations, request status
- center: large availability calendar or schedule board
- right column: quote summary, facility rules, support contact

Primary action:

- `Request booking`

### Visual Feel

- cleaner and more neutral than member mode
- less playful than the member shell
- emphasis on trust, timing, and clarity

### Core Pages

#### Availability / Booking Calendar

This page should look like a polished reservation board.

Visual structure:

- top date range controls
- facility and zone filters
- color-coded availability blocks
- closures and blocked periods visibly overlaid
- selected slot inspector on the right

Important feel:

- should look authoritative
- should make conflicts obvious
- should not look like a social calendar

#### Booking Request / Quote Flow

This should be a guided step flow, not a giant form.

Steps:

- choose facility or area
- choose time window
- choose use type
- headcount and equipment needs
- quote summary
- submit request

Each step should show:

- what the user is deciding
- what affects price or approval
- what is still provisional

#### Reservations / Requests

This page should read like a clean order history mixed with a schedule ledger.

Cards or rows should show:

- request state
- requested time
- facility / zone
- quote or invoice state
- next required action

### Technical Backing

Current backend truth is not enough for this to be real yet.
This page family depends on a real scheduling and booking layer above raw
ATHENA facility truth.

To make this page family truthful in Phase 3, it should gain:

- dedicated scheduling truth for availability blocks, holds, request state,
  quote state, and conflict checks
- ATHENA facility truth as input for facility, zone, hours, and closure data
- later ATHENA session and occupancy analytics as advisory context, not as the
  booking source of truth

Do not let raw current occupancy or a fake calendar stand in for reservable
availability.

## Member

### Purpose

The member shell is the emotional center of the product.
It should feel native on an iPhone or S25 class phone and be usable one-handed.

### Main Navigation

Bottom nav:

- `Meals`
- `Workouts`
- `Home`
- `Tournaments`
- `Settings`

The bar should be:

- floating
- slightly inset from the screen edges
- pill-shaped
- center-weighted on `Home`

Top-right:

- avatar button opening `Profile`

The top bar should be minimal.
The bottom bar should carry the main movement.

### Home

#### Purpose

This is not a feed.
It is a live personal control surface.

#### Visual Layout

Top row:

- facility indicator
- tap/check-in state chip
- avatar orb

Hero block:

- greeting
- current streak or continuity line
- the single best next action

Card stack below:

- `Today` workout or recovery card
- planner preview
- competition status card
- facility hours / closures card
- recommendation card

#### Vibe

- warm
- kinetic
- clear
- motivating without gamified clutter

#### Details

The tap state card should feel physical and immediate:

- `Checked in`
- `Not checked in`
- `E-tap available`

The `Today` card should feel like a launchpad:

- quick start workout
- resume current session
- preview planned session

The recommendation card should feel structured, not chatty:

- headline suggestion
- reason
- confidence or guardrail copy
- one action button

### Profile

#### Purpose

Profile is identity, not settings.

#### Visual Layout

Header:

- large avatar
- display name
- sport tags
- profile QR/share action

Middle:

- streaks
- achievements
- favorite modes or sports
- personal highlights

Lower:

- badge shelf
- milestone history
- future share-card preview

#### Vibe

- collectible
- proud
- personal

But still clean.
This should not become a noisy gamer profile.

### Workouts

This tab should have a top switcher:

- `Tracker`
- `Planner`
- `Coaching`

The switcher should feel like a segmented control anchored under the page title.

#### Tracker View

This is the most important current workout surface.

Visual structure:

- current workout card if a session is active
- quick-start from recent template
- workout history list
- progress strip with PRs or trend highlights

Workout detail should use expandable exercise cards:

- exercise name
- sets / reps / weight
- effort rating
- notes

Interaction style:

- tap to expand
- bottom sheet for editing
- sticky save / finish actions

The page should feel fast and editable, not form-heavy.

#### Planner View

This should look more deliberate and calmer than Tracker.

Visual structure:

- week timeline at top
- templates and loadouts carousel or list
- planned sessions by day
- exercise library browser
- equipment filter chips

Planner should feel like composing a week, not logging a live session.

#### Coaching View

This should not open as a chatbot.

It should open as a recommendation panel with:

- current focus
- conservative next-step suggestion
- why this suggestion exists
- alternate easier or harder route
- recovery guardrail copy

Future AI helper actions can live here as buttons:

- `Explain`
- `Make easier`
- `Make harder`
- `Adapt to no equipment`

### Meals

This tab should mirror workouts structurally:

- `Tracker`
- `Planner`
- `Guidance`

#### Tracker View

Future visual target:

- meal log cards by daypart
- quick add flow
- macro summary strip
- hydration and protein indicators if those become real

The feel should be:

- lightweight
- fast
- non-judgmental

#### Planner View

Future visual target:

- preferred cuisine chips
- budget and prep-time controls
- cooking ability indicator
- weekly meal plan blocks
- swap buttons for practical substitutions

#### Guidance View

Future visual target:

- structured recommendation card
- conservative suggestion framing
- restriction-aware variants
- disclaimers and doctor-check language

This should never read like a medical bot.

### Tournaments

This tab should feel more charged and competitive than the rest of the member
shell.

Top switcher:

- `Overview`
- `Queue`
- `Sessions`
- `Stats`
- `Standings`

#### Overview

Should feel energetic:

- next match or queue state
- recent result summary
- current momentum card
- joinable items

#### Queue

Should feel live:

- open queues
- estimated wait
- joined status
- quick join / leave state

#### Sessions

Should feel more factual:

- session cards
- bracket or matchup previews
- roster and timing details

#### Stats

Should stay self-focused:

- wins / losses
- rating trends
- per-sport metrics
- recent form

#### Standings

This is likely a later surface.
When it becomes real, it should feel like a proper ranking board, not a simple
table dump.

### Settings

This tab should feel quieter and more utility-oriented than the rest of the app.

Visual sections:

- account
- preferences
- facility
- help
- legal

Rows should be clean, grouped, and searchable if the page grows.

## Supervisor / Member+

### Purpose

Supervisor is a hybrid role.
They are still a member, but on trusted devices they unlock live operational
tools.

### Entry

On untrusted devices:

- drop into member shell or a reduced triage mode

On trusted devices:

- show an `Ops` entry point or mode switch

### Main Page

`Supervisor ops home`

This screen should feel like a live board, not a spreadsheet.

### Layout

Desktop:

- left rail for navigation
- center live board
- right detail drawer

Phone:

- stacked live cards
- segmented mode switch
- bottom sheet for details and interventions

### Core Pages

#### Live Board

Visual structure:

- occupancy strip at top
- active courts or zones in the center
- queue / session cards below
- issues or flags on the side

This page should feel like:

- mission control
- not enterprise bloat

#### Reconciliation

This page should feel analytical and careful.

Visual structure:

- facility selector
- window selector
- occupancy trend chart
- heat-map style bucket display
- anomaly or `inspect next` panel

#### Quick Match / Queue Controls

This page should feel compact and action-safe.

Actions might include:

- open / close queue
- remove queue member
- adjust match timing
- pause or archive session

The actions should be clearly separated from the informational board.

### Technical Backing

Current truthful backend is still missing the staff web runtime and staff role
model.
This role should be designed now, but only built honestly after role/authz and
staff runtime widening land.

## Manager

### Purpose

Manager is the scheduling and event-control role.
They should see the operational whole, not just live session triage.

### Main Page

`Manager ops home`

This should look like an operational canvas.

### Layout

Desktop:

- left rail
- large central schedule/calendar board
- right inspector
- top strip for facility status and open issues

Phone:

- agenda-first view
- triage cards
- critical alerts and approvals only

### Core Pages

#### Schedule / Calendar

This is the centerpiece page for managers.

Visual structure:

- day / week / month views
- facility and zone filters
- event layers
- closure overlays
- booking overlays
- conflict warnings

This page should visually answer:

- what is happening
- what conflicts
- what has capacity
- what needs intervention

#### Event / Tournament Studio

This should feel like a builder.

Visual structure:

- event metadata block
- schedule block
- facilities / zones block
- participant and format block
- publishing / draft state block

#### Booking Oversight

This should feel like a queue with calendar context.

Visual structure:

- incoming requests
- quote states
- decision panel
- calendar conflict preview

#### Analytics

This should feel lighter than the developer console but richer than supervisor
view.

Visuals:

- trend cards
- occupancy trends
- queue load
- event performance
- booking conversion later if real
- smart occupancy summary later only if it is grounded in real historical,
  session, and prediction truth

### Technical Backing

Current truthful backend already covers the role/capability boundary, facility
truth, and competition base.

To make manager analytics and scheduling real in Phase 3, the platform should
add:

- ATHENA Postgres-backed edge observation storage
- derived session facts for stay duration, repeat visits, and unmatched or
  anomalous entry/exit states
- bounded internal analytics reads by facility, zone, node, and time window
- a separate scheduling and booking substrate for calendar, request, quote, and
  conflict truth

Any later AI occupancy summary should sit above that substrate as a helper
surface, not as the source of the numbers or the first place the reasoning
lives.

Without those lines, occupancy trend cards and booking analytics would be
decorative rather than operationally useful.

## Developer / System Admin

### Purpose

This is the highest internal trust surface.
It is not meant to feel like the customer app with extra toggles.

It should feel like a platform console.

### Main Page

`Developer / system home`

This screen should feel like:

- service health wall
- audit window
- diagnostics board
- proposal / approval workspace

### Layout

Desktop only should be considered primary here.

Structure:

- left rail for system sections
- center diagnostics grid
- right-side deep inspector
- dense header with environment, facility, and runtime scope

### Core Pages

#### Service Health

Visual structure:

- service status cards
- health checks
- freshness / latency indicators
- degraded or stale banners

#### Audit + Routed Calls

Visual structure:

- searchable audit stream
- tool name chips
- caller identity chips
- outcome states
- latency and failure detail inspector

#### Contract / Manifest Explorer

Visual structure:

- repo/contract tree on the left
- selected contract detail in the center
- downstream impact panel on the right

#### Proposal / Approval Workspace

Visual structure:

- pending proposals
- diff summaries
- approver notes
- audit trail

This should feel deliberate and slightly severe.
Nothing in this area should look playful.

## Technical Layer Under The Visuals

### Backing Repos By Page Family

| Page family | Primary truth owner |
| --- | --- |
| member auth / workouts / planner / competition / profile | `apollo` |
| facility occupancy / hours / zones / closures / tap truth | `athena` |
| future staff reconciliation and operator reads | `hermes` |
| routed read tools and audit seam | `ashton-mcp-gateway` |
| shared events / manifests / narrow contracts | `ashton-proto` |

### Honest Constraint Layer

Do not visually promise these as real until the backend is ready:

- meal intelligence before `Tracer 25`
- staff ops truth before role/authz
- booking/calendar truth before a real scheduling layer
- manager analytics before real observation/session storage
- AI smart occupancy summaries before real observation/session storage and
  bounded analytics reads
- public competition / social surfaces
- chat as the core runtime

## Main Page Summary

If someone asked what the product should look like at a glance:

- the member home should feel like a polished athletic personal dashboard with
  a floating bottom nav and strong next-action cards
- the business home should feel like a premium booking workspace centered on
  schedule confidence
- the supervisor home should feel like a live operations board
- the manager home should feel like a scheduling and event-control canvas
- the developer home should feel like a platform console with audit and
  diagnostic density

## Next Step

The next useful pass after this document is one screen spec per page with these
labels only:

- `build now`
- `build over current backend truth`
- `needs backend widening first`
- `Phase 3 only`
