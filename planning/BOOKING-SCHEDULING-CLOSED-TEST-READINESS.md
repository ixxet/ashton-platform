# Booking/Scheduling Closed-Test Readiness

## Hours verification found two blockers; hours rolled back (2026-10-20)

The original **18:50:06 Toronto execution window has ended**. Further live
changes require a new approved window; source testing/publication continues.

The owner explicitly approved the 15 weekday-hours rules for 21 October through
19 November. They were applied at **22:33:34 UTC**, then real availability and
customer-browser checks found two defects. The approved hours-only rollback
cancelled exactly those 15 rules by recorded ID/current version at
**22:36:47 UTC**. No rows were deleted. There are **zero active hours, requests
or reservations**; the verified Owner, three resources, images, DNS and other
configuration remain. These findings supersede the earlier base-page/route
checks as the current launch verdict. **The booking launch remains blocked.**

- **APOLLO recurrence boundary:** the end date is converted to the next
  midnight but the loop treated that bound as inclusive. The rule ending
  Thursday 19 November exposed Friday 20 November 09:00–18:00. The local fix
  makes that midnight exclusive. A new five-case regression failed before the
  fix for the normal and clock-change boundaries and passes after it.
  [APOLLO `3bfbbe9`](https://github.com/ixxet/apollo/commit/3bfbbe9faf8f1bd0713bb4472cec11047ce8f0c7)
  publishes only the loop correction and five regression cases. Local checks
  passed: focused regression **1.664s**, full suite **199.919s**, uncached
  internal race suite **196.143s**, vet **0.666s**, build **1.924s** and diff
  check. There is no migration delta. Its [CI/image run](https://github.com/ixxet/apollo/actions/runs/39510385987)
  is in progress; an actual immutable image digest and fresh approval are
  required before deployment. The existing real Hestia/Themis/APOLLO/PostgreSQL
  request -> approval -> reservation -> status/cancellation browser rehearsal
  also passed against the repaired source (**8.376s**, explicit opt-in,
  isolated fixtures). That rehearsal bypasses the production proxy; it does
  not claim the live navigation defect is repaired.
- **Customer navigation:** the base HTML returns 200, but choosing a date uses
  SvelteKit's `/intake/__data.json` route, which the exact-path public proxy
  omitted. `/intake/status/__data.json` is also blocked. Both live data paths
  returned 404 while the underlying Hestia Service returned valid framework
  JSON. A private two-line amendment adds only these exact paths. It passed
  `nginx -t`, 14 isolated route/HTTPS/denial/tap checks and direct read-only
  checks of both real Hestia data endpoints. No live proxy/GitOps edit has
  been applied. The diff SHA256 is
  `a637e58dda8e2a88c6bb2159ff3547e62e3a5440d68ab28898312dd617acc1ca`.

A full page reload currently renders the selected day with no posted times;
that is a diagnostic fallback, not a claim that client navigation is repaired.
Do not create a fake request, change the approved date range to hide the bug,
or re-enable the hours on the old image. Prepare the repaired image plus
exact proxy amendment and the same hours for a renewed approval. No live
changes beyond necessary approved rollback may exceed the original 18:50:06
Toronto execution deadline without a newly approved window.

## Live portals and verified Owner access (2026-10-20)

This is the current execution ledger. The dated implementation, proposal and
R1 audit sections below remain historical evidence; their earlier "not deployed"
and approval-gap statements do not override this ledger. **External booking
acceptance is still open.** The portals, verified Owner access and three real
spaces are live. Opening hours remain unset.

The user approved the exact private rollout review and ten-path GitOps diff,
then explicitly approved the six-line HTTPS redirect amendment. The execution
window began **17:20:06 America/Toronto / 21:20:06 UTC** and ends
**18:50:06 / 22:50:06 UTC**. Approval covers the named images, DNS, application
Secret references, backup/restore checks, migration 033, the specified
mailbox-verified Owner account and the three real resources. Hours stay unset;
Flux stays suspended. Zizo is accountable owner; Codex is execution/rollback
operator. The frozen reviews, hashes, snapshots and exact identity remain
outside Git. No unrelated change or live data-loss restore is authorised.

### Deployed and checked

| Area | Executed result |
| --- | --- |
| Target | Existing `admin@talos-tower`, node `talos-p0d-y77`; application objects in `agents`, existing edge in `athena`. Management configuration unchanged. |
| GitOps | [Prometheus `aa2fa64`](https://github.com/ixxet/Prometheus/commit/aa2fa648c3ce16b1ee732b150801326b51cee093) published only the ten approved paths, including the HTTPS amendment. Seven unrelated dirty files were preserved byte-for-byte. Flux `apps` remains suspended. |
| APOLLO | Exact `6a10c55` image listed below is serving; server/init pins match. Migration 033 applied, ledger 001..033, one ready replica, health 200, no new-pod restarts. Rollout reached controller readiness in 20.385s. |
| Hestia / Themis | Exact `f9bbca0` / `3da3375` images from the cleanup ledger are serving, one ready replica each, static health 200. Recorded readiness intervals: 25.162s / 1.109s. |
| DNS / mail | All six approved DNS records created. Resend verified the sender domain and all four mail records; tracking remains disabled, TLS Enforced. Named SMTP/surface Secrets and mail ConfigMap created; existing database/session credentials preserved. |
| HTTPS edge | `nginx -t` passed in the existing proxy image. Only the two reviewed edge ConfigMaps and their Deployments changed. Both portals return HTTPS 200 and HTTP 308. Existing tap health stayed 200 after each edge restart. |
| Route boundary | All 12 external HTTPS route cases passed through both public-resolver checks and the Mac's normal resolver after refresh, plus two HTTP-to-HTTPS redirects. Book rejects staff/member routes; staff rejects public-intake/API routes. An empty, unauthenticated cross-origin schedule form returned 403 before any data submission. |
| Mail / Owner | The actual college-address sign-in request was accepted at 22:00:28 UTC and reported delivered by Resend. The user entered the code; the backend confirmed the exact verified identity and a real unrevoked session before the conditional Owner grant. Actual live browser reads of Schedule, Requests and internal intake succeeded after reload. Owner remains APOLLO-wide. No message body, sign-in code or cookie value was retrieved or forged. |
| Resources | Actual Owner/session CLI provisioned only Court, Studio and Fitness room at Ashtonbee, active and bookable. The public-options read returns exactly these labels. All three availability reads returned empty requestable/unavailable arrays; final counts were three resources, zero schedule blocks and zero booking requests. |
| Cookie / origin | Source policy is Secure, HttpOnly, SameSite Strict and no Domain attribute; live Secure-cookie/token-logging env values match the approved configuration. User-completed HTTPS login and subsequent authenticated browser navigation passed. Cookie attributes were checked in source/runtime, not by extracting a live cookie. |

The amended ten-path diff SHA256 is
`0821878f87dc99a975ca843433dd6be20289c11577a2717f413e3003e41fdcde`;
the six-line amendment alone is
`d14a22a63b77bc70d84c2b5827aeb923946ee2eb9c5d76fd4abd144c5d583732`.
Its per-host 308 condition uses Cloudflare's visitor
[X-Forwarded-Proto header](https://developers.cloudflare.com/fundamentals/reference/http-headers/#x-forwarded-proto).
Eleven local HTTPS/HTTP/missing-proto, route-denial, entry and existing-tap checks
passed before the separate amendment approval. No zone-wide HTTPS setting or
tap redirect policy changed.

A serving-pod verifier initially included an old completed APOLLO pod and
reported an image mismatch. Inspection identified the historical pod as
`Succeeded`, with an earlier completion date and no serving role. The corrected
check selects Running, Ready, non-deleting pods and confirmed the single new
serving image; no historical pod was removed or health gate waived.

### Independent backup and rehearsal

The physical PostgreSQL database is shared. To keep the approved data copy
within APOLLO's scope, the independent custom-format backup contains **only the
APOLLO schema**, excluding other applications. It remains outside Git with
directory 0700/file 0600: **312,435 bytes**, SHA256
`91d1889262d17b90e34301908c36efcb64698817d00cd3b2b3425511be1454da`.
It restored into isolated PostgreSQL 16 with the existing pgcrypto dependency.
Counts matched the live baseline: one existing user; zero sessions, resources,
schedule blocks, booking requests, rating rows or rating events. No personal
row data or credentials are published.

The restored ledger was exactly 001..032. The candidate applied only 033 in a
**0.352s migration-container invocation**, with unchanged counts and 33 ledger
entries. This measures the invocation, not live lock duration. The exact old
amd64 rollback image returned HTTP 200 and the public-options array on that
restored schema. No synthetic rows were added. Initial clone probes encountered
PostgreSQL listener timing and absent Docker host-port publication; the completed
compatibility check used the isolated internal network. All owned restore
containers/networks and temporary clone credentials were removed. The backup
is retained. No schema down or live database restore was performed.

The customer link initially failed because this Mac retained an earlier
negative DNS lookup. The configured resolvers subsequently returned the new
record; the Mac's own lookup still failed. An unprivileged cache refresh did
not clear it, and the privileged responder refresh required the user's own
administrator password. The user performed that step. Normal resolution and
all 12 HTTPS route checks then passed without an address override. A fresh
live browser tab loaded **https://book.lintellabs.net/intake**, showing exactly
Court, Studio and Fitness room with blank contact fields. No network, hosts
file or resolver configuration changed. The actual customer-page screenshot
is retained privately; no public booking was submitted.

### Remaining acceptance

- The authenticated schedule truthfully shows no activity. Its timeline rows
  derive from recorded hours, blocks and requests, so unused resources do not
  create empty rows before hours/activity exist. The existing advanced scope
  controls remain available; no fictional block was created to populate it.
- Real opening hours, an actual participant and an operational booking decision
  are required for external request -> approval -> reservation -> customer
  status/cancellation acceptance. The owner supplied **weekdays 09:00–18:00;
  weekends rentals only**. The exact additional live-hours review proposes
  15 resource-specific weekly rules for **21 October–19 November 2026**, with
  no regular weekend hours. That finite 30-day interval is a proposal, not a
  user-supplied date range. Its private JSON SHA256 is
  `54196316786a6d79784374c49b317dbc0966a4c06d031dbeab59d021261fee2b`.
  Hours remain unset pending that specific approval. The existing rental
  request/staff-decision flow is retained; no payment/rental category is added.
  Pending requests remain unreserved. No physical-phone acceptance is claimed;
  responsive browser tests remain separate evidence.

Final bounded checks at **22:10:03 UTC** reconfirmed one ready/updated replica
for all five changed Deployments, exact public labels and empty availability,
the verified Owner/session and suspended Flux. Private acceptance evidence and
the live staff screenshot remain outside Git. There are two users (the original
baseline account plus the actual verified Owner) and one session; no fixture
identity, request, reservation or hours were added.

The existing image/config rollback path is preserved with private before-state
and created-object/DNS IDs. It closes new portal ingress and restores only
approved changed objects/images; no automatic schema down or database restore.
The retained old APOLLO image has the known approval race, so staff writes must
remain closed if that image is restored. No destructive probe was run.

## Staff access and live rollout review — historical pre-approval checkpoint (2026-10-20)

The user approved **Owner** access for their specified account, **Resend Free**,
`book.lintellabs.net` for customer intake/status and `staff.lintellabs.net` for
authenticated staff scheduling. Owner is APOLLO-wide; the operator selected it
after that scope was disclosed. Exact personal account/email details remain in
the private review outside this public repository. The user completed provider
signup and supplied a credential for use. These choices resolve the earlier
provider/recipient/role decisions; they do not authorise the pending live rollout.

Resend now has sender domain `notify.lintellabs.net` in us-east-1, sending enabled
and receiving disabled. Open/click tracking is disabled and provider TLS is
Enforced. The domain is **Not Started** because its DNS has not been published.
A bounded SMTP authentication check against `smtp.resend.com:587` succeeded with
AUTH PLAIN over certificate-verified **TLS 1.3 in 0.313s**, within the configured
4s deadline. **No email was sent.** Authentication is not proof of domain
verification, provider delivery or inbox receipt.

Read-only target checks reconfirmed the old APOLLO image ready, no Hestia/Themis
Deployments, both existing edge Deployments ready and Flux `apps` suspended.
Authenticated Cloudflare inventory found all six proposed portal/mail DNS names
unused. Captured live edge ConfigMap data matches the preserved existing proxy
content and tunnel rules in the proposed additions. No DNS record, Kubernetes
Secret, live database/role/inventory, migration or deployment has been changed.
Resend sender registration/settings are the only provider changes completed.

Both exact frontend images in the cleanup ledger below were pulled and run
locally as UID 1000, with read-only root filesystems and dropped capabilities.
Their static health and entry pages returned 200 with APOLLO unreachable. The
new scoped GitOps drafts render successfully; APOLLO's init/server image pins
match, its database/session references and NATS remain intact, and there are no
duplicate environment names. This supplements prior CI/browser/lifecycle proof;
it does not establish external TLS, live sign-in or phone acceptance.

The private final approval review is
`/tmp/booking-deployment-review/mail-provider-review.md`, with exact DNS JSON,
ten-path GitOps diff, rendered manifests, image pins and artifact hashes in the
same folder. The executable scope is below. A single fresh approval covers its
conditional backup/restore preflight and rollout; a failing prerequisite stops
execution. No destructive live probe is required for ordinary deployment.
Operating hours remain unset until the owner supplies real hours. Online login
and portal access can be verified first; complete external booking acceptance
still needs real hours, a real participant and an actual booking decision.

## Local review cleanup (2026-10-20)

The user approved the interface and requested removal of fictional preview
data while retaining its formatting. The local startup helper now creates only
the user-specified **Court, Studio and Fitness room**. It no longer creates
requests, reservations, hours, holds, closures, staff accounts or captured
sign-in codes. Assumed resource zones were also removed. The previous review
contained four sample requests, six schedule blocks and one preview account;
there were zero additional booking requests. A private recovery copy was kept
outside Git before that disposable session was closed.

Fresh database verification found **0 requests, 0 schedule blocks, 0 users,
0 sessions and 0 verification tokens**, with exactly three configured spaces.
All three public availability reads returned empty requestable/blocked arrays.
Opening hours remain unset; an empty calendar is not claimed as availability.
The staff routes require an actual verified account and approved role. Local
verification start returns **503** until email delivery is configured, before
creating an account or claiming to have sent a code. This local guard is not a
deployed backend change. The preview staff session is no longer authenticated.

Hestia `f9bbca0` clarifies empty availability wording. Themis `3da3375` replaces
technical empty-queue copy with ordinary booking language and distinguishes
unavailable activity from a successfully loaded empty schedule. The published
changes leave styling, dependencies, API contracts, configuration and APOLLO
runtime source unchanged. Automated fixtures
remain isolated in the test suites and are not loaded into the user-facing
review. Pending requests still do not reserve time.

Both frontends passed check with zero errors/warnings, build and diff checks.
The customer intake browser suite passed **30 cases**; the full staff browser
suite passed **105 cases / 1 existing skip**. Initial sandboxed browser launches
could not bind loopback listeners (`EPERM`); the permitted reruns passed. The
customer view was refreshed and visually inspected with the same branding and
controls, empty posted availability and blank contact fields. Both open staff
views correctly returned to sign-in after removal of the preview identity.

Both linked GitHub workflows completed their test and image jobs successfully.
Direct GHCR index reads verified linux/amd64 and linux/arm64 for both:

| Source | CI result | Verified candidate |
| --- | --- | --- |
| [Hestia `f9bbca0`](https://github.com/ixxet/hestia/commit/f9bbca0d96144473d0907befe2bd004ac11d6ef2) | [39487507630](https://github.com/ixxet/hestia/actions/runs/39487507630): test + image SUCCESS | `ghcr.io/ixxet/hestia:sha-f9bbca0@sha256:f44b50520d0066c9a9dba1e539c8d2d92a700cdd500f5349fd1a6881df097931` |
| [Themis `3da3375`](https://github.com/ixxet/themis/commit/3da337537f56fb753597e4794110c2bfab01b0b7) | [39487544445](https://github.com/ixxet/themis/actions/runs/39487544445): test + image SUCCESS | `ghcr.io/ixxet/themis:sha-3da3375@sha256:0fe93b083518a3e18d2b18b311e4fa8c06775b1a8a08f49a59372a405aa86a02` |

These frontend candidates supersede the initial interface-release images below.
The rendered and local unapplied frontend manifests use the new pins. APOLLO
remains the previously verified `6a10c55` candidate; no backend source changed.

No live data, deployment, DNS, mailbox/provider setup or staff role was changed
by that cleanup checkpoint. Real operating hours, staff access, mail delivery
and exact rollout approval remain required before external use. The current live acceptance proposal below
also removes its fictional public canary space, hours and contact. Earlier R1
fixture examples remain historical test plans, not authority to populate a
customer-facing calendar with sample data.

## Booking interface release (2026-10-20)

The user authorised reworking the actual customer and staff interfaces using
Centennial Documents house-style inspiration. This release replaces the earlier
discussion-only layout with runtime code; it does not authorise live rollout,
resource provisioning, institutional email access or a calendar integration.
Earlier R1/hardening results and image candidates below remain dated evidence.

### Implemented interface

- **Hestia:** ASHTON masthead, charcoal/lime/Arial styling, schematic space
  drawings, visual space choices, week/date selection, native clock plus
  duration, posted availability, retained contact draft and a live request
  summary. Exact custom start/end controls and receipt/status remain available.
  Availability navigation retains the original loaded idempotency key. Public
  and member/staff boundaries are unchanged.
- **Themis:** matching staff sign-in and shell; sign-in opens the configured
  facility schedule rather than the ATHENA-dependent overview. The shared
  resource timeline reads existing APOLLO calendar occurrences and booking
  requests. It displays recurring hours/closures, linked confirmed reservations
  and separate lanes for overlapping pending requests. Pending requests remain
  unreserved. Failed calendar/queue reads are explicitly unknown.
- Time-window selection by pointer/drag or keyboard prepares existing one-off
  hold/closure form values without writing. Native date/time controls and exact
  offset controls remain available. A manager/owner must submit the existing
  server action; supervisors remain read-only. Existing reservations open their
  request lifecycle for approval/cancellation; they are not dragged into an
  in-place reschedule. Local gaps/folds are rejected; cross-midnight requests
  reproject when changing days.
- The staff requests page has local search and All activity / Needs review /
  Confirmed views. No new dependencies, public API, migration, proxy allowlist,
  build/CI configuration or runtime backend change was needed.

The user specified **one Court, one Studio and one Fitness room at Ashtonbee**.
The initial local review used those three resources, synthetic requests, hours,
closures and a confirmed booking in disposable PostgreSQL. No half/split court
option or resource edge was added. Runtime customer options come from APOLLO;
staff rows derive from authorised calendar/booking activity rather than an
invented resource catalogue. An unused resource with no hours/activity may not
appear on that timeline. Nothing here establishes live inventory or operating
hours. Live inventory and hours still require owner confirmation and the
acceptance approval below.

The design borrows the shared availability-grid interaction described by
[Microsoft's Scheduler guidance](https://support.microsoft.com/en-US/teams/meetings/schedule-a-meeting-in-microsoft-teams).
It has no Microsoft/Apple calendar connection. The charcoal/lime/Arial treatment
uses the local Centennial Documents house style; it is not an official brand
certification, and no institutional logo was copied or redrawn.

### Locally executed verification

Hestia: `npm run check`, `npm test` **36 passed**, `npm run build`, and full
`npm run test:e2e` **52 passed**. Themis: check, **42 unit passed**, build, and
full browser verification **105 passed / 1 existing skip** across Chromium
and WebKit phone projects. The tests cover clock/duration payloads, retained
fields/key when switching spaces, selection without a POST, authorised hold
creation, overlapping pending lanes, recurring occurrences, unknown queue
state, day navigation/overnight windows, DST gap/fold refusal and 320px layouts.
Both working trees passed `git diff --check`. No lockfile or dependency changed.

Real built adapter-node applications and actual APOLLO HTTP/services passed:

```bash
APOLLO_BOOKING_FRONTEND_REHEARSAL=1 go test -count=1 -v ./internal/server -run '^TestBookingClosedTestFrontendsRealPostgres$'
```

Final run: **6.23s test / 6.521s package**, with one pending request and zero
reservations, then one approved request/linked reservation, then linked
cancellation and customer status/message. Synthetic verified staff used actual
session cookies; no visit/tap records changed. Captured fixture email and the
localhost Secure-cookie exception do not prove provider delivery or external
TLS. APOLLO's only source change in this interface release updates two browser
selectors in that existing rehearsal; its backend runtime is unchanged.

The initial staff browser pass was **91 passed / 6 failed / 1 skip**. It exposed
local-date projection/SSR refresh errors and a controlled exact-time panel
closing during editing, plus implicit timezone assumptions in the new tests.
Those were corrected with explicit reactive date inputs, retained panel state
and explicit fixture timezone boundaries. Four subsequent DST cases initially
failed to locate a select by its exact label; the role-based combobox selector
reaches them. A cold hydration timing case led to disabling client-only
controls until their handlers are ready. Final focused DST cases passed **4/4**;
no assertion, role denial or test was skipped to get the final full pass.
Raw intermediate logs remain in `/tmp/booking-redesign-themis-initial-e2e.log`,
`/tmp/booking-redesign-themis-dst-selector-failures.log` and the final logs under
`/tmp/booking-redesign-*`. These local paths are evidence locations, not public
links or deployment artifacts.

The initial local review served actual source on `http://127.0.0.1:8824/intake` and the
staff workspace on `http://localhost:8825`, backed by disposable APOLLO/
PostgreSQL. Its captured sign-in code was kept outside Git, and no real
email was sent. That session was replaced by the empty review described above.
It was local review, not external acceptance. Desktop
customer/staff screenshots and narrow-phone renders were inspected; schedule
horizontal scrolling stays inside its panel.

### Source publication and deployment boundary

All three scoped source commits are on GitHub main. Each linked CI run
completed both test and image jobs successfully. Direct GHCR index inspection
verified **linux/amd64 and linux/arm64** for each immutable candidate:

| Source | CI result | Verified candidate |
| --- | --- | --- |
| [Hestia `9f2c276`](https://github.com/ixxet/hestia/commit/9f2c276b5bd7f55e10bc71ef1ae8835b80bacb24) | [39482731740](https://github.com/ixxet/hestia/actions/runs/39482731740): test + image SUCCESS | `ghcr.io/ixxet/hestia:sha-9f2c276@sha256:e7c19f5d1856c89f3753b39e00e07c317497c7547e5bcfeaa584f7082a58ab79` |
| [Themis `7056091`](https://github.com/ixxet/themis/commit/7056091bfec64037b54696a762aa525b045ed0c8) | [39482977228](https://github.com/ixxet/themis/actions/runs/39482977228): test + image SUCCESS | `ghcr.io/ixxet/themis:sha-7056091@sha256:705ff2a48a2fd4475dd0eca4c1fd29a8a3db6a8cd6d37197c87c40362650d3df` |
| [APOLLO `6a10c55`](https://github.com/ixxet/apollo/commit/6a10c552fd233cd79a3d588dde064198269c7333) | [39482734765](https://github.com/ixxet/apollo/actions/runs/39482734765): test + image SUCCESS | `ghcr.io/ixxet/apollo:sha-6a10c55@sha256:838ac17b26cc7e9d0bb3d62e31b82425fc7e573bbfb49a846ca6c98e0299aad8` |

These candidates supersede the pre-design ledger below for the proposed UI
rollout. The folded deployment review and local unapplied image references
now use these pins; no live object or GitOps file was changed. APOLLO has only
rehearsal-selector changes relative to its earlier runtime candidate.

The fresh read-only publication check still found APOLLO at the old deployed
`sha-6b27618` digest, no Hestia/Themis Deployments, `apps.spec.suspend=true`, and
no installed image-update-automation resource type. No live mutation, DNS,
mail/provider setup, Secret read/write, migration, rollout, restart or
Prometheus change/push was performed. Staff roles, mail/domain verification,
independent backup/restore and fresh exact deployment approval remain open.

## Earlier booking hardening follow-up (2026-10-20; before interface release)

This section supersedes the original R1 next-packet/readiness rulings below.
The original G0-G4 findings, failed commands and publication evidence remain
dated history. The user authorised booking/scheduling implementation, local
verification and scoped publication, then explicitly approved frontend
Dockerfile/ignore/CI additions and standard SMTP support. Live changes still
require review of the exact deployment package. No live approval was given.

The user also chose **pending requests remain unreserved**. A request, including
one under staff review, is not a tentative reservation. Approval creates the
linked reservation; approved cancellation releases it and retains history.
Existing staff schedule holds are a separate blocking schedule object, even
when their effect is named `soft_hold`. No new customer hold/expiry policy,
instant booking, payment, presence dependency or competition expansion is added.

### Implemented fixes and evidence

| Owner / source | Reproduced gap | Result |
| --- | --- | --- |
| Hestia `fec132e` | Malformed successful receipt/status payloads escaped as route errors; failed retries could lose fields/key or reuse stale hidden timestamps | Neutral failures preserve the original fields/key; successful receipts remain visible through options failure. Hidden instants clear on invalid conversion; missing keys, DST gaps and ambiguous folds are rejected. Public server calls reject redirects and use a 5s deadline. |
| Themis `8bfc368` | New-request load errors falsely claimed read-only permission; stale example dates and hard-coded facility navigation; redirecting transport could forward proof | Explicit unavailable/malformed/unsupported-role states; manage controls appear only after verified capability. Failure retains fields/key, dates are required, facility is configured, and redirect denial plus 5s/caller cancellation are tested with real HTTP. |
| APOLLO `7fb1e7c` | Simultaneous distinct approvals created two conflicting reservations | A transaction advisory lock per facility precedes the schedule snapshot/conflict check/insert. Same-resource, parent/child and exclusive-resource races now yield one approval/one conflict/one reservation. Adjacent windows both succeed. A cancelled lock wait leaves the request unchanged. No migration/public API added. |
| APOLLO `0f2268e`, `c25cc84` | Docker occasionally returned an empty published port; pgx then used unrelated port 5432 | Request an explicit ephemeral binding, inspect/validate it within the shared 2-minute startup budget, and fail with bounded state diagnostics. Three concurrent real databases prove usable ports. Running non-OOM containers with `PublishAllPorts=true` and no actual bindings were captured; waiting alone was insufficient. |
| APOLLO `a86ea20` | No complete real-frontend or migration/old-image/restore rehearsal | Explicit opt-in tests run actual adapter-node Hestia/Themis, APOLLO HTTP/services and disposable PostgreSQL; a separate fixture exercises schema 032 -> 033, the exact previous live APOLLO image and dump/restore. |
| APOLLO `9aa43b8` | Verification defaulted to a no-op sender; token logging was not delivered mail | User-authorised standard-library SMTP supports mandatory STARTTLS or implicit TLS, certificate/hostname verification, TLS 1.2+, protected AUTH PLAIN, a total deadline up to 4s, cancellation and redacted errors. SMTP refuses verification-token logging. Local encrypted fixtures include generated-code/hash consistency. Provider configuration and inbox acceptance are still unproved. |
| Hestia `bd65408`, Themis `ed81467` | No tracked frontend container or image workflow | Approved pinned Node 22 multi-stage builds run check/unit/build, then serve adapter-node as `node`. Main CI gates amd64/arm64 GHCR publication on check/unit/build/browser tests. Static `/_app/version.json` health stays available during an APOLLO outage. No deploy step. |
| Hestia `3882621` / Themis runtime test fixes | Dated fixtures, ambiguous selectors and interactions before hydration blocked full-suite CI | Explicit fixture dates, selectors, hydration readiness and awaited settings-save result preserve the original assertions. Initial failures remain in the verification history below. |

The scheduling lock covers application schedule creation, not arbitrary SQL or
older APOLLO processes. A mixed old/new deployment or rollback must keep staff
writes closed until every serving process has the intended implementation.
Facility-level serialisation deliberately favours correctness over maximum
parallel approval throughput for this closed test.

### Locally executed proof and limits

Both frontends passed `npm run check`, `npm test`, `npm run build` and full
`npm run test:e2e`: Hestia 36 unit / 46 browser cases; Themis 40 unit / 89 browser
cases with one existing skip. Hestia's later four-line CI fixture correction
also passed check and all 46 browser cases locally. Both actual Docker builds
passed and ran as `node`; static health and the entry page returned HTTP 200
with APOLLO deliberately unreachable. Final backend/SMTP and GitHub outcomes
are recorded in the publication ledger in this follow-up.

Before SMTP was added, the final frozen APOLLO matrix passed:
`go test -count=1 ./...` (225.26s), `go test -count=1 -race ./internal/...`
(211.41s), `go vet ./...` (0.67s), `go build ./cmd/apollo` (1.88s) and
`git diff --check`. That earlier result is not SMTP verification; the final
post-SMTP published-source matrix is below. Focused SMTP/config/CLI tests with
`-race` passed in 1.648s / 1.172s / 1.428s. Full current results are recorded below.

Real frontend reproduction, with Docker, built sibling frontends and existing
Playwright browsers:

```sh
APOLLO_BOOKING_FRONTEND_REHEARSAL=1 go test -count=1 -v ./internal/server \
  -run '^TestBookingClosedTestFrontendsRealPostgres$'
```

Latest execution passed in 7.738s. A real browser submitted Hestia options /
availability / request / receipt / status, signed into actual Themis through
APOLLO verification, navigated from the degraded overview to Requests, saved a
public message, approved and cancelled. SQL checkpoints were request=1 /
reservations=0, approved=1 / reservations=1, cancelled=1 / cancelled
reservations=1. Visits and tap-links remained unchanged. ATHENA was absent.
The test sender captures email rather than delivering it; localhost Secure
cookie handling is not external TLS/origin/cookie proof. Single-repo CI skips
this explicit opt-in test when siblings/browsers are absent; a skip is not proof.

Migration/old-image/restore reproduction:

```sh
docker pull --platform linux/amd64 \
  ghcr.io/ixxet/apollo:sha-6b27618@sha256:6c2d4ef8c67d0ea21d3b41963eb9709d0114b53bfea56c30d456cafbb677515a
APOLLO_BOOKING_DEPLOY_REHEARSAL=1 go test -count=1 -v ./internal/server \
  -run '^TestBookingSchema033OldImageAndRestore$'
```

Passed in 6.63s. Actual schema 001..032 plus two synthetic four/five-match
rating rows and a retained historical event upgraded with actual 033 SQL in
4.626917ms: one provisional, one ranked, historical event retained. The exact
old amd64 image served nonempty public options and accepted a new booking
against schema 033. A 311431-byte custom dump restored into a separate
`booking_restore` database and retained one request and one resource. Dump SHA256:
`a63f58f712086720a71699c870ebdd0e02d892cfa61a72e6c5e276f662780a73`.
The old image has no arm64 manifest; the proof used explicit amd64 emulation.

This is a small populated synthetic SQL-delta/booking-path rehearsal, not a
production lock-time measurement, full old-image competition rollback proof,
actual live initContainer/migration-ledger execution, or independent live
backup. Migration 033 down deletes rating-wrapper events and is not routine
rollback. No new migration was introduced by this follow-up.

Raw local evidence remains under `/tmp/booking-*.log` and result JSON files;
it is not committed test output. Earlier matrix failures are retained:
`booking-apollo-results.json` had focused/full fixture connection failures;
`booking-apollo-complete-results.json` had a 214.83s full-suite failure caused
by the initial 5s published-port budget. Later post-SMTP full/race runs failed
in 203.52s / 199.37s: running containers sometimes never published a port, and
the consumer fixture observed its first visit commit before linked writes
finished. `c25cc84` requests explicit ephemeral ports and waits for the handler
result; it changes no presence runtime. The diagnostic full run was deliberately
stopped after capturing six running, non-OOM, permanently unbound containers
(`/tmp/booking-postgres-port-diagnostics.json`); its interrupted/cleanup errors
are not a pass. All owned diagnostic containers were removed. The final frozen
published-source matrix below passes without serialising the requested package
commands. Initial frontend fixture/selector failures and local rehearsal
selector/table-name corrections were not hidden. Hestia GitHub run
[39464799179](https://github.com/ixxet/hestia/actions/runs/39464799179) passed all
booking cases but failed two member-shell timing cases (44 passed / 2 failed),
so no image was published by that run; `3882621` corrects those test waits.

### Final published-source verification and image ledger

Final APOLLO source is `7e6680e22eb854b97f32e2e5e0353a96e67d792f`, including
SMTP `9aa43b8` and fixture/consumer verification repair `c25cc84`. Its frozen
local matrix passed without changing the requested commands:

| Command | Final result |
| --- | --- |
| `go test -count=1 ./...` | PASS, 203.86s |
| `go test -count=1 -race ./internal/...` | PASS, 204.02s |
| `go vet ./...` | PASS, 0.62s |
| `go build ./cmd/apollo` | PASS, 1.76s |
| `git diff --check` | PASS, 0.02s |

Results and raw output: `/tmp/booking-apollo-published-results.json` and
`/tmp/booking-apollo-published-{full,race,vet,build,diff}.log`. Focused final
`go test -count=1 -race ./internal/testutil ./internal/auth ./internal/config ./internal/consumer`
also passed (4.055s / 22.417s / 1.394s / 11.516s). No SMTP messages were sent to
a real provider. All diagnostic/review containers and the review network were
removed; the final test run left no labelled PostgreSQL fixture containers.

| Repository / verified remote main | GitHub CI | Published immutable image candidate |
| --- | --- | --- |
| [APOLLO `7e6680e22eb854b97f32e2e5e0353a96e67d792f`](https://github.com/ixxet/apollo/commit/7e6680e22eb854b97f32e2e5e0353a96e67d792f) | [39468231070](https://github.com/ixxet/apollo/actions/runs/39468231070): test and image SUCCESS | `ghcr.io/ixxet/apollo:sha-7e6680e@sha256:160ce351240e909bfbd2023232ab7ba08b193f65820eea6ec33ed9bae083ea1b` |
| [Hestia `3882621d0c0b5c57acc4f0ab2f64abd56cd06a3f`](https://github.com/ixxet/hestia/commit/3882621d0c0b5c57acc4f0ab2f64abd56cd06a3f) | [39466037826](https://github.com/ixxet/hestia/actions/runs/39466037826): test and image SUCCESS | `ghcr.io/ixxet/hestia:sha-3882621@sha256:2281fd6c30fda5df9b484859d88e3cf5166112b46aea4a3e679c267455b02d6f` |
| [Themis `ed814673620a441936a7978971f4da4cc6e3aec6`](https://github.com/ixxet/themis/commit/ed814673620a441936a7978971f4da4cc6e3aec6) | [39464801105](https://github.com/ixxet/themis/actions/runs/39464801105): test and image SUCCESS | `ghcr.io/ixxet/themis:sha-ed81467@sha256:f64bbe026ad5e59e3c143b20acf2e51151ab97e8a948d0fe2012b64ffb8f282b` |

Each registry index was inspected directly with `docker buildx imagetools
inspect`; all three contain amd64 and arm64 runtime manifests. Actual remote
main SHAs were verified separately with `git ls-remote`, not cached origin refs.
These are published candidates, not deployed images. ATHENA and Prometheus
source/deploy state are not changed by this follow-up.

The two **published frontend images** were also pulled and run on default port
3000 with read-only root filesystems, dropped capabilities and non-root `node`,
while APOLLO was unreachable: static health and entry pages returned 200. The
rendered edge proposal passed `nginx -t` using the existing live proxy's exact
image digest `nginx@sha256:65645c7bb6a0661892a8b03b89d0743208a18dd2f3f17a54ef4b76fb8e2f2a10`
(amd64 emulation locally). Fourteen host/path checks proved the separate entry
redirects, intake/status/static/login routes and private/member/public-API
denials. Access logging omitted the synthetic receipt query marker. Tracked
tap configuration remains byte-for-byte at the start of the proposed config
(SHA256 `7593e37633aeb9b78ca2cfb1386260950c1a83b7ff77f834a8e15d8c225e2d58`).
Local port-80 frontend stand-ins represented Kubernetes Services for routing
checks; no Kubernetes Service, external TLS/DNS or actual staff login was
exercised by that proxy test. The earlier actual-APOLLO browser rehearsal is
separate proof. Raw proposal/wiring evidence is in
`/tmp/booking-deployment-review/local-wiring-proof.json`; rendered proposals are
embedded below so they remain reviewable on GitHub.

Post-publication bounded read-only safety refresh at **2026-10-20 18:05:39 UTC**
reconfirmed the old desired APOLLO `sha-6b27618` and ATHENA `v0.8.2` digest pins,
one ready replica each, suspended `apps`, no Hestia/Themis Deployments and no
image-automation resources. APOLLO init/server pins also still match, with only
the existing database/HTTP/NATS/session env names. No live Secret value,
mutation, migration, rollout, restart, DNS change or Prometheus edit/push was
performed. Unrelated working-tree changes remain preserved.

### Original customer/staff design discussion (historical)

Recommended entrances on the existing domain are `book.lintellabs.net` for
customer intake/status and `staff.lintellabs.net` for the authenticated staff
portal. Customers receive the booking URL; staff open Requests/Schedule inside
their portal. `book.staff.lintellabs.net` would only be needed for a deliberately
separate staff booking application. Hostnames are proposals, not created DNS or
approved exposure.

The local interactive concept compares a large native start-time plus duration
picker with requestable time slots. A separate staff resource timeline shows
existing reservations, unavailable staff holds/closures, and a dashed pending
request overlay that does not reserve time. It demonstrates conflict refusal,
approval, existing-booking selection and linked cancellation with synthetic
data. It does not reschedule an approved booking in place or send requests.
The customer concept's single submitted request cannot overwrite its saved
reservation. Staff and customer views stay separate in the runtime.

At this earlier checkpoint the design was discussion-only. The interface release
above supersedes that layout; no Microsoft/Apple calendar integration is added.
Browser QA covers submission/approval/cancellation,
conflict refusal, private-note separation and no horizontal overflow at 320,
736 and 1024px in light/dark appearance. The visual is returned in the chat.

### Reviewed live approval package — historical proposal

Target: existing `admin@talos-tower`, node `talos-p0d-y77`; APOLLO and frontends
in `agents`, existing proxy/tunnel in `athena`. Retain authenticated management
SSH and the per-command Talos API override; no kubeconfig/topology changes.
Execution window: **one 90-minute window beginning at explicit approval on
2026-10-20**. Recheck source/target/config/DNS at its start; refresh the review
before execution if approval arrives on another date. Stop at the budget except
necessary ingress closure/rollback. Zizo is accountable owner; Codex is executor
and image/config rollback executor. This is not a scheduled unattended job.

Exact immutable images:

| App | Proposed image |
| --- | --- |
| APOLLO init and server | `ghcr.io/ixxet/apollo:sha-6a10c55@sha256:838ac17b26cc7e9d0bb3d62e31b82425fc7e573bbfb49a846ca6c98e0299aad8` |
| Hestia | `ghcr.io/ixxet/hestia:sha-f9bbca0@sha256:f44b50520d0066c9a9dba1e539c8d2d92a700cdd500f5349fd1a6881df097931` |
| Themis | `ghcr.io/ixxet/themis:sha-3da3375@sha256:0fe93b083518a3e18d2b18b311e4fa8c06775b1a8a08f49a59372a405aa86a02` |

The exact proposed nonsecret Prometheus diff is privately reviewable at
`/tmp/booking-deployment-review/gitops-scoped.diff`, SHA256
`2551f21f8a4b67d36db82f7dcf657c1156e062edc3d31dac5c9fea3e17f87b24`,
against `8020388`. It contains only ten named paths: frontend Deployment/Service
and Kustomization files for Hestia/Themis, the agents Kustomization entries,
APOLLO's booking patch/mail ConfigMap/Kustomization, and the two existing edge
ConfigMaps. These are draft copies outside the repository. Final approval
includes copying/committing/pushing only that reviewed delta and applying only
its named objects. Preserve all seven unrelated dirty Prometheus files.
**Keep Flux apps suspended; do not reconcile the pending apps tree.**

| Object / field | Proposed change |
| --- | --- |
| `agents/deployment/apollo` | Set both `apollo` and `apollo-migrate` to the identical pin above; preserve database/session references and NATS. Existing init runs pending 033 via `migrate up`. No optional ATHENA bridge configuration. |
| APOLLO session/proof env | `APOLLO_SESSION_COOKIE_NAME=apollo_session`, `APOLLO_SESSION_COOKIE_SECURE=true`, `APOLLO_LOG_VERIFICATION_TOKENS=false`; trusted mappings from Secret `ashton-booking-surface`, key `apollo-trusted-surfaces`, preserving other entries. |
| APOLLO mail | ConfigMap `ashton-booking-mail`: `host=smtp.resend.com`, `from=ASHTON <signin@notify.lintellabs.net>`. Secret `apollo-smtp`: username `resend`, password from the supplied protected provider key. STARTTLS / 587 / 4s. |
| New Hestia Deployment/Service | One replica; `agents-ghcr`; HOST 0.0.0.0, PORT 3000, ORIGIN `https://book.lintellabs.net`, APOLLO origin `http://apollo.agents.svc.cluster.local`; Service 80→3000. No staff proof or SMTP secret. |
| New Themis Deployment/Service | Same pod/service basics; ORIGIN `https://staff.lintellabs.net`, default facility `ashtonbee`, trusted surface `themis`; matching token from `ashton-booking-surface/themis-token`. |
| Frontend pod security/health | UID/GID 1000, non-root, read-only filesystem, no privilege escalation, all capabilities dropped, no service links/token automount. Requests 50m CPU/64Mi, memory limit 256Mi; static `/_app/version.json` probes. |
| Edge ConfigMaps | Preserve current tap server, tunnel UUID/credential path and existing tap rule. Add only the two portal host servers/rules before terminal 404. Validate and restart only `athena-edge-proxy` and `athena-edge-tunnel`; tap regression aborts. |

The SMTP credential is protected outside Git and has not been put in a live
Secret. The trusted surface proof is generated securely only if absent; reuse
an existing matching entry without rotation, and stop on conflicts. Preserve
`apollo-runtime/database-url` and `apollo-runtime/session-cookie-secret`.
No credential values belong in public manifests, command arguments or logs.
The college mailbox only receives the user's sign-in codes; there is no
institutional sender, mailbox access, Microsoft OAuth or calendar integration.

#### Exact DNS and mail setup

Resend Free remains the approved plan: [published limits](https://resend.com/pricing)
checked 2026-10-20 are $0/month, 3,000 emails/month and 100/day. No paid plan or
overage. Its [SMTP settings](https://resend.com/docs/send-with-smtp) match the
implemented adapter. Domain verification and actual inbox receipt remain open.

The private `dns-change-review.json` contains the exact six provider/portal
records, all TTL Auto. All six names were unused at preflight; check again and
stop on collision. Preserve apex MX/SPF/DMARC and all unrelated records.

| Type / name under lintellabs.net | Exact target or value |
| --- | --- |
| TXT `resend._domainkey.notify` | Exact provider-returned public DKIM value in the reviewed DNS JSON |
| MX `send.notify` | `feedback-smtp.us-east-1.amazonses.com`, priority 10 |
| TXT `send.notify` | `v=spf1 include:amazonses.com ~all` |
| CNAME `rsend.notify` | `send.forge.rmta.net`, DNS only |
| CNAME `book` and CNAME `staff` | `c69461ee-6752-482d-a24c-a1229a0e9ab0.cfargotunnel.com`, proxied |

Mail records are DNS-only. No optional root DMARC, wildcard or new tunnel.
Require Resend verified status before sign-in delivery. Provider open/click
tracking remains disabled and TLS Enforced.

Public edge policy remains narrow:

| Host | Allowed paths | Other behavior |
| --- | --- | --- |
| book | Exact `/intake`, `/intake/status`, prefix `/_app/` | `/` redirects to `/intake`; other API/member/staff/competition/bridge routes 404. |
| staff | Exact `/login`, `/ops`, `/api/v1/profile`, existing auth start/verify/logout; prefixes `/ops/`, `/_app/` | `/` redirects to `/ops/bookings`; intake/public API/other API paths 404. Session/role checks remain authoritative. |

Forward original Host and HTTPS proto, preserve Origin/form bodies and host-local
Set-Cookie, clear incoming trusted-surface headers, no Domain widening/cache.
Proxy connect/read/send deadlines remain 3s/10s/10s, body limit 64KiB and safe
access logs omit query/receipt values. Themis adds proof server-side only.

#### Conditional backup, rollout and acceptance

Final approval includes a consistent **APOLLO-database-only** custom-format
backup from `agents/postgres-0` (PostgreSQL 16) to
`/Users/zizo/Library/Application Support/ASHTON/backups/booking-<UTC>.dump`
(directory 0700/file 0600, outside Git). This protected live data copy has not
been taken. Restore it into isolated disposable local PostgreSQL 16 with
loopback-only networking, no provider/NATS access and protected credential
inputs. Inspect its ledger/bounded counts, expect only
`033_competition_rating_policy_wrapper.up.sql` pending after 001..032, measure
033 locks/runtime and verify restored data plus old-image compatibility. Stop
before live migration on any unexpected ledger or failed restore/compatibility
check. Remove only this disposable restored clone; retain the independent backup.

Keep new staff ingress unavailable until every serving APOLLO process has the
reviewed image. After the backup-clone checks, apply mail DNS/verification and
reviewed Secret/ConfigMap values, record the scoped GitOps delta, patch APOLLO,
wait for migration/readiness, start the frontends, then apply only reviewed
portal DNS/edge changes. Monitor lock wait >5s, migration execution >30s and
readiness >120s as abort thresholds; these are operator budgets, not newly
implemented SQL timeouts. Preserve ATHENA and confirm tap health/Flux suspension.

Send sign-in only to the privately specified user/account, through real SMTP.
The user enters their code in the staff portal. Match the exact account/email,
mailbox-verified UUID and actual session; stop on an identifier/email conflict.
One conditional operator update grants the already selected Owner role to that
verified row. No domain-based promotion, token logging/manual extraction,
authentication bypass or additional manager/supervisor accounts. Existing role
denials remain covered locally; they do not require fake live accounts.

Using that actual Owner/session, provision only the owner-specified Ashtonbee
inventory: `court` / type `court` / label Court; `studio` / type `studio` / label
Studio; `fitness-room` / type `fitness_room` / label Fitness room; each active
and bookable. Stop on conflicting existing keys. No assumed zone/graph,
capacity, half-court, people, requests, reservations, closures or sample hours.
**Leave operating hours unset** until supplied by the owner. Staff timeline
rows derive from actual activity/hours and may remain empty.

Check external TLS, Secure/HttpOnly/SameSite-Strict host-local cookies, CSRF/
origin behavior, route isolation and actual Owner scheduling access in a browser
and phone-size layout. Report login/portal availability separately from complete
booking acceptance. The external request→approval→reservation→status/cancellation
gate still needs real opening hours, an approved actual participant/contact and
the real operational decision. Do not create a fictional public canary, stop
ATHENA or run destructive probes to close that gate.

Abort on failed backup/clone checks, schema/config/DNS drift or collision,
lock/runtime/readiness breach, tap regression, mail/login/role failure, bad
TLS/cookies/origin protections, public exposure or duplicate reservation. Close
new staff ingress/POSTs and scale down Themis before rollback. Restore captured
edge data and the previous changed APOLLO settings, including both pins:
`ghcr.io/ixxet/apollo:sha-6b27618@sha256:6c2d4ef8c67d0ea21d3b41963eb9709d0114b53bfea56c30d456cafbb677515a`.
Remove only newly created frontend/application-config objects once unreferenced
and this run's portal DNS IDs; retain mail setup for repair, real data and the
backup. Revert only this run's scoped GitOps commit with a new commit; never
reset unrelated work or resume Flux. If access must be withdrawn, conditionally
return only the newly granted account to its captured prior role.

Keep booking writes closed after old-image rollback: that image retains the
concurrent-approval defect. No automatic 033 down or database restore; a live
data-loss restore requires a separately approved path/window. No accepted
restore loss window is inferred from this rollout.

Current verdict: implementation, local verification, source/image publication,
provider setup and exact technical approval review are complete. **Live approval,
its conditional independent backup/restore checks, domain verification, deployed
Owner/inbox access and external acceptance remain pending.** Real hours are
needed for bookable slots, not for first proving the two portals and staff login.

### Rendered deployment review material — NOT APPLIED

These exact drafts use the current image pins and reviewed references above.
Provider/role/hostname choices are complete; final exact live approval and its
conditional backup checks still gate execution.
The APOLLO patch is strategic merge by container/env name, preserving unrelated
fields; it is not a replacement Deployment. The frontend List creates only its
four named objects. The nginx addition preserves the existing tap server, and
the tunnel data replaces only the existing ConfigMap data after approved review.

<details>
<summary>Frontend Deployments and Services (unapplied)</summary>

```yaml
---
apiVersion: v1
kind: List
items:
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: hestia
    namespace: agents
    labels:
      app.kubernetes.io/name: hestia
      app.kubernetes.io/part-of: ashton
  spec:
    replicas: 1
    selector:
      matchLabels:
        app.kubernetes.io/name: hestia
    template:
      metadata:
        labels:
          app.kubernetes.io/name: hestia
          app.kubernetes.io/part-of: ashton
      spec:
        serviceAccountName: agents-ghcr
        automountServiceAccountToken: false
        enableServiceLinks: false
        securityContext:
          runAsNonRoot: true
          runAsUser: 1000
          runAsGroup: 1000
          seccompProfile:
            type: RuntimeDefault
        containers:
        - name: hestia
          image: ghcr.io/ixxet/hestia:sha-f9bbca0@sha256:f44b50520d0066c9a9dba1e539c8d2d92a700cdd500f5349fd1a6881df097931
          ports:
          - name: http
            containerPort: 3000
          env:
          - name: HOST
            value: 0.0.0.0
          - name: PORT
            value: '3000'
          - name: ORIGIN
            value: https://book.lintellabs.net
          - name: APOLLO_ORIGIN
            value: http://apollo.agents.svc.cluster.local
          securityContext:
            allowPrivilegeEscalation: false
            readOnlyRootFilesystem: true
            capabilities:
              drop:
              - ALL
          readinessProbe:
            httpGet:
              path: "/_app/version.json"
              port: http
            initialDelaySeconds: 5
            periodSeconds: 10
            timeoutSeconds: 3
            failureThreshold: 3
          livenessProbe:
            httpGet:
              path: "/_app/version.json"
              port: http
            initialDelaySeconds: 10
            periodSeconds: 10
            timeoutSeconds: 3
            failureThreshold: 3
          resources:
            requests:
              cpu: 50m
              memory: 64Mi
            limits:
              memory: 256Mi
- apiVersion: v1
  kind: Service
  metadata:
    name: hestia
    namespace: agents
    labels:
      app.kubernetes.io/name: hestia
      app.kubernetes.io/part-of: ashton
  spec:
    selector:
      app.kubernetes.io/name: hestia
    ports:
    - name: http
      port: 80
      targetPort: http
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: themis
    namespace: agents
    labels:
      app.kubernetes.io/name: themis
      app.kubernetes.io/part-of: ashton
  spec:
    replicas: 1
    selector:
      matchLabels:
        app.kubernetes.io/name: themis
    template:
      metadata:
        labels:
          app.kubernetes.io/name: themis
          app.kubernetes.io/part-of: ashton
      spec:
        serviceAccountName: agents-ghcr
        automountServiceAccountToken: false
        enableServiceLinks: false
        securityContext:
          runAsNonRoot: true
          runAsUser: 1000
          runAsGroup: 1000
          seccompProfile:
            type: RuntimeDefault
        containers:
        - name: themis
          image: ghcr.io/ixxet/themis:sha-3da3375@sha256:0fe93b083518a3e18d2b18b311e4fa8c06775b1a8a08f49a59372a405aa86a02
          ports:
          - name: http
            containerPort: 3000
          env:
          - name: HOST
            value: 0.0.0.0
          - name: PORT
            value: '3000'
          - name: ORIGIN
            value: https://staff.lintellabs.net
          - name: APOLLO_ORIGIN
            value: http://apollo.agents.svc.cluster.local
          - name: THEMIS_DEFAULT_FACILITY_KEY
            value: ashtonbee
          - name: THEMIS_APOLLO_TRUSTED_SURFACE
            value: themis
          - name: THEMIS_APOLLO_TRUSTED_SURFACE_TOKEN
            valueFrom:
              secretKeyRef:
                name: ashton-booking-surface
                key: themis-token
          securityContext:
            allowPrivilegeEscalation: false
            readOnlyRootFilesystem: true
            capabilities:
              drop:
              - ALL
          readinessProbe:
            httpGet:
              path: "/_app/version.json"
              port: http
            initialDelaySeconds: 5
            periodSeconds: 10
            timeoutSeconds: 3
            failureThreshold: 3
          livenessProbe:
            httpGet:
              path: "/_app/version.json"
              port: http
            initialDelaySeconds: 10
            periodSeconds: 10
            timeoutSeconds: 3
            failureThreshold: 3
          resources:
            requests:
              cpu: 50m
              memory: 64Mi
            limits:
              memory: 256Mi
- apiVersion: v1
  kind: Service
  metadata:
    name: themis
    namespace: agents
    labels:
      app.kubernetes.io/name: themis
      app.kubernetes.io/part-of: ashton
  spec:
    selector:
      app.kubernetes.io/name: themis
    ports:
    - name: http
      port: 80
      targetPort: http
```

</details>

<details>
<summary>APOLLO strategic merge patch (unapplied)</summary>

```json
{
  "spec": {
    "template": {
      "spec": {
        "initContainers": [
          {
            "name": "apollo-migrate",
            "image": "ghcr.io/ixxet/apollo:sha-6a10c55@sha256:838ac17b26cc7e9d0bb3d62e31b82425fc7e573bbfb49a846ca6c98e0299aad8"
          }
        ],
        "containers": [
          {
            "name": "apollo",
            "image": "ghcr.io/ixxet/apollo:sha-6a10c55@sha256:838ac17b26cc7e9d0bb3d62e31b82425fc7e573bbfb49a846ca6c98e0299aad8",
            "env": [
              {
                "name": "APOLLO_SESSION_COOKIE_NAME",
                "value": "apollo_session"
              },
              {
                "name": "APOLLO_SESSION_COOKIE_SECURE",
                "value": "true"
              },
              {
                "name": "APOLLO_LOG_VERIFICATION_TOKENS",
                "value": "false"
              },
              {
                "name": "APOLLO_TRUSTED_SURFACES",
                "valueFrom": {
                  "secretKeyRef": {
                    "name": "ashton-booking-surface",
                    "key": "apollo-trusted-surfaces"
                  }
                }
              },
              {
                "name": "APOLLO_SMTP_HOST",
                "valueFrom": {
                  "configMapKeyRef": {
                    "name": "ashton-booking-mail",
                    "key": "host"
                  }
                }
              },
              {
                "name": "APOLLO_SMTP_FROM",
                "valueFrom": {
                  "configMapKeyRef": {
                    "name": "ashton-booking-mail",
                    "key": "from"
                  }
                }
              },
              {
                "name": "APOLLO_SMTP_TLS_MODE",
                "value": "starttls"
              },
              {
                "name": "APOLLO_SMTP_PORT",
                "value": "587"
              },
              {
                "name": "APOLLO_SMTP_TIMEOUT",
                "value": "4s"
              },
              {
                "name": "APOLLO_SMTP_USERNAME",
                "valueFrom": {
                  "secretKeyRef": {
                    "name": "apollo-smtp",
                    "key": "username"
                  }
                }
              },
              {
                "name": "APOLLO_SMTP_PASSWORD",
                "valueFrom": {
                  "secretKeyRef": {
                    "name": "apollo-smtp",
                    "key": "password"
                  }
                }
              }
            ]
          }
        ]
      }
    }
  }
}
```

</details>

<details>
<summary>Additional nginx host servers (append to preserved tap configuration; unapplied)</summary>

```nginx
log_format ashton_booking_safe '$time_iso8601 $host $request_method $status';

server {
  listen 8080;
  server_name book.lintellabs.net;
  absolute_redirect off;
  client_max_body_size 64k;
  access_log /var/log/nginx/access.log ashton_booking_safe;
  proxy_http_version 1.1;
  proxy_connect_timeout 3s;
  proxy_read_timeout 10s;
  proxy_send_timeout 10s;
  proxy_cache off;
  proxy_set_header Host $host;
  proxy_set_header X-Forwarded-Host $host;
  proxy_set_header X-Forwarded-Proto https;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Apollo-Trusted-Surface "";
  proxy_set_header X-Apollo-Trusted-Surface-Token "";

  location = / { return 302 /intake; }
  location = /intake { proxy_pass http://hestia.agents.svc.cluster.local:80; }
  location = /intake/status { proxy_pass http://hestia.agents.svc.cluster.local:80; }
  location ^~ /_app/ { proxy_pass http://hestia.agents.svc.cluster.local:80; }
  location / { return 404; }
}

server {
  listen 8080;
  server_name staff.lintellabs.net;
  absolute_redirect off;
  client_max_body_size 64k;
  access_log /var/log/nginx/access.log ashton_booking_safe;
  proxy_http_version 1.1;
  proxy_connect_timeout 3s;
  proxy_read_timeout 10s;
  proxy_send_timeout 10s;
  proxy_cache off;
  proxy_set_header Host $host;
  proxy_set_header X-Forwarded-Host $host;
  proxy_set_header X-Forwarded-Proto https;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Apollo-Trusted-Surface "";
  proxy_set_header X-Apollo-Trusted-Surface-Token "";

  location = / { return 302 /ops/bookings; }
  location = /login { proxy_pass http://themis.agents.svc.cluster.local:80; }
  location = /ops { proxy_pass http://themis.agents.svc.cluster.local:80; }
  location ^~ /ops/ { proxy_pass http://themis.agents.svc.cluster.local:80; }
  location ^~ /_app/ { proxy_pass http://themis.agents.svc.cluster.local:80; }
  location = /api/v1/profile { proxy_pass http://themis.agents.svc.cluster.local:80; }
  location = /api/v1/auth/verification/start { proxy_pass http://themis.agents.svc.cluster.local:80; }
  location = /api/v1/auth/verify { proxy_pass http://themis.agents.svc.cluster.local:80; }
  location = /api/v1/auth/logout { proxy_pass http://themis.agents.svc.cluster.local:80; }
  location / { return 404; }
}
```

</details>

<details>
<summary>Cloudflare tunnel routing data (existing credential reference; unapplied)</summary>

```yaml
---
tunnel: c69461ee-6752-482d-a24c-a1229a0e9ab0
credentials-file: "/etc/cloudflared/creds/credentials.json"
ingress:
- hostname: tap.lintellabs.net
  service: http://athena-edge-proxy.athena.svc.cluster.local:8080
- hostname: book.lintellabs.net
  service: http://athena-edge-proxy.athena.svc.cluster.local:8080
- hostname: staff.lintellabs.net
  service: http://athena-edge-proxy.athena.svc.cluster.local:8080
- service: http_status:404
```

</details>

## Original R1 status and ownership (historical)

Intake recorded 2026-10-20. R1 continues the bridge/readiness work; completed
3B packet IDs and dated milestone evidence remain historical. G0 audit, G1
source repairs and G2 real local proof are executed. G3 found concrete external
booking blockers; deployment readiness is not closed. G4 source/docs publication
and exact CI outcomes are recorded below. No live changes are authorised.

Delivery order is booking/scheduling through Hestia and Themis, competition /
rating / game identity, then member services. Broader ATHENA physical truth is
later. Booking does not gain a presence dependency. Daily credits, teams,
reliability scoring, instant booking, payments, public tournaments and OpenSkill
cutover remain outside R1.

Platform owns sequencing and cross-repository readiness. Repository docs own
implemented contracts. APOLLO's [launch expansion audit](https://github.com/ixxet/apollo/blob/main/docs/launch-expansion-audit.md)
still governs competition expansion. The controller owns every edited file;
Contract, Surface/Ops and Verifier guards perform independent read-only audits.
No additional worker chats are created.

## G0 current source and overlap audit

All six remote main SHAs were checked with `git ls-remote origin refs/heads/main`
on 2026-10-20, separately from local origin refs. All match local HEAD.
Hestia/Themis GitHub API checks now succeed; their previous 404 verification gaps
are resolved. GitHub all-state issue lists (limit 50) and PR lists (limit 30)
returned empty for all five ASHTON repositories. Recent commit lists, existing
Markdown, bridge preflight, route/API matrix, flows and deferments were searched.
No equivalent canonical booking-readiness artifact exists. Existing bridge and
frontend evidence is reused below, rather than creating a parallel tracker.

### ASHTON/apollo

Branch `main`; HEAD `165369f30b6ebc39c0938c319382d10503d60652`.

Initial dirty files (unrelated, preserved):

```text
M README.md
 M docs/README.md
 M docs/glossary.md
 M docs/growing-pains.md
 M docs/hardening/README.md
 M docs/runbooks/member-state.md
?? gotest.json
?? junit.xml
```

Recent ten commits:

```text
165369f docs: document APOLLO ATHENA bridge readiness
9e9340d test: cover ATHENA bridge runtime errors
7cd1778 fix: harden ATHENA bridge client handling
43424c1 docs: document operational presence gate
c37f8e1 test: cover operational ATHENA presence gate
1a17633 feat: consume ATHENA ingress bridge
82188d0 docs: record ATHENA presence gate proof
ad0171e feat: add ATHENA presence gate proof
4f0bff1 docs: record live destructive probe planning truth
1eec51d docs: record ATHENA ingress bridge truth
```

### ASHTON/athena

Branch `main`; HEAD `99ca72e961b3502bdde91362eb98db7c0b829881`.

Initial dirty files (unrelated, preserved):

```text
?? gotest.json
?? junit.xml
```

Recent ten commits:

```text
99ca72e docs: document ingress bridge deploy readiness
a8fce6c test: cover ingress bridge deploy-readiness bounds
ec0d47a fix: harden internal ingress bridge read
d6b3386 docs: document operational ingress bridge
9e5f6df test: cover internal ingress bridge read
205027d feat: expose internal ingress bridge read
2477427 docs: record live destructive probe planning truth
d10eed0 docs: document real ingress bridge proof
b58fa9b test: cover real ingress bridge proof
6058042 feat: add real ingress bridge proof
```

### ASHTON/ashton-platform

Branch `main`; HEAD `13f751b3dc32d49dfd59294db6b1d2eeaf890f7f`.

Initial dirty files (unrelated, preserved):

```text
M README.md
 M planning/audits/2026-04-29-competition-system-audit.md
 M planning/milestones/milestone-3.0-packet.md
 M planning/repo-briefs/apollo.md
 M planning/repo-briefs/ashton-mcp-gateway.md
 M planning/repo-briefs/ashton-proto.md
 M planning/repo-briefs/athena.md
 M planning/runbooks/phase-2-launch.md
 M planning/sprints/TRACER-MATRIX.md
```

Recent ten commits:

```text
13f751b docs: add closed-test bridge deployment preflight
4f11b3b docs: record operational bridge deploy readiness
b201ba0 docs: record operational presence trust gate
e2a20ca docs: add live destructive probe readiness checklist
1ec5812 docs: record co-presence private presence gate truth
e156e71 docs: add live destructive probe plan
1527682 docs: record ATHENA real ingress bridge truth
34e348b docs: record game identity policy tuning truth
d5941bc docs: add frontend route api contract matrix
23daefc docs: record rating policy simulation truth
```

### ASHTON/hestia

Branch `main`; HEAD `66010ae8bd453fccdaffbe4e4eb27cb7b4d39535`.

Initial dirty files (unrelated, preserved):

```text
clean
```

Recent ten commits:

```text
66010ae docs: close Hestia 3B trust spine
4672fc2 docs: harden 3B.20 truth
88f9bd4 docs: sync Hestia 3B.20 truth
71a6117 test: cover Hestia game identity boundary
7deb131 feat: add game identity surface
3c190bb test: cover Hestia 3B.19 public competition boundary
7f8ed9f fix: harden Hestia 3B.19 public competition cohesion
1ab7803 docs: sync Hestia 3B.19 truth
cb30f42 test: cover Hestia public competition boundary
cd87a6c feat: add public competition readiness surface
```

### ASHTON/themis

Branch `main`; HEAD `0231e02dd651676667b867f3112ef2179a5f26cb`.

Initial dirty files (unrelated, preserved):

```text
clean
```

Recent ten commits:

```text
0231e02 test: cover Themis 3B.18 safety reliability boundary
2a2e20f fix: harden Themis 3B.18 safety reliability cohesion
b44d626 docs: sync Themis 3B.18 truth
93a5d45 test: cover Themis safety reliability boundary
98a1e48 feat: add manager safety reliability ops surface
0bc9503 docs: sync Themis 3B.12 truth
7af9b6e test: cover Themis lifecycle result boundaries
1d774e1 feat: add competition lifecycle result ops states
7cd3097 test: cover Themis 3B.11 ops boundary
74561a9 fix: harden Themis 3B.11 ops cohesion
```

### Computers/Prometheus

Branch `main`; HEAD `80203883c4fbbe4b88889d8ee8c20e048c83a7e1`.

Initial dirty files (unrelated, preserved):

```text
M docs/runbooks/model-upgrade.md
 M docs/runbooks/network-relocation.md
 M homelab-gitops/apps/agents/langgraph/langgraph-configmap.yaml
 M homelab-gitops/apps/ai/vllm/vllm-deployment.yaml
 M homelab-gitops/apps/summarizer/summarizer-deployment.yaml
 M scripts/verify-after-network-move.sh
 M services/langgraph/app/config.py
```

Recent ten commits:

```text
8020388 chore: roll APOLLO deployment to milestone 3.0 image
86bd13c feat: add competition telemetry scrape and alert example
02033bd docs: record ATHENA named tunnel ingress
2ec2f61 deploy: move ATHENA edge tunnel to tap.lintellabs.net
ad9e83f deploy: stage ATHENA v0.8.2 hardening
2b1d4d0 deploy: repin ATHENA to v0.8.1
49d63fc deploy: stage ATHENA v0.8.0 policy rollout
0105bad observability: add ATHENA edge truth dashboard
e97a449 athena: add Ashtonbee second edge node token
93b1d71 docs: align athena deploy truth in Prometheus README
```

## Confirmed findings and acceptance baseline

| Finding | Current source / owner | Reproduction and desired behaviour | Acceptance evidence planned |
| --- | --- | --- | --- |
| Redirect token forwarding | APOLLO `internal/athena/client.go` | Internal-read client follows redirects by default. Reject every redirect without a follow-up; keep occupancy/analytics behaviour unchanged. | Same-origin/cross-origin 301/302/307/308 target hit counts and token assertions. |
| Incomplete resource bounds | ATHENA `internal/edgehistory/ingress_bridge.go`, shared analytics-session reader; APOLLO client and CLI | Observation/history/session materialisation and response/file reads lack complete bounds. Reject overflow; no eligible silently truncated report. | Exact limit / plus one, query plans, timeout/cancellation and CLI checks. |
| Missing pre-window replay | ATHENA bridge | New empty projector loses pre-window arrivals and other-node state. Replay retained history in storage order across nodes, preserving facility/zone identity scope; bounded unknown blocks eligibility. | Pre-window arrival, in-window exit/repeated arrival, stale/late/same-time and cross-node cases with disposable PostgreSQL. |
| Insufficient envelope validation | APOLLO client / presence gate / CLI | `{}` is accepted; missing safety fields and contradictory scope/identity/eligibility need rejection. | Versioned completeness envelope, strict required fields, valid empty report, additive compatibility, malformed/scope/oversize and file/runtime parity. |
| Themis load-error permission claim | Themis `src/routes/ops/bookings/new/+page.server.ts` | Non-401 load failure sets `canManage:false` and renders read-only state. | Record source and current test result; runtime fix belongs to next booking packet. |
| Malformed public success handling | Hestia `src/lib/server/public-intake.ts` and intake routes | Malformed 202 receipt / 200 status can throw through route handlers. | Reproduce and record; runtime fix belongs to next booking packet. |

Implemented bridge budgets: 1,000 report observations, 4,000 historical/context
observations, requested sessions 1..250 (default 50), 2 MiB encoded response or
file input, 24-hour observation window and 5-second producer operation deadline.
The client uses its configured shorter timeout, capped at 5 seconds for this
bridge read. A database LIMIT bounds result materialisation only; fixture
EXPLAIN and cancellation must separately establish scan/time behaviour.

Internal contract metadata identifies `athena_ingress_bridge_v2`, report-local
identity scope, storage replay order and explicit observations/sessions/context
completeness. Existing prose contracts remain. Legacy unversioned file reports
must be regenerated; harmless additive fields remain accepted. Raw identity
values are never emitted. Report-local references cannot support durable member
credit. Source-pass sessions remain separate from policy-accepted presence.

## Verification and publication plan

Run the packet's exact Go focused/full/race/vet/build commands and both frontend
check/unit/build/e2e suites. Keep raw failure logs outside repositories and
report skipped/unexecuted proof honestly. Use real ATHENA HTTP handler + durable
store/service + APOLLO actual client/classifier/CLI for cross-service proof, not
two mocks. Record query plans, fixtures and no-product-write checks.

Before pushing, inspect service image publication and Flux reconciliation.
Publish only scoped source and Markdown commits through the existing main
workflow if that cannot roll out live. No frontend runtime, GitOps, secret,
migration-on-live, pod or deployed-data mutations are authorised.

## Outstanding readiness decisions

Approved deployment target, external Hestia URL, authenticated Themis access,
server-only trusted-token secret references, APOLLO auth/cookies/routing,
staff provisioning, resource/hour/closure inventory, canary fixtures and rollback
owner/window remain unapproved. Current access and packaging evidence will be
recorded during G3. Booking rollout and optional bridge rollout are separate.
Live deployment requires fresh approval of target, exact images/diff, secrets,
migrations, fixture mutations, window, rollback and abort criteria.

## G1-G2 implemented bridge repairs and local proof

| Repaired defect / decision | Runtime source and regression owner | Local implementation commit |
| --- | --- | --- |
| Bounded snapshot/history/session materialisation; historical cross-node replay; explicit unknown after ambiguous uncommitted state; as-of-window session/node filtering | ATHENA `internal/edgehistory/ingress_bridge.go`, `internal/edgehistory/ingress_bridge_test.go` | [`43bcfb6`](https://github.com/ixxet/athena/commit/43bcfb6958a68945d789558d57129eabd8501aa6) |
| HTTP/CLI time/window/session/byte budgets; no eligible truncation; redacted internal failures; auth/no-store preserved | ATHENA `internal/server/{server.go,server_test.go}`, `cmd/athena/{main.go,main_test.go}` | `43bcfb6` |
| Reject all bridge redirects; cap response read/time; require requested facility/window/zone/node and session scope; preserve analytics/occupancy behaviour | APOLLO `internal/athena/{client.go,client_test.go}` | [`7fab80e`](https://github.com/ixxet/apollo/commit/7fab80e0373417a8832cde9c7e425bac213448c3) |
| Shared required v2 decoder; explicit completeness; supported semantics; evidence/session identity/time/eligibility/summary checks; safe report-local identity allowlist | APOLLO `internal/presence/{athena_ingress_gate.go,athena_ingress_gate_test.go}` | `7fab80e` |
| Bounded regular-file input; reject FIFO/device/directory without blocking; scope checks; real HTTP service/client/classifier/CLI proof | APOLLO `cmd/apollo/{main.go,main_test.go}` | `7fab80e` |
| Explicit completeness line in text mode, including zero-evidence incomplete reports | APOLLO `cmd/apollo/{main.go,main_test.go}` | [`c64b349`](https://github.com/ixxet/apollo/commit/c64b34928f090f48f7ff5ead73d5fbceaf29eb33) |

Source-pass and accepted-policy presence remain separate. Reconstructing all
retained applicable context is conservative: above 4,000 context records, or
when an uncommitted pass would change presence, `replay_context=false` and all
evidence/session signals are blocked. This can reduce eligibility for busy or
ambiguous histories; it does not guess absence or use today's latest marker.
A bounded historical checkpoint/index design is later work if actual use earns
it, not a hidden migration or widened runtime in R1.

The read-only repeatable-read snapshot, cancellation and cap-plus-one queries
bound materialisation. Representative EXPLAIN fixtures are explicitly not full
production-query or production-population load proof: 4,002 history rows were
scanned/sorted to return 4,001, with approximately 378 KiB sort memory and 2 ms
fixture execution. LIMIT is not a physical scan limit. Each PostgreSQL statement
has a four-second timeout inside a five-second operation context; blocked-query
cancellation is regression-tested. Existing shared analytics queries are unchanged.

The wire contract is `athena_ingress_bridge_v2`, with
`identity_scope: report_local`,
`replay_order: stored_at,observed_at,event_id,observation_id`, and required
`completeness: {observations,sessions,replay_context,reason_codes}`. Both
producer and consumer exercise it. Legacy files are rejected with regeneration
guidance, not silently upgraded. Required fields/null/wrong types, `{}`, scope
mismatches, future session closure, contradictory eligibility, unsafe references,
oversize bytes, malformed trailing input and incomplete context are covered;
additive fields and complete empty reports are accepted. Same/cross-origin
301/302/303/307/308 tests assert zero redirect follow-ups. JSON/text modes remain.

Reproducible real-service proof from the APOLLO root, using installed Docker:

```sh
APOLLO_ATHENA_REHEARSAL=1 \
ATHENA_SOURCE_DIR=/Users/zizo/Personal-Projects/ASHTON/athena \
go test -count=1 -v ./cmd/apollo -run TestPresenceAthenaGateRealServicePostgres
```

`TestPresenceAthenaGateRealServicePostgres` builds actual sibling ATHENA into a
temporary directory, applies both existing migration sets in disposable
PostgreSQL 16, starts disposable NATS, creates a synthetic facility admission
policy and sends actual HTTP taps. APOLLO's actual client, shared classifier
and CLI consume four nonempty node-scoped observations. Results: two eligible
co-presence signals, one daily-readiness signal, no source-pass session for a
policy failure, blocked repeated arrival, file/runtime JSON equality and matching
text counts. Actual service reads also verify valid empty reports and 401/403.
The APOLLO product count vector stays `0,0,0,0,0,0` for visits, tap-links,
streaks, streak events, booking requests and schedule blocks. Runtime logs from
the child service are discarded; output is checked for raw identity/token leaks.

The fixture policy, taps, databases, containers and child binary/process are
local disposable artifacts, cleaned by the harness. No test targets a live DB.
The opted-in command requires the sibling checkout and fails on missing source;
single-repo CI explicitly skips this rehearsal and proves its own suite only.

Direct booking/schedule proof, with no ATHENA/Ops dependency supplied to the
actual APOLLO HTTP/PostgreSQL fixture:

```sh
go test -count=1 ./internal/server -run 'Test(PublicBooking|BookingRequest|Schedule)'
```

This passed and exercises existing real booking request/approval/reservation /
status/cancellation and schedule handlers. Frontend mocks remain separate proof.

## G3 booking journey and authority

| Step | Actual endpoint / frontend | Authority and meaning |
| --- | --- | --- |
| Options | Hestia `/intake`; `GET /api/v1/public/booking/options` | Public-listed option IDs/labels; APOLLO owns inventory. |
| Availability | `GET /api/v1/public/booking/options/{optionID}/availability?from=&until=` | Explicit RFC3339 bounds; requestable/closed/booked/unavailable hints, never a reservation. |
| Request | `POST /api/v1/public/booking/requests` | `Idempotency-Key` and `X-Apollo-Public-Intake-Channel: public_web`; 202 receipt creates `requested`, no schedule reservation. |
| Status | Hestia `/intake/status`; `GET /api/v1/public/booking/requests/status?receipt_code=` | Opaque receipt, customer status/message/window/update time. Submission is not confirmation. |
| Staff workspace | Themis `/ops/bookings`; `GET/POST /api/v1/booking/requests`, `GET .../{id}` | Session plus `booking_read` / `booking_manage`. Staff create uses load-born, form-carried idempotency. |
| Decisions | `POST .../{id}/review`, `/needs-changes`, `/reject`, `/approve`, `/cancel` | Manager/owner, server-only trusted proof and `expected_version`. Approval atomically creates linked `reservation` / `hard_reserve` / `internal` schedule truth. |
| Public message and replacement | `POST .../{id}/public-message`, `/edit`, `/rebook` | Versioned changes, separate public-safe message; replacement additionally idempotent. Internal notes remain private. |
| Cancellation | Approved request `/cancel` | Atomically cancels the linked reservation, retains linkage/audit and refuses stale versions or reservation drift. Do not cancel linked reservations through generic schedule controls. |
| Schedule | Themis `/ops/facilities/{facility}/schedule`; `GET/POST /api/v1/schedule/blocks`, `POST .../{id}/cancel` | Supervisor read, manager/owner trusted writes; APOLLO validates resources, relationships, conflicts and versions. |

Source: APOLLO `internal/booking/service.go`, `internal/schedule/service.go`,
`internal/authz/{authz,trusted_surface}.go`, `internal/server/server.go` and
`internal/server/booking_request_integration_test.go`; Hestia
`src/lib/server/public-intake.ts`; Themis `src/routes/ops/bookings` and
`src/lib/server/{client,apollo}.ts`. `booking_read`/`schedule_read` admit
supervisor, manager and owner; manage capabilities admit manager and owner.
Members are denied. Themis injects trusted proof only on the server. Both
browser proxies strip caller-supplied trusted headers; public APOLLO paths are
not broad browser passthroughs.

Booking service dependencies are repository + schedule, not ATHENA. APOLLO
constructs booking/schedule before the optional ATHENA-backed ops overview.
Direct Requests/Schedule navigation exists when facility overview fails; no
occupancy value is invented. Themis's default schedule link still hardcodes
`ashtonbee`; a different facility needs an explicit next-packet decision.

Hestia converts browser-local selections to RFC3339 with explicit offsets.
APOLLO uses IANA zones and calendar arithmetic for recurrence. Existing offset /
Toronto tests do not establish DST gap/fold and 23/25-hour-day acceptance;
that focused proof is required in the next packet. Sequential resource conflicts
are covered. Simultaneous overlapping approvals for distinct request IDs are an
unverified concurrency risk: request-row locks are separate and no resource
exclusion constraint was found. Reproduce before deciding a backend fix.

## G3 reproduced frontend blockers

The existing [route/API matrix](FRONTEND-ROUTE-API-CONTRACT-MATRIX.md) already
tracks both gaps; this record updates their evidence and priority.

- Themis `src/routes/ops/bookings/new/+page.server.ts` catches every non-401
  list-load failure as `canManage:false`; the page then asserts read-only
  permission. Current-function connection-failure reproduction returns
  `{"canManage":false,"hasSafetyKey":true,"hasErrorState":false}`. The next
  packet must separate verified read-only authority from retryable unavailable,
  malformed and load-error states, retaining its idempotency key.
- Hestia `src/lib/server/public-intake.ts` throws on malformed successful
  receipt/status payloads and network failures, which intake/status routes do
  not catch. Current-function 202 `{}` rejects `status must be a string`; 200
  `{}` rejects `receipt_code must be a string`; network failure rejects
  `fetch failed`. Convert these to neutral retryable results, preserving form
  values and the original submission key after ambiguous submission failure.
- Themis new-request defaults still use April 20, 2026. Replace stale defaults;
  do not infer real opening hours or resource availability from fixtures.

These reproductions transpiled actual TypeScript in memory using the existing
`typescript` package and injected mocked upstreams. They are function evidence,
not HTTP, browser, real APOLLO or deployed frontend proof. No frontend runtime,
configuration, dependency or lockfile was changed by R1.

## G3 current deployment inventory

Read-only capture: 2026-10-20, approximately 14:33-14:34 UTC. This supersedes
access failures from the May preflight, not its dated milestone evidence.
The target remains a candidate until explicitly approved.

| Check | Current verified result |
| --- | --- |
| Candidate | `admin@talos-tower`, node `talos-p0d-y77` Ready, namespaces `agents` / `athena` |
| Mac saved API | `https://10.0.0.209:6443` `/readyz` timed out |
| MIMIR saved API | `/home/boi/.config/prometheus/kubeconfig` points at `192.168.2.46`; timed out |
| Rediscovered management | Existing authenticated MIMIR SSH at `100.109.171.72`; Ethernet `172.31.250.1` reaches Talos `172.31.250.162`; existing Kubernetes credentials and per-command server override return `/readyz=ok`, without TLS bypass/config edits |
| APOLLO | Ready 1/1, tag `sha-6b27618`, actual digest `sha256:6c2d4ef8c67d0ea21d3b41963eb9709d0114b53bfea56c30d456cafbb677515a` |
| ATHENA | Ready 1/1, tag `v0.8.2`, actual digest `sha256:6524994c2c30bfd227b684b9144b95a798e3e46e600ac5da46df2a600dfd271d` |
| Health / metrics | APOLLO `ok`, consumer enabled, 34 metric samples; ATHENA `ok`, edge-projection, one occupancy sample; Prometheus `up=1` for both; 15s ServiceMonitors |
| Recent logs | Limited to 30 lines / 30 minutes; APOLLO zero, ATHENA four; identity/message fields suppressed |
| External edge | Valid TLS; `https://tap.lintellabs.net/api/v1/health` 200; bridge path 404 |
| Ingress allowlist | No Kubernetes Ingress, no HTTPRoute resource; nginx permits exact tap/health only, fallback 404; tunnel routes only to edge proxy |
| Frontends | No Hestia or Themis Deployment found; no tracked frontend Dockerfile or image/deploy workflow; adapter-node builds exist |
| Migration ledger | 32 applied, latest `032_competition_safety_reliability_delete_guard.up.sql`; `033` not applied |
| Booking inventory | Zero active/bookable resources; no schedule blocks; public options `[]`; zero booking requests; no verified supervisor/manager/owner accounts returned |
| Wiring | APOLLO trusted-surface and ATHENA settings absent; ATHENA internal-read token absent. Secret values were never retrieved. |
| Restore evidence | Bound local-path PostgreSQL PVC; no agents CronJobs or VolumeSnapshot resource. Independent backup and successful restore remain unverified; this does not prove no external backup exists. |

Database inventory used bounded SELECTs with `default_transaction_read_only=on`
and `statement_timeout=5000`, no identity output or unrestricted dumps. No live
mutations, secret reads/changes, network changes or restarts were performed.

The working authenticated command, retained as dated evidence, is:

```sh
ssh -i /Users/zizo/.ssh/mimir_ed25519 \
  -o BatchMode=yes -o StrictHostKeyChecking=yes -o ConnectTimeout=8 \
  boi@100.109.171.72 \
  'kubectl --kubeconfig /home/boi/.config/prometheus/kubeconfig \
    --server=https://172.31.250.162:6443 --request-timeout=10s \
    get --raw=/readyz'
```

Use that prefix for authenticated GETs of:

```text
/api/v1/namespaces/agents/services/apollo:80/proxy/api/v1/health
/api/v1/namespaces/athena/services/athena:80/proxy/api/v1/health
/api/v1/namespaces/agents/services/apollo:80/proxy/metrics
/api/v1/namespaces/athena/services/athena:80/proxy/metrics
```

Addresses must be rediscovered if these reads fail. Do not change saved
kubeconfig or bypass certificate checks to make inventory pass.

## Deployment wiring for review, not applied

| Decision / setting | Booking rollout requirement | Optional bridge rollout |
| --- | --- | --- |
| Target / URLs | Approve environment, external TLS Hestia hostname and authenticated Themis hostname/access; no URL exists in this evidence | Same approved target, private service access only |
| Node frontend packaging | Add bounded adapter-node container/build publication for Hestia and Themis in the next packet; record exact image digests | No frontend dependency |
| `APOLLO_ORIGIN` | Explicit server-side internal APOLLO origin, e.g. `http://apollo.agents.svc.cluster.local`; both frontend defaults use 8080 while APOLLO runtime defaults 8081, so defaults are not deployment wiring | Unchanged |
| Node `ORIGIN` / proxy | Exact external HTTPS origin for each frontend; approve forwarded host/protocol and ingress/TLS handling; verify form-origin/CSRF behaviour | Bridge remains private |
| Themis trusted proof | `THEMIS_APOLLO_TRUSTED_SURFACE` and `THEMIS_APOLLO_TRUSTED_SURFACE_TOKEN` from an approved server-only secret reference; matching `APOLLO_TRUSTED_SURFACES` mapping | Not a substitute for bridge auth |
| APOLLO session | Approved reference for `APOLLO_SESSION_COOKIE_SECRET`, correct cookie name, `APOLLO_SESSION_COOKIE_SECURE=true`; HttpOnly/SameSite Strict; verify actual external login/logout and host-local cookie mirroring | Unchanged |
| Staff provisioning/login | Approve verified manager/owner and supervisor accounts plus a working verification/login channel. Runtime defaults to `NoopEmailSender`; optional token logging is not delivered mail; no staff role provisioning CLI was found | Unchanged |
| Resources and dates | Approve synthetic active/bookable/public-listed resource, relationships/exclusivity, facility timezone, hours, closure dates and bounded canary request window | Approve separate nonempty ATHENA evidence fixture only if bridge smoke is chosen |
| Bridge base URL | Booking has no required ATHENA setting | `APOLLO_ATHENA_BASE_URL=http://athena.athena.svc.cluster.local`, timeout at most 5s |
| Bridge secrets | None needed for direct booking | Approved paired secret references for `ATHENA_INTERNAL_READ_TOKEN` and `APOLLO_ATHENA_INTERNAL_READ_TOKEN`; never expose them through browsers or edge proxy |

The trusted-surface parser accepts comma-separated `surface=token` entries;
Themis defaults to surface `themis`, so the approved APOLLO mapping must bind
that exact key to the server-only token reference. Keep other approved entries.
No secret names or values have been invented. The approval record must name the
existing/new references and who provisions them. Packaging/configuration work
belongs to the next booking packet and requires its normal scope decisions.

## Migration, rollback and abort plan for approval

Current APOLLO/ATHENA Dockerfiles embed migrations. APOLLO's existing deployment
initContainer automatically runs pending migrations before its server. The
source delta from deployed `6b27618` contains APOLLO migration `033` only; it
adds/backfills rating-policy columns, constraints and an index. It is not a
booking or bridge migration. ATHENA requires no new R1 migration.

Before approval, capture a current migration ledger and independently
recoverable database backup, rehearse restore, and measure 033 lock/runtime
against representative disposable data. Prove old APOLLO image compatibility
with schema 033 before relying on image-only rollback. The 033 down migration
deletes wrapper rating events and resets policy metadata: do not run it as a
routine rollback. A database restore/down operation requires its own approved
scope and loss window.

Rollback image references are the exact APOLLO/ATHENA digests in the live
inventory above. Previous desired configuration is Prometheus commit
`80203883c4fbbe4b88889d8ee8c20e048c83a7e1`, with apps last-applied revision
`ad9e83f` and live `apps` reconciliation suspended. Capture live Deployment and
secret-reference metadata immediately before an approved rollout; do not resume
Flux or push GitOps under R1. Named rollback owner, execution window, fixture
IDs and an independently tested restore path remain required approval inputs.

Abort on failed readiness/health, migration error or unacceptable lock time,
login/role/trusted-surface failure, empty synthetic options, wrong public
exposure, duplicate request/reservation, missing linked cancellation,
customer-private field leak or canary scope escape. Stop further POSTs, preserve
redacted evidence, and use the named owner's approved image/config rollback;
restore database data only through the separately approved restore path.

## Smoke procedure for the next approved rollout

1. Read-only: capture ready replicas, actual imageIDs, migration ledger, health,
   metrics, public route allowlists and TLS on both external hostnames. Capture
   the initial options/availability baseline; the current empty inventory is an
   expected baseline, not nonempty canary proof. Verify the private bridge still
   returns public 404 whether or not it is enabled.
2. Approval-required fixture mutations: provision the approved synthetic staff,
   resources, hours/closures and canary window; log only fixture IDs and intended
   cleanup. Do not reuse real customer/member identities or invent availability.
   Then perform read-only checks that public options are nonempty and the chosen
   synthetic option has the expected explicit-offset availability.
3. Approval-required request: open Hestia `/intake`, choose the synthetic option
   and an explicit RFC3339 window. Before submitting, capture the original
   frontend action field values and idempotency key in the approved browser
   harness. Submit once, retain its receipt, then replay that original action
   body/key in the same origin/session context. Do not log cookies or tokens.
   Hestia clears successful fields with `blankFields()` and generates a new key,
   so normal form resubmission is not an idempotency test. Assert the replay
   returns the same receipt and leaves exactly one APOLLO request and no
   reservation; customer status is pending, not confirmed.
4. Approval-required decisions: log in through the actual Themis hostname;
   check supervisor read-only and member denial, then manager/owner review and
   approval with the current `expected_version`. Confirm one linked reservation
   through authenticated APOLLO detail/calendar, customer approved status and a
   separate public-safe message. Stale-version and resource-conflict attempts
   require the same fixture approval and must fail without extra reservations.
5. Approval-required cancellation: cancel the approved booking request using its
   latest version. Confirm the linked reservation is cancelled and customer
   status changes. Keep internal notes, contacts, request IDs, reservation IDs,
   actors, conflict details and tokens out of public responses.
6. Read-only: repeat direct booking/schedule health and reads while the local
   rehearsal has no ATHENA configured. Do not disable deployed ATHENA for this
   proof. Optional bridge smoke uses a separate image/secret/config approval and
   must return nonempty, complete v2 evidence or explicit blocked eligibility.
7. Approval-required cleanup: cancel only the recorded canary requests/blocks,
   retire canary resources/staff by an approved path, and preserve audit records.
   No broad deletes or automatic schema down migration.

The API diagnostic payload shape, if separately approved, is:

```json
{
  "option_id": "the approved synthetic option ID",
  "requested_start_at": "2026-11-20T09:00:00-05:00",
  "requested_end_at": "2026-11-20T10:00:00-05:00",
  "contact_name": "Synthetic Canary",
  "contact_email": "canary@example.invalid",
  "purpose": "Approved closed-test canary"
}
```

POST it only to `/api/v1/public/booking/requests` with the retained
`Idempotency-Key` and `X-Apollo-Public-Intake-Channel: public_web`. The example is
not approved fixture data. Staff decision bodies carry `expected_version` and
optional internal notes; public messages use the separate `public_message`
field. Browser-to-frontend form actions remain the required external journey,
not a direct-API substitute.

## Publication automation audit

APOLLO and ATHENA `.github/workflows/ci-image.yml` run tests and build/publish
GHCR `main` and SHA tags after main pushes. The live Deployments pin immutable
digests. Live Flux has only Prometheus main as its Git source; `apps` is
suspended and image toolkit resources are absent. Thus scoped service-source
pushes publish images without an observed reconciliation path. This does not
authorise any rollout, GitOps push, Flux resume or secret/config change.

Hestia/Themis have no tracked image publication workflows. Platform is docs-only.
Remote source SHA, actual CI outcome and image publication are recorded
separately below; none imply deployed acceptance.

## Frontend executed results and failure classification

Both repositories passed `npm run check`, `npm test` and `npm run build` with
existing dependencies and installed Playwright browsers. No prerequisite was
installed and no lockfile/configuration was changed.

| Exact command / scope | Executed result | Meaning |
| --- | --- | --- |
| Hestia `npm run test:e2e` | 39 passed, 1 failed | All 18 public intake/options/availability/receipt/status assertions passed across desktop/mobile; member lobby mutation failed on desktop. |
| Hestia `npx playwright test tests/e2e/shell.spec.ts --grep 'member shell stays member-safe' --workers=1` | 2 passed | Isolated member rerun is green; initial full-suite failure remains disclosed as unresolved flakiness. |
| Themis `npm run test:e2e` | 77 passed, 6 failed, 1 skipped | Four booking/schedule failures plus two competition selector failures; not demo-ready. |
| Themis `npx playwright test tests/e2e/shell.spec.ts --grep 'manager can create and cancel|manager booking edit form preserves|staff can triage public|competition command denials' --workers=1` | 4 passed, 4 failed | Edit/message cases passed; cancellation and duplicate-text selector failures reproduced in both browser projects. |

Exact failure excerpts from the requested full commands:

```text
Hestia shell.spec.ts:52 desktop:
getByRole('button', { name: 'Leave lobby' }): expected visible; element not found.

Themis shell.spec.ts:237 desktop/mobile:
getByText('trusted surface token is invalid') resolved to 2 elements.

Themis shell.spec.ts:274 desktop/mobile:
getByRole('button', { name: 'Cancel block' }): expected 1; received 0.

Themis shell.spec.ts:957, called from :500 desktop:
Resource key: expected empty string; received "court-101".

Themis shell.spec.ts:388 mobile:
getByText('We are reviewing your updated details.').first(): element not found.
```

The cancellation fixture uses April 24, 2026 and the actual UI compares its end
time to `new Date()`, so the future-only action is correctly absent on October
20. Repair the dated fixture/clock in the next packet. The competition assertion
is an ambiguous selector over two matching messages, not a denied-operation
failure. The member lobby, edit and public-message failures did not reproduce
in isolated serial runs; do not call those initial results passes or assert a
root cause without further evidence. All these e2e tests use mock APOLLO and
prove frontend behaviour only. Raw local outputs remain at
`/tmp/r1-{hestia,themis}-{check,unit,build,e2e}.log`, with isolated logs at
`/tmp/r1-hestia-member-recheck.log` and `/tmp/r1-themis-booking-recheck.log`.
They are not committed artifacts or deployed acceptance.

The next implementation packet must separate these frontend repairs from
read-only backend proof and then test the nonempty external journey with real
APOLLO. R1 does not silently absorb frontend runtime work.

## Reproduce the confirmed frontend failure states

These commands use installed TypeScript, transpile current functions in memory
and inject upstream behaviour without file writes or network requests. Run
from the named frontend checkout. They supplement browser tests and do not
replace real-service proof.

Hestia:

```sh
node --input-type=module <<'NODE'
import fs from 'node:fs';
import ts from 'typescript';
let source = fs.readFileSync('src/lib/server/public-intake.ts', 'utf8')
  .replace(/^import .* from '\$lib\/.*';\n/gm, '');
source = "const apolloOrigin = () => 'http://127.0.0.1:1';\n" + source;
const compiled = ts.transpileModule(source, {
  compilerOptions: { module: ts.ModuleKind.ES2022, target: ts.ScriptTarget.ES2022 }
}).outputText;
const intake = await import('data:text/javascript;base64,' +
  Buffer.from(compiled).toString('base64'));
const fields = {
  ...intake.blankFields(), option_id: 'test-option',
  requested_start_at: '2026-11-20T14:00:00Z',
  requested_end_at: '2026-11-20T15:00:00Z',
  contact_name: 'Synthetic Canary', contact_email: 'canary@example.invalid'
};
for (const [name, fn] of [
  ['receipt', () => intake.submitIntake(fields,
    async () => new Response('{}', { status: 202 }))],
  ['status', () => intake.lookupPublicStatus({ receipt_code: 'BR-TEST' },
    async () => new Response('{}', { status: 200 }))],
  ['network', () => intake.lookupPublicStatus({ receipt_code: 'BR-TEST' },
    async () => { throw new TypeError('fetch failed'); })]
]) {
  try { console.log(name, JSON.stringify(await fn())); }
  catch (error) { console.log(name, 'REJECTED:', error.message); }
}
NODE
```

Expected current failures: receipt `status must be a string`; status
`receipt_code must be a string`; network `fetch failed`.

Themis:

```sh
node --input-type=module <<'NODE'
import fs from 'node:fs';
import ts from 'typescript';
let source = fs.readFileSync('src/routes/ops/bookings/new/+page.server.ts', 'utf8')
  .replace(/^import (?!.*node:crypto).*\n/gm, '');
source = `
class ApiError extends Error { constructor(status) { super('test'); this.status = status; } }
const redirect = (status, location) => ({ status, location });
const fail = (status, value) => ({ status, value });
const createApolloServerClient = () => ({ listBookingRequests: async () => {
  throw new Error('upstream connection refused');
} });
` + source;
const compiled = ts.transpileModule(source, {
  compilerOptions: { module: ts.ModuleKind.ES2022, target: ts.ScriptTarget.ES2022 }
}).outputText;
const route = await import('data:text/javascript;base64,' +
  Buffer.from(compiled).toString('base64'));
const result = await route.load({
  request: new Request('http://localhost/ops/bookings/new')
});
console.log(JSON.stringify({ canManage: result.canManage,
  hasSafetyKey: Boolean(result.idempotencyKey),
  hasErrorState: 'state' in result || 'loadError' in result }));
NODE
```

Observed: `{"canManage":false,"hasSafetyKey":true,"hasErrorState":false}`.

## G1-G3 verification ledger

All commands ran with existing installed dependencies. Final source was
rechecked after review repairs. The initial APOLLO broad race failure is kept
below; the serial diagnostic does not replace its disclosure.

| Repository / command | Result | Local evidence |
| --- | --- | --- |
| ATHENA `go test -count=1 ./internal/edge ./internal/edgehistory ./internal/presence ./internal/publish ./internal/server ./cmd/athena` | PASS; exit 0; 23.78s | `/tmp/r1-athena-final-focused.log` |
| ATHENA `go vet ./...` | PASS; exit 0; 0.46s | `/tmp/r1-athena-final-vet.log` |
| ATHENA `go build ./cmd/athena` | PASS; exit 0; 3.7s | `/tmp/r1-athena-final-build.log` |
| ATHENA `go test -count=1 ./...` | PASS; exit 0; 25.82s | `/tmp/r1-athena-final-full.log` |
| ATHENA `go test -race ./internal/...` | PASS; exit 0; 25.62s | `/tmp/r1-athena-final-race.log` |
| ATHENA `git diff --check` | PASS; exit 0; 0.02s | `/tmp/r1-athena-final-diff.log` |
| APOLLO `go test -count=1 ./internal/athena ./internal/presence ./internal/consumer ./internal/visits ./internal/booking ./internal/schedule ./internal/authz ./internal/server ./cmd/apollo` | PASS; exit 0; 193.47s | `/tmp/r1-apollo-focused.log` |
| APOLLO `go test -race ./internal/...` | FAIL, retained; exit 1; 356.24s | `/tmp/r1-apollo-race.log` |
| APOLLO `go test -count=1 ./internal/server -run Test(PublicBooking|BookingRequest|Schedule)` | PASS; exit 0; 38.48s | `/tmp/r1-apollo-booking-no-athena.log` |
| APOLLO `go test -count=1 -v ./cmd/apollo -run TestPresenceAthenaGateRealServicePostgres` | PASS; exit 0; 8.36s; explicit real-service opt-in enabled | `/tmp/r1-apollo-final-cross-service.log` |
| APOLLO `go test -count=1 ./internal/athena ./internal/presence ./cmd/apollo` | PASS; exit 0; 19.58s | `/tmp/r1-apollo-final-changed-focused.log` |
| APOLLO `go vet ./...` | PASS; exit 0; 0.59s | `/tmp/r1-apollo-final-vet.log` |
| APOLLO `go build ./cmd/apollo` | PASS; exit 0; 1.9s | `/tmp/r1-apollo-final-build.log` |
| APOLLO `go test -count=1 ./...` | PASS; exit 0; 198.21s | `/tmp/r1-apollo-final-full.log` |
| APOLLO `git diff --check` | PASS; exit 0; 0.04s | `/tmp/r1-apollo-final-diff.log` |
| APOLLO `go test -race -p 1 ./internal/...` | PASS; exit 0; 185.43s | `/tmp/r1-apollo-race-serial-diagnostic.log` |
| APOLLO `go test -race ./internal/...` | PASS; exit 0; 0.45s; cached after successful serial run | `/tmp/r1-apollo-race-requested-recheck.log` |

The exact original broad race failure was unrelated to bridge code:

```text
--- FAIL: TestWorkoutRecommendationRuntimeFollowsDeterministicPrecedence (154.91s)
recommendation_runtime_integration_test.go:18: StartPostgres() error =
postgres did not become ready within 2m0s: failed to connect to
user=apollo database=apollo at 127.0.0.1:5432: connect: connection refused
FAIL github.com/ixxet/apollo/internal/server
```

No data-race diagnostic was emitted. The full serial race diagnostic passed;
the exact requested command then passed with cached successful results. A
fixture-startup/concurrency issue is the supported classification, not a proven
bridge regression or a reason to change shared infrastructure under R1. The
raw initial failure remains `/tmp/r1-apollo-race.log`.

Hestia check/unit/build pass (27 unit tests); Themis check/unit/build pass
(38 unit tests). Their exact e2e failures and isolated reruns are recorded above.
Platform and changed Markdown passed `git diff --check`; added local links were
resolved, 20 shell blocks parsed with `bash -n`, and both saved
no-network frontend reproduction commands executed successfully. Neither
frontend full e2e command is claimed green.

The final APOLLO text-only follow-up keeps incomplete context visible even when
there are zero evidence rows. `go test -count=1 ./cmd/apollo`, the opted-in real
cross-service rehearsal, `go vet ./...`, `go build ./cmd/apollo` and
`git diff --check` all passed after that addition. Logs are
`/tmp/r1-apollo-text-{cli,cross-service,vet,build,diff}.log`. No internal runtime
or shared analytics code changed in this follow-up.

## G4 GitHub publication and current closeout

Source and repo documentation pushes are verified independently with
`git ls-remote origin refs/heads/main`. No PR/issue tracker was invented; G0's
issue/PR searches found no overlapping entries. Only scoped source, tests and
Markdown were staged. No test artifacts, unrelated dirty docs, GitOps manifests
or secrets were committed.

| Repo | Implementation / docs commits | Verified remote main | CI / image state at this capture |
| --- | --- | --- | --- |
| ATHENA | [`43bcfb6`](https://github.com/ixxet/athena/commit/43bcfb6958a68945d789558d57129eabd8501aa6) runtime; [`b52376b`](https://github.com/ixxet/athena/commit/b52376b1551deb5ca810d99598aab1ceb9954325) contract/runbook/README | `b52376b1551deb5ca810d99598aab1ceb9954325` | Both observed ci-image runs passed tests and image publication. |
| APOLLO | [`7fab80e`](https://github.com/ixxet/apollo/commit/7fab80e0373417a8832cde9c7e425bac213448c3) runtime/proof; [`43b52f9`](https://github.com/ixxet/apollo/commit/43b52f9bee6d01be77e73345c31ba9608c6b08af) docs; [`c64b349`](https://github.com/ixxet/apollo/commit/c64b34928f090f48f7ff5ead73d5fbceaf29eb33) explicit text completeness | `c64b34928f090f48f7ff5ead73d5fbceaf29eb33` | Final-head test and image jobs succeeded; published registry digest verified below. |
| Hestia | [`13f5cbc`](https://github.com/ixxet/hestia/commit/13f5cbcaf41fc907fb911f41abd41f647e034c97) scoped README readiness note | `13f5cbcaf41fc907fb911f41abd41f647e034c97` | No configured workflow/image publication; local frontend results remain above. |
| Themis | [`15ca377`](https://github.com/ixxet/themis/commit/15ca377f27f2edb0b1528b1d270c120032911319) scoped README readiness note | `15ca377f27f2edb0b1528b1d270c120032911319` | No configured workflow/image publication; local frontend results remain above. |
| Platform | [`802ac0a`](https://github.com/ixxet/ashton-platform/commit/802ac0a4f338f269f0ae8a944961e7b416c3d6f0) canonical readiness record plus SAIL, CORE-ONE-PART-TWO, IMPLEMENTATION-BOARD, bridge preflight, route/API matrix, FLOWS, DEFERMENTS and compatibility overlay; this file's follow-up records final CI and smoke corrections | Initial publication `802ac0a4f338f269f0ae8a944961e7b416c3d6f0` verified; final closeout SHA is this file's latest Git history entry | Docs-only; no runtime/image workflow. |

ATHENA CI: [run 39445588763](https://github.com/ixxet/athena/actions/runs/39445588763)
and [run 39445587824](https://github.com/ixxet/athena/actions/runs/39445587824)
both succeeded. They published the same source SHA in two observed push runs;
only one configured workflow exists and the reason for duplicate runs was not
established. Their image-index digests differ. A registry read confirms the
SHA tag currently resolves to:

```text
ghcr.io/ixxet/athena:sha-b52376b@sha256:6d0165f85f184406e8a4d056c9290ed6be4301102945f3ca60c922efdf452df0
```

The other run's pushed digest was
`sha256:996278887888c74ffe9a1788bb6c29a17c8e02d1443ae6871dbc2f2abbaae18c`.
Approval must choose an immutable digest, never assume a mutable `main` or SHA
name is immutable. The registry index includes linux/amd64 and linux/arm64.
APOLLO CI for the [initial docs head](https://github.com/ixxet/apollo/actions/runs/39445594339)
and [final text-completeness head](https://github.com/ixxet/apollo/actions/runs/39446321522)
both succeeded. The final head's test job completed at 15:20:14 UTC and image
job at 15:21:58 UTC on 2026-10-20. A registry read confirms the final SHA tag
resolves to the following linux/amd64 and linux/arm64 image index:

```text
ghcr.io/ixxet/apollo:sha-c64b349@sha256:6a70af9d891accd3252ff68754fac6440d5cf87300c234420b7563fcf6fa8819
```

The earlier APOLLO docs head published
`sha-43b52f9@sha256:55d8bb9730d3802272605ff2006d1e7c526a5caef19a2868735ae3581a9367b0`;
it does not contain the final text-completeness follow-up. Registry verification
used `docker buildx imagetools inspect ghcr.io/ixxet/apollo:sha-c64b349 --format '{{json .Manifest}}'`
and the equivalent ATHENA SHA-tag inspection. Published
image indices are candidates for a separately approved rollout, not deployed
acceptance.

| Gate | Verdict |
| --- | --- |
| G0 current source / overlap | Passed; all six actual remotes verified, dirty work preserved and old Hestia/Themis access gaps resolved. |
| G1 bridge runtime | Repaired and locally verified; explicit incomplete/unknown evidence fails closed. |
| G2 real local proof | Passed actual ATHENA HTTP/service/PostgreSQL -> APOLLO client/classifier/CLI, with unchanged APOLLO product counts. |
| G3 booking / frontend / ops | Audit executed; external closed-test readiness is blocked by the concrete frontend, packaging, staff/login, inventory, migration/restore and approval gaps above. |
| G4 documentation / publication | Scoped source and canonical/repo docs published; all five ASHTON remote heads verified. Final backend tests and image publication succeeded; registry digests verified. |

The external booking launch is not closed. Next packet: Booking/Scheduling
Closed-Test Implementation + Deployment, centred on Hestia, Themis and APOLLO.
Resolve the listed gaps and prove external request -> staff approval -> real
reservation -> customer status/cancellation after fresh live approval. Do not
substitute daily credits, public tournaments or OpenSkill work.

Unrelated working-tree state is preserved: APOLLO's six pre-existing dirty docs
and two generated artifacts; ATHENA's two generated artifacts; platform's nine
dirty docs; Prometheus's seven dirty files. Hestia/Themis are clean after their
scoped README commits. No runtime/configuration file was changed in either
frontend or Prometheus.

Live state remains unchanged: the deployed APOLLO and ATHENA digests are the
historical pinned digests in G3, not the newly published images. A bounded
read-only safety refresh after final image publication reconfirmed both desired
image pins, one ready replica each, `apps` suspended and no image automation
resources. No rollout, deployment
manifest write, secret operation, live migration, fixture mutation, restart,
kill or destructive probe was performed. Live execution still requires the
fresh target/images/diff/secrets/migrations/fixtures/window/rollback/abort
approval described above.
