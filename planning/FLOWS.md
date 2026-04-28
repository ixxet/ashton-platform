# ASHTON Flow Truth

This file records expected UI and system behavior for flows that already exist
or are close enough to guide the next packet. It is for product debugging and
operator review: when a button, page, or backend action feels wrong, compare the
observed behavior to this file before changing code.

Repo-local tests and docs remain implementation truth. This file records what
the user should see, what the system should do invisibly, and how to classify
failures.

## Debug Classifier

| Symptom | Likely class | First place to inspect |
| --- | --- | --- |
| Button cannot be pressed | UI state, disabled rule, role gating | Frontend page/component and role payload |
| Button presses but no pending state appears | Frontend event/form wiring | Svelte form action or `use:enhance` handler |
| Pending appears but no backend request is recorded | Frontend server action or proxy/client wiring | SvelteKit server action, APOLLO client, mock request log |
| Backend request is rejected | Authz, trusted-surface, validation, stale version, conflict | APOLLO response status and error body |
| Backend changes but UI does not update | Frontend redirect/load/cache/render issue | Themis/Hestia load function and page state |
| UI updates but backend truth is absent | Mock drift or optimistic UI bug | E2E mock, frontend state mutation, APOLLO DB proof |
| Duplicate click creates duplicates | Missing pending state or idempotency | Button pending guard and `Idempotency-Key` path |

## Flow Template

Use this template when adding or changing a flow.

```md
## Flow Name

Surface:
Hestia / Themis / APOLLO / ATHENA / HERMES

Entry:
Where the user starts.

User-visible steps:
1. What the user sees.
2. What button or control they use.
3. What changes on screen.

Invisible system steps:
1. What the frontend sends.
2. What backend/service handles it.
3. What is persisted, emitted, or left unchanged.

Expected success state:
What the user should see after success.

Expected failure states:
- Button does not press:
- Button presses but nothing updates:
- UI updates but backend does not:
- Backend updates but UI does not:
- Duplicate click/retry:
- Auth/session failure:
- Conflict/stale version:

Backend truth:
Routes, tables, events, or service methods that should change.

Hard stops:
Things this flow must not do.
```

## Public Booking Request Intake

Surface:
Hestia public `/intake`, APOLLO public booking API.

Entry:
Anonymous customer opens Hestia `/intake`.

User-visible steps:
1. Customer sees request-only copy, public booking options, date/time controls,
   public-safe availability hints, contact fields, and purpose fields.
2. Customer chooses a requestable window or enters a requested time.
3. Customer submits a booking request.
4. The form shows a neutral received state and APOLLO receipt code, not a
   confirmed booking.

Invisible system steps:
1. Hestia loads APOLLO public options server-side.
2. After option/date selection, the browser creates explicit RFC3339 day bounds
   and Hestia loads APOLLO public availability server-side.
3. APOLLO returns only public-safe requestable windows and generic unavailable
   blocks for closed, booked, or unavailable time.
4. Browser-local date/time input is serialized to explicit RFC3339 values before
   Hestia submits to APOLLO.
5. Hestia sends `Idempotency-Key` and `X-Apollo-Public-Intake-Channel:
   public_web`.
6. APOLLO creates only a `requested` booking request and records source/channel
   truth.
7. APOLLO creates an opaque public receipt linked internally to the request.
8. APOLLO creates no schedule block on public submit.

Expected success state:
Customer sees that the request was received, gets a receipt code, and can move
to `/intake/status`.

Expected failure states:
- Button does not press: inspect Hestia form validation and disabled state.
- Button presses but nothing updates: inspect Hestia action and APOLLO client.
- UI updates but backend does not: inspect mock drift or missing APOLLO write.
- Backend updates but UI does not: inspect Hestia action response and render state.
- Duplicate click/retry: APOLLO public idempotency should prevent duplicates.
- Auth/session failure: should not apply; this flow is public.
- Conflict/stale version: public same-key different-payload conflict should stay
  neutral and not expose internal truth.

Backend truth:
`GET /api/v1/public/booking/options/{optionID}/availability` reads APOLLO
schedule truth and returns public-safe requestable windows plus unavailable
time hints only. It does not create a reservation or expose schedule block IDs,
request IDs, staff IDs, request counts, raw conflicts, contact data, internal
notes, private labels, or trusted-surface fields.
`POST /api/v1/public/booking/requests` writes a requested booking request and
public idempotency row, returns a public receipt code, and does not write a
schedule reservation.

