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
1. Customer sees request-only copy, public booking options, requested time fields,
   contact fields, and purpose fields.
2. Customer submits a booking request.
3. The form shows a neutral received state and APOLLO receipt code, not a
   confirmed booking.

Invisible system steps:
1. Hestia loads APOLLO public options server-side.
2. Browser-local date/time input is serialized to explicit RFC3339 values before
   Hestia submits to APOLLO.
3. Hestia sends `Idempotency-Key` and `X-Apollo-Public-Intake-Channel:
   public_web`.
4. APOLLO creates only a `requested` booking request and records source/channel
   truth.
5. APOLLO creates an opaque public receipt linked internally to the request.
6. APOLLO creates no schedule block on public submit.

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
`POST /api/v1/public/booking/requests` writes a requested booking request and
public idempotency row, returns a public receipt code, and does not write a
schedule reservation.

Hard stops:
No payment, quote promise, confirmed booking, raw conflict details, schedule
block ID, staff notes, or trusted-surface proof.

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
fields, self-confirmed booking, public availability/request calendar,
edit/rebook, AI/LLM runtime, or deploy claim.

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
| Pending/approved edit or rebook | Deferred | Likely cancel-and-new-request unless narrower in-place proof is earned |
| Public availability/request calendar | Deferred future | May show facility hours, unavailable slices, public event labels, and requestable times; still no self-confirmed booking before staff policy earns it |
| Bounded staff schedule controls | Deferred | Themis/APOLLO rails for operational holds, delays, closures, or schedule edits if needed |
| Public booking display labels | Deferred future | Public event/team labels only when facility policy and privacy rules allow them |
| Approved recurring booking self-service | Post-launch watch later | Customer can request or auto-confirm occurrences only inside staff-approved policy and conflict checks |
| Demand heatmaps and booking analytics | Post-launch watch later | Managerial insight from request/approval/occupancy patterns, not a booking prerequisite |
| Quote/payment handling | Planning-first | No public quote promise or checkout until policy and accounting rules are explicit |
| AI-assisted quote negotiation | Watch later | No autonomous binding quote, discount, or policy decision; escalation to staff required |