Hard stops:
No payment, quote promise, confirmed booking, self-booking, public
self-edit/rebook, raw conflict details, schedule block ID, staff notes, private
labels, contact display, direct staff schedule controls, or trusted-surface proof.

## Customer Booking Status And Public Message

Surface:
Hestia public `/intake/status`, APOLLO public booking status API, Themis
internal booking detail for public-message staff input.

Entry:
Customer has a receipt code from public intake. Staff may optionally save a
public-safe message from Themis.

User-visible steps:
1. Customer enters the receipt code on `/intake/status` or follows the intake
   success link.
2. Hestia shows kind public status copy: Received, Under review, More
   information needed, Approved, Not approved, or Cancelled.
3. Hestia shows APOLLO's public message if staff saved one.
4. The page shows requested start/end and last updated time only.

Invisible system steps:
1. Hestia calls APOLLO status lookup server-side with only `receipt_code`.
2. APOLLO maps internal request status to public status:
   `requested -> received`, `under_review -> under_review`,
   `needs_changes -> more_information_needed`, `approved -> approved`,
   `rejected -> declined`, `cancelled -> cancelled`.
3. APOLLO omits request UUIDs, schedule block IDs, internal notes, conflicts,
   staff IDs, trusted-surface fields, quote/payment fields, and private contact
   data from the public response.
4. Themis manager/owner can save APOLLO `public_message` through trusted-surface
   staff APIs. Supervisors can only read it.
5. `public_message` stays separate from `internal_notes`.

Expected success state:
Customer sees a customer-safe request status, optional public-safe message,
requested window, and update time. Staff can update only the public message
from Themis when APOLLO returns a public receipt context.

Expected failure states:
- Unknown receipt: Hestia shows a neutral not-found message.
- APOLLO status API unavailable: Hestia shows retry-safe unavailable copy.
- Supervisor save attempt: APOLLO rejects through `booking_manage`; Themis does
  not render the save control.
- Missing trusted surface: APOLLO rejects the staff update.

Backend truth:
`apollo.public_booking_receipts` stores opaque receipt codes and optional
public-safe messages linked internally to booking requests. APOLLO remains the
status authority. Hestia is presentation only. Themis remains internal ops only.
Deployed truth is unchanged.

Hard stops:
No request UUID, schedule block ID, raw conflict truth, internal notes, staff
IDs, trusted-surface fields, private contact data, payment/quote/deposit/invoice
fields, self-confirmed booking, public self-edit/rebook, AI/LLM runtime, or
deploy claim.

## Staff Booking Request Edit And Replacement

Surface:
Themis `/ops/bookings/:requestId`, APOLLO staff booking edit and rebook APIs.

Entry:
Manager or owner opens an internal request detail page. Supervisors can read the
same detail but do not receive edit or replacement controls.

User-visible steps:
1. For `requested`, `under_review`, or `needs_changes`, staff edits the request
   fields and submits `Save request changes`.
2. Themis sends the form server-side with `expected_version` and trusted-surface
   proof.
3. APOLLO reruns schedule availability truth, increments the request version,
   and returns the same request with updated details.
4. For `approved`, staff uses `Create replacement request`.
5. Themis carries the hidden load-born idempotency key with the rebook form and
   redirects to the new requested replacement request after APOLLO accepts it.

Invisible system steps:
1. APOLLO rejects edit unless the request is still `requested`,
   `under_review`, or `needs_changes`.
2. APOLLO rejects rebook unless the original request is `approved`.
3. APOLLO hashes the rebook idempotency key with the original request and full
   payload, including `expected_version`.
4. Same rebook key plus same payload returns the same replacement; same key plus
   different payload conflicts.
5. APOLLO never creates a schedule block during edit or rebook. Approval remains
   the only path that creates a new reservation block.

Expected success state:
Pending/open edits keep the original request in review with a higher version and
fresh APOLLO availability truth. Approved rebook leaves the original approved
reservation untouched and creates a new `requested` replacement linked to it.

Expected failure states:
- Stale `expected_version`: APOLLO returns conflict and Themis keeps form values.
- Missing trusted surface or unsupported role: APOLLO denies the mutation.
- Missing rebook idempotency key: Themis fails locally before calling APOLLO.
- Same rebook key with changed payload: APOLLO returns conflict.

Backend truth:
APOLLO owns request state, version, availability truth, replacement lineage, and
rebook idempotency. Themis owns only the internal staff workflow. Hestia remains
customer-facing and separate. Deployed truth is unchanged.

Hard stops:
No public self-edit/rebook, in-place approved booking mutation, direct staff
schedule editing, payment/quote/deposit/invoice flow, Hestia staff controls,
AI/LLM runtime, or deploy claim.

## Staff Booking Request Create

Surface:
Themis `/ops/bookings/new`, APOLLO staff booking API.

Entry:
Manager or owner opens the internal new booking request form.

User-visible steps:
1. Staff sees internal intake fields and APOLLO availability/conflict copy.
2. Staff clicks `Create request`.
3. The button disables while pending and a creating status is shown.
4. On success, Themis redirects to the booking request detail page.

Invisible system steps:
1. Themis creates an idempotency key when the form is loaded.
2. The hidden form field carries the key through submit and validation failures.
3. Themis sends APOLLO trusted-surface headers, the session cookie, and
   `Idempotency-Key`.
4. APOLLO creates or replays one staff booking request for same-key/same-payload.

Expected success state:
Staff lands on `/ops/bookings/{requestId}` for the created request.

Expected failure states:
- Button does not press: inspect Themis `canManage` role gating and pending state.
- Button presses but nothing updates: inspect Themis action and APOLLO client.
- UI updates but backend does not: inspect mock APOLLO or redirect logic.
- Backend updates but UI does not: inspect Themis redirect and detail load.
- Duplicate click/retry: Themis pending state and APOLLO idempotency should
  prevent duplicate requests.
- Auth/session failure: Themis should redirect to `/login`.
- Conflict/stale version: same idempotency key with different payload returns a
  clear conflict.

Backend truth:
`POST /api/v1/booking/requests` writes a versioned request with staff attribution
and trusted-surface proof. It does not approve or reserve by itself.

Hard stops:
No public-source attribution, no anonymous submitter treated as staff, no
schedule reservation on create, no payment or quote promise.

## Staff Booking Decision

Surface:
Themis booking detail page, APOLLO booking transition API.

Entry:
Supervisor, manager, or owner opens a booking request detail page.

User-visible steps:
1. Supervisor can read status and details but cannot mutate.
2. Manager/owner sees allowed action buttons for the current state.
3. Clicking one decision disables all decision buttons while pending.
4. The page updates or redirects with the new state.

Invisible system steps:
1. Themis sends the selected transition and `expected_version`.
2. APOLLO checks role capability, trusted-surface proof, current version, and
   valid state transition.
3. On approval, APOLLO checks schedule availability and creates a linked internal
   reservation block.
4. Other transitions update booking state without creating reservations.

Expected success state:
The request state changes to the chosen valid state. Approval creates linked
schedule reservation truth.

Expected failure states:
- Button does not press: inspect role/capability and allowed-transition UI.
- Button presses but nothing updates: inspect Themis action route.
- UI updates but backend does not: inspect optimistic UI or mock drift.
- Backend updates but UI does not: inspect detail load after transition.
- Duplicate click/retry: pending-disabled state and APOLLO version checks should
  prevent double mutation.
- Auth/session failure: redirect to `/login`.
- Conflict/stale version: APOLLO returns conflict; UI should show a clear error.

Backend truth:
`/api/v1/booking/requests/{id}/{action}` updates a versioned booking request.
Approval also creates a linked `reservation` / `hard_reserve` schedule block.

Hard stops:
No supervisor mutation, no public customer mutation, no payment, no quote promise,
no owner policy edits, no direct staff schedule editing beyond approval-created
reservation truth.

## Approved Booking Cancellation

Surface:
Themis booking detail page, APOLLO approved cancellation path.

Entry:
Manager or owner opens an approved booking.

User-visible steps:
1. Staff sees `Cancel approved booking` when the request is approved.
2. Staff clicks the action.
3. Decision buttons disable while pending.
4. The request becomes cancelled.

Invisible system steps:
1. Themis sends cancel transition with expected version.
2. APOLLO locks and verifies the linked schedule block still matches the booking.
3. APOLLO cancels the linked schedule block and booking request in one
   transaction.
4. APOLLO retains the schedule block link for audit.

Expected success state:
The approved booking is cancelled and the linked schedule reservation is no
longer active.

Expected failure states:
- Button does not press: inspect role/capability and state gating.
- Button presses but nothing updates: inspect Themis action and APOLLO response.
- Backend updates but UI does not: inspect detail reload.
- Duplicate click/retry: APOLLO should reject stale or already-cancelled state.
- Linked schedule drift: APOLLO should reject as conflict.

Backend truth:
Booking moves to `cancelled`; linked schedule block is cancelled; linkage remains
for audit.

Hard stops:
No silent reconciliation of externally mutated schedule blocks, no deletion of
audit linkage, no customer self-cancellation.

## Bounded Staff Schedule Controls

Surface:
Themis `/ops/facilities/:facilityKey/schedule`, APOLLO schedule APIs.

Entry:
Supervisor, manager, or owner opens the internal facility schedule page.

User-visible steps:
1. Staff selects a bounded RFC3339 schedule window.
2. Supervisor can read one-off schedule blocks only.
3. Manager/owner can choose a supported intent and submit a single-instance
   closure, unavailable hold, maintenance hold, or public-event hold.
4. Manager/owner can cancel only visible future schedule-managed non-reservation
   blocks.
5. Submit buttons disable while pending and the page reloads from APOLLO truth.

Invisible system steps:
1. Themis calls APOLLO schedule reads server-side with the staff session.
2. APOLLO returns schedule truth and a manage-capability hint; Themis uses the
   hint only for controls, while APOLLO remains enforcement.
3. Themis sends schedule mutations only from server actions with trusted-surface
   proof.
4. APOLLO runs existing schedule conflict validation and version checks.
5. APOLLO refuses generic schedule cancellation for booking-linked reservations;
   approved booking cancellation stays under the booking request lifecycle.
6. Public availability continues to derive only public-safe unavailable/closed
   hints and never leaks private schedule details.

Expected success state:
The internal schedule list reflects APOLLO truth. Public availability changes
only through APOLLO's existing public-safe availability response.

Expected failure states:
- Button does not press: inspect Themis role gating, pending state, and
  APOLLO manage hint.
- Button presses but nothing updates: inspect Themis schedule action and APOLLO
  client.
- Backend updates but UI does not: inspect redirect/window query preservation.
- Duplicate click/retry: Themis pending-disabled state should prevent duplicate
  submits; APOLLO conflict rules remain authoritative.
- Auth/session failure: redirect to `/login`.
- Conflict/stale version: APOLLO returns conflict and Themis shows a bounded
  error without exposing private truth.

Backend truth:
`GET /api/v1/schedule/blocks`, `POST /api/v1/schedule/blocks`, and
`POST /api/v1/schedule/blocks/{id}/cancel` remain APOLLO-owned. Themis is a
privileged UI/client only.

Hard stops:
No recurrence, broad operating-hours policy editor, tournament/session controls,
supervisor proposal workflow, approved booking reservation mutation through the
schedule page, Hestia staff controls, public self-booking, payment/quote flow,
or deploy claim.

## Member Presence And Tap Claim

Surface:
ATHENA ingestion, APOLLO presence routes, Hestia home.

Entry:
Member taps in or uses the member-visible claim path where available.

User-visible steps:
1. Member sees current presence or recent visit summary in Hestia.
2. If a claim flow is shown, member submits the claim.
3. Hestia home reflects linked current/recent visit truth after APOLLO records it.

Invisible system steps:
1. ATHENA ingests tap identity and projects presence truth.
2. APOLLO exposes member-safe current and recent visit summary.
3. Claim flow links the authenticated member to an eligible external identity.
4. Hestia consumes APOLLO member-safe fields only.

Expected success state:
Member sees current or recent visit status without staff-only tap metadata.

Expected failure states:
- Button does not press: inspect Hestia form/control state.
- Button presses but nothing updates: inspect Hestia action/client.
- Backend updates but UI does not: inspect home load/render fields.
- Duplicate/replay: APOLLO claim idempotency and validation should reject or
  replay safely.
- Auth/session failure: redirect to login.

Backend truth:
ATHENA owns ingress/projection; APOLLO owns member-safe presence summary and
claim validation; Hestia only displays member-safe results.

Hard stops:
No raw tap hashes, no foreign member claim, no staff metadata, no direct booking
or payment coupling.

## ATHENA Recognized-Denied Testing Admission

Surface:
ATHENA edge ingress and owner CLI policy controls.

Entry:
TouchNet browser bridge posts a `fail` row with a populated name while an
explicit facility testing policy is active and
`ATHENA_EDGE_POLICY_ACCEPTANCE_ENABLED=true`.

User-visible steps:
1. Operator still sees the original TouchNet deny on the source system.
2. No extra workstation action is required in ATHENA for the tap itself.
3. Internal ATHENA history and analytics can later show that the source result
   was `fail` while accepted presence was still created through testing policy.

Invisible system steps:
1. ATHENA authenticates the node token and writes one immutable observation row.
2. ATHENA normalizes the fail as `recognized_denied`, `bad_account_number`, or
   `unclassified_fail`.
3. ATHENA evaluates the active facility or subject policy only for
   `recognized_denied`.
4. If admitted, ATHENA writes a separate accepted-presence row with explicit
   acceptance path and reason code.
5. Live occupancy, replay truth, and downstream publish follow accepted
   presence.
6. Current `edge_sessions` remain source-pass-only in this line.

Expected success state:
ATHENA history shows source `result='fail'` plus separate accepted presence
with `acceptance_path='facility_window'` or another explicit policy path.
Occupancy and publish reflect accepted physical presence without claiming
TouchNet passed it.

Expected failure states:
- No active policy or runtime flag off: observation is stored, but no accepted presence is created.
- `bad_account_number`: observation only; never admitted by testing policy in this line.
- Policy/acceptance write failure: source observation still remains immutable; acceptance is absent.
- Duplicate/replay: existing dedupe/projector rules still decide whether occupancy changes.

Backend truth:
`athena.edge_observations`, `athena.edge_access_policies`,
`athena.edge_access_policy_versions`, `athena.edge_identity_subjects`,
`athena.edge_identity_links`, and `athena.edge_presence_acceptances` change.
`edge_sessions` does not widen to policy-backed admissions in this line.
Subject-scoped policies take precedence over facility windows, active policies
cannot overlap inside the same scope, and identity links must use
runtime-validated privacy-safe keys.

Hard stops:
No rewrite from source deny to source pass, no name-based auto-merge, no public
report surface, no HTTP admin surface, and no session-duration claim for
policy-backed accepted fails.

## Hestia Member Session Entry

Surface:
Hestia `/login`, Hestia `/app/**`, APOLLO auth/session.

Entry:
Customer/member opens Hestia login or a protected member app route.

User-visible steps:
1. Unauthenticated `/app/**` redirects to `/login`.
2. Login verifies through APOLLO.
3. Authenticated user lands in `/app/home`.
4. Member navigation stays under member-safe sections.

Invisible system steps:
1. Hestia forwards auth/session requests to APOLLO.
2. APOLLO owns session cookie and profile truth.
3. Hestia server loads member-safe data for each app section.

Expected success state:
Authenticated member sees `/app/home` and can navigate member-safe sections.

Expected failure states:
- Login button does not press: inspect Hestia form state.
- Login request rejected: inspect APOLLO auth response.
- Backend session exists but UI loops to login: inspect cookie forwarding.
- UI shows staff controls: inspect Hestia section ownership drift.

Backend truth:
APOLLO owns auth/session/profile. Hestia owns customer-facing presentation only.

Hard stops:
No staff controls, no Themis route drift, no APOLLO admin UI, no payment/quote
surface.

## Placeholders To Fill As They Become Active

These are intentional placeholders, not implementation claims.

| Flow | Status | Notes |
| --- | --- | --- |
| Staff pending edit and approved replacement | Closed in Phase 3B.8 | APOLLO owns truth; Themis owns internal manager/owner workflow; public self-edit/rebook and in-place approved mutation remain deferred |
| Public availability/request calendar | Closed in Phase 3B.9 | APOLLO owns public-safe requestable/unavailable hints; Hestia renders them on `/intake`; submit remains request-only and staff approval remains the only confirmed reservation path |
| Bounded staff schedule controls | Closed in Phase 3B.10 | APOLLO remains schedule authority; Themis renders internal supervisor read-only and manager/owner bounded one-off create/cancel controls; recurrence, broad policy, tournament controls, supervisor proposals, public self-booking, and payments/quotes remain deferred |
| Competition command ops foundation | Closed in Phase 3B.11 | APOLLO owns shared competition command/outcome/readiness contracts plus service-backed CLI parity; Themis renders internal APOLLO-backed readiness, dry-run, accepted, denied, and unavailable states at `/ops/competition`; result trust closed in 3B.12 and analytics foundation closed in 3B.16, while OpenSkill read-path switch, tournament runtime, public competition surfaces, and game identity remain deferred |
| Competition lifecycle/result trust | Closed in Phase 3B.12 | APOLLO owns canonical result identity, recorded/finalized/disputed/corrected/voided facts, correction supersession, and finalized/corrected-only rating consumption; Themis renders internal APOLLO-backed lifecycle/result states without fake result truth; legacy rating foundation is closed separately in 3B.13, OpenSkill dual-run in 3B.14, and analytics foundation in 3B.16, while OpenSkill read-path switch, tournament runtime, public competition surfaces, and game identity remain deferred |
| Legacy rating foundation | Closed in Phase 3B.13 | APOLLO owns versioned `legacy_elo_like` rating math, golden cases, rating compute/policy/rebuild events, source result IDs, rating event IDs, and deterministic projection watermarks; UI may consume APOLLO projections only and must not compute rating truth; OpenSkill dual-run is closed separately in 3B.14, while OpenSkill read-path switch, ARES v2, analytics, tournament runtime, public competition surfaces, and game identity remain deferred |
| OpenSkill dual-run comparison | Closed in Phase 3B.14 | APOLLO owns internal OpenSkill comparison rows/events, legacy/OpenSkill deltas, accepted delta budgets, comparison scenarios, and delta flags over finalized/corrected canonical results; the active read path remains the legacy projection; UI must not compute rating truth and OpenSkill read-path switch, ARES v2, analytics, tournament runtime, public competition surfaces, and game identity remain deferred |
| ARES v2 proposal foundation | Closed in Phase 3B.15 | APOLLO owns explicit queue intent facts and internal match-preview proposal facts with server-computed match quality, predicted win probability, and explanation codes; ARES remains proposal-only and does not own result, lifecycle, rating, booking, or public competition truth; analytics foundation closed in 3B.16, while OpenSkill read-path switch, tournament runtime, public/member matchmaking, and game identity remain deferred |
| Competition analytics foundation | Closed in Phase 3B.16 | APOLLO owns internal derived competition stat events and analytics projections over finalized/corrected canonical results plus legacy active rating facts; projection version, confidence, sample size, computed time, source match/result, and deterministic watermarks are explicit; UI must not compute analytics truth and dashboard-first work, public profiles/stats/scouting, carry coefficient, OpenSkill read-path switch, tournament runtime, public/member competition surfaces, and game identity remain deferred |
| Internal tournament runtime | Closed in Phase 3B.17 | APOLLO owns staff/internal tournament containers, single-elimination bracket and seed facts, immutable team snapshots, match bindings, explicit advance reasons, audited round advancement facts, and tournament events over trusted APOLLO team/match/result truth; advancement consumes finalized/corrected canonical result truth only; UI must not compute bracket, seed, snapshot, result, or advancement truth; public tournaments, Hestia member/public expansion, booking/commercial/proposal workflow, OpenSkill read-path switch, ARES behavior changes, dashboard-first analytics, CP/badges/rivalry/squads, and game identity remain deferred |
| Public booking display labels | Deferred future | Public event/team labels only when facility policy and privacy rules allow them |
| Approved recurring booking self-service | Post-launch watch later | Customer can request or auto-confirm occurrences only inside staff-approved policy and conflict checks |
| Demand heatmaps and booking analytics | Post-launch watch later | Managerial insight from request/approval/occupancy patterns, not a booking prerequisite |
| Quote/payment handling | Planning-first | No public quote promise or checkout until policy and accounting rules are explicit |
| AI-assisted quote negotiation | Watch later | No autonomous binding quote, discount, or policy decision; escalation to staff required |
