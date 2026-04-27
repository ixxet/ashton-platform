# ASHTON Deferments Register

This is the living control-plane register for deferred, closed, deprecated,
replaced, and post-launch work. Every future packet must update this file when
scope is deferred, closed, deprecated, replaced, or moved post-launch.

Repo-local docs and `planning/IMPLEMENTATION-BOARD.md` remain implementation
truth. This file records coordination truth and hard stops.

## Active Deferred Items

| Item | Current status | Owning repo or likely repo | Why deferred | Reopen trigger | Hard stop / non-goal | Last touched phase |
| --- | --- | --- | --- | --- | --- | --- |
| Public self-booking | Deferred | `apollo`, `hestia`; Themis only for staff review context | Phase 3B.9 closes availability-informed requests only | Business explicitly chooses customer-confirmed reservations and the privacy/conflict contract is proven | No bypassing staff approval, payment sprawl, public conflict truth, private labels, contact info, or deploy claim | Phase 3B.9 |
| Public booking self-edit/rebook | Deferred | `apollo`, `hestia`; Themis only for staff review context | Phase 3B.8 closed staff-side edit/replacement only | Customers need to amend submitted requests without exposing internal truth | No public schedule conflict details, staff controls, in-place approved mutation, payment sprawl, or deploy claim | Phase 3B.8 |
| In-place approved booking mutation | Deferred | `apollo`, `themis` | Phase 3B.8 uses replacement requests so approved reservations stay untouched | A narrower audited mutation proof is explicitly earned | No direct schedule-block mutation, public self-service, payment sprawl, or owner policy writes by implication | Phase 3B.8 |
| Recurring schedule rules | Deferred | `apollo`, `themis` | Phase 3B.10 closes single-instance staff blocks only | Repeat operating patterns are explicit and conflict policy is proven | No recurrence by implication from one-off staff controls | Phase 3B.10 |
| Broad operating-hours editor | Deferred | `apollo`, `themis` | Phase 3B.10 intentionally avoids broad policy editing | Facility operating policy needs editable admin rails | No casual policy blob or public calendar drift | Phase 3B.10 |
| Tournament/session schedule controls | Deferred | `apollo`, `themis` | Competition/session controls are separate from facility holds | Tournament or session workflow earns a dedicated contract | No mutation of competition/session truth through generic schedule blocks | Phase 3B.10 |
| Supervisor schedule proposals | Deferred | `apollo`, `themis` | Supervisors are first-line read-only in Phase 3B.10 | Supervisor draft/proposal workflow becomes a real operational need | No supervisor mutation by implication | Phase 3B.10 |
| Public competition surfaces | Deferred behind launch-expansion gates | `apollo`, `hestia`, `themis` | Internal result/rating truth exists, but disputes, privacy, scale, frontend contract, and compatibility gates are not closed | `apollo/docs/launch-expansion-audit.md` gates are satisfied for the target surface | No public ratings, leaderboards, records, rivalry, tournament pages, or social display by implication | Launch expansion audit |
| OpenSkill / ARES v2 public stakes | Planned behind rating trust substrate | `apollo` | Current ratings are legacy APOLLO projections and ARES is preview-only | Rating extraction, policy versioning, rating audit, golden cases, and OpenSkill dual-run are complete | No hard swap from populated legacy ratings; no permanent competing Elo/OpenSkill public scores | Launch expansion audit |
| Retention game layer | Deferred behind trust and privacy gates | `apollo`, `hestia` | CP, badges, squads, quests, and rivalry amplify whatever truth is underneath | Result correction/dispute, reporting/moderation, scale, and rating trust gates are closed | No badges, CP, public rivalry, squads, bounty, or public records from uncorrectable/untrusted results | Launch expansion audit |
| Payment/quote planning | Deferred | Planning first, then likely `apollo` plus customer shell | Commercial flow needs policy before runtime | Explicit commercial-readiness packet | No processor integration, checkout UI, deposits, invoices, or readiness claim | Phase 3B.6 |
| Hestia `hooks.server.ts` auth/CSRF/error hardening | Deferred | `hestia` | Security proof deserves its own packet | Security hardening line starts | Do not smuggle into request intake or UI cleanup | Phase 3B.6.1 |
| Booking `ListRequests` N+1 | Deferred | `apollo` | Not blocking current cleanup | Pre-scale or measured ops volume pressure | Do not optimize inside product-scope packets | Phase 3B.6.1 |
| OpenAPI / HTTP contract generation | Deferred to 3C | Cross-repo, likely `ashton-proto` plus service repos | Contract drift is not yet expensive enough | Multiple frontends/APIs make drift costly | No generated contract churn in 3B hardening | Phase 3B.6.1 |
| RFC 7807 error format | Deferred to 3C | Cross-repo | Error shape is cross-cutting | Contract stabilization line starts | Do not change runtime error envelopes casually | Phase 3B.6.1 |
| Billing/membership | Deferred | `apollo`, `hestia`; maybe platform planning | Needed before second real customer, not current intake | Commercial readiness before second customer | No payments, invoices, or checkout by implication | Phase 3B.6.1 |
| Multi-tenancy | Deferred | Cross-repo | Single-facility truth is still intentional | Multiple independent facilities/customers are real | No speculative tenant abstraction | Phase 3B.6.1 |
| Observability | Deferred before production launch | Service repos plus Prometheus | Launch proof needs telemetry but not this cleanup | Production launch hardening starts | Do not bundle with product features | Phase 3B.6.1 |
| NATS clustering | Deferred | Prometheus, service repos | Single-node/bounded deployment truth remains enough | Production reliability requirement | No deploy implication in repo/runtime packets | Phase 3B.6.1 |
| Read replicas | Deferred | Prometheus, Postgres consumers | Not needed before measured read pressure | Read load exceeds primary comfort | No premature data-topology work | Phase 3B.6.1 |
| PgBouncer | Deferred | Prometheus, service repos | Connection pressure not proven | Connection limits become operational risk | No hidden deploy/runtime dependency | Phase 3B.6.1 |
| Projector partitioning | Deferred | `athena` | Current projector truth is sufficient | Retention/replay volume proves partition need | No partition complexity before data exists | Phase 3B.6.1 |
| ATHENA accepted-presence session cutover | Deferred | `athena` | `v0.8.x` intentionally keeps `edge_sessions` source-pass-only while accepted presence settles | Accepted-presence truth is stable enough to support stay-duration semantics without muddying source truth | No silent session rewrite or duration claim from testing-policy accepted fails | ATHENA `v0.8.x` |
| ATHENA policy/identity operator UI | Deferred | `athena`, `hermes` | CLI-first is enough for the policy-backed admission line | Operators need repeatable non-CLI workflows over the same explicit policy and identity model | No casual HTTP admin surface or write widening by implication | ATHENA `v0.8.x` |
| ATHENA public report/export surfaces | Deferred | `athena`, `hermes` | Observation and accepted-presence truth need to settle before broader reporting shape freezes | Internal reads are stable and privacy review is explicit | No public history API, broad export path, or dashboard claim by implication | ATHENA `v0.8.x` |
| ATHENA alias-management UX | Deferred | `athena`, `hermes` | Facility-local subjects and privacy-safe links now exist, but explicit reconciliation UX is not earned yet | Real operator demand justifies reviewing and curating links beyond CLI use | No name-based auto-merge | ATHENA `v0.8.x` |

## Post-Launch Watch Later

| Item | Current status | Owning repo or likely repo | Why deferred | Reopen trigger | Hard stop / non-goal | Last touched phase |
| --- | --- | --- | --- | --- | --- | --- |
| AI sentiment on feedback | Watch later | `apollo` or analytics repo if earned | No useful feedback corpus yet | Enough labeled feedback exists | AI may summarize, not decide policy | Phase 3B.6.1 |
| AI recommendations | Watch later | `apollo` | Deterministic rails remain primary | Deterministic recommendation gaps are measured | No LLM-first decision core | Phase 3B.6.1 |
| Occupancy forecasting | Watch later | `athena`, maybe `hermes` | Historical data volume is not ready | Stable occupancy history accumulates | No forecast-as-truth in ops decisions | Phase 3B.6.1 |
| Churn prediction | Watch later | `apollo` or analytics repo if earned | Membership/billing truth is not present | Retention dataset and policy exist | No automated adverse decisions | Phase 3B.6.1 |
| Tap anomaly detection | Watch later | `athena`, `hermes` | Need stable tap/visit baselines first | False-positive-tolerant ops workflow exists | No silent enforcement | Phase 3B.6.1 |
| Summarization / Q&A helpers | Watch later | `hermes`, `apollo`, gateway if earned | Helper reads exist but runtime authority is narrow | Staff/member support load justifies it | No model-owned durable truth or writes | Phase 3B.6.1 |
| Public booking display labels | Watch later | `apollo`, `hestia`, `themis` | Public calendar/display policy is not modeled yet | Facility wants approved bookings/events visible publicly | No contact info, internal notes, or unapproved requester identity | Phase 3B.7 planning |
| Approved recurring booking self-service | Watch later | `apollo`, `hestia`, `themis` | Recurrence needs staff-approved policy and conflict rules first | Staff-approved recurring policy exists and repeat requests are common | No automatic confirmation outside policy or without conflict checks | Phase 3B.7 planning |
| Demand heatmaps and booking analytics | Watch later | `apollo`, `hermes`, `themis` | Need real request/approval/occupancy volume | Managers need seasonal or facility-slice demand insight | No forecast-as-truth, rate automation, or staffing mandate | Phase 3B.7 planning |
| AI-assisted quote negotiation | Watch later | Future AI service, `apollo`, `themis` | Pricing policy and quote runtime do not exist | Quote policy exists and management approves assisted negotiation | No autonomous binding quotes, discounts, or customer-visible AI authority | Phase 3B.7 planning |

## Closed / Resolved Deferred Items

| Item | Current status | Owning repo or likely repo | Why deferred | Reopen trigger | Hard stop / non-goal | Last touched phase |
| --- | --- | --- | --- | --- | --- | --- |
| Public booking-request intake | Closed as request-only intake | `apollo`, `hestia`, `themis` | Deferred until internal booking truth existed | Reopen only for status, public self-edit/rebook, self-booking, or payment planning | No self-booking, payment, or deploy claim by implication | Phase 3B.6 |
| Customer status/communication | Closed as receipt status/message lookup | `apollo`, `hestia`, `themis` | Deferred until public intake had receipt truth | Reopen only for broader customer self-service, delivery channels, or public self-edit/rebook | No raw conflict truth, internal notes, self-booking, payment, quote, AI/LLM negotiation, or deploy claim | Phase 3B.7 |
| Staff pending-request edit and approved replacement request | Closed as staff-side edit/replacement | `apollo`, `themis` | Deferred until request/approval/cancellation truth existed | Reopen only for public self-edit/rebook, recurrence, or in-place approved mutation | No public self-edit/rebook, in-place approved mutation, direct schedule editing, payment/quote flow, Hestia staff controls, or deploy claim | Phase 3B.8 |
| Public availability/request calendar | Closed as availability-informed request hints | `apollo`, `hestia`; Themis only for staff review context | Deferred until APOLLO could derive public-safe requestable/unavailable windows | Reopen only for public labels, recurrence, self-edit/rebook, or self-booking after separate proof | No self-confirmed booking, public conflict truth, contact info, quote promise, or deploy claim | Phase 3B.9 |
| Bounded staff schedule controls | Closed as internal single-instance controls | `apollo`, `themis` | Deferred until schedule truth and public availability boundaries were proven | Reopen only for recurrence, broad hours policy, supervisor proposals, tournament/session controls, or approved booking mutation after separate proof | No public self-booking, Hestia staff controls, payments/quotes, broad admin editor, or deploy claim by implication | Phase 3B.10 |
| Themis `/api/v1/public/*` proxy denial | Closed | `themis` | Needed once APOLLO public routes existed | Proxy behavior changes | Themis must not become a public surface | Phase 3B.6 |
| Public intake source/channel triage | Closed | `apollo`, `themis` | Needed for honest staff review | New intake channels are added | Do not treat anonymous submitters as staff | Phase 3B.6 |

## Deprecated / Removed From Scope

| Item | Current status | Owning repo or likely repo | Why deferred | Reopen trigger | Hard stop / non-goal | Last touched phase |
| --- | --- | --- | --- | --- | --- | --- |
| `ashton-booking-intake` active repo line | Removed from active workspace | Folded into `hestia` | Hestia owns `/intake`; donor had no remote | Only local archaeology from archive if needed | Do not create a remote or active repo inventory row | Phase 3B.6.1 |
| Hestia as staff shell | Removed from scope | `hestia`, `themis` | Themis owns privileged ops | None without a platform governance reversal | No staff controls in Hestia | Phase 3B.6 |
| Themis as public surface | Removed from scope | `themis` | Themis is privileged ops only | None without a platform governance reversal | No anonymous public pages or public proxying | Phase 3B.6 |

## Replaced By Another Path

| Item | Current status | Owning repo or likely repo | Why deferred | Reopen trigger | Hard stop / non-goal | Last touched phase |
| --- | --- | --- | --- | --- | --- | --- |
| Dedicated public intake repo | Replaced by Hestia `/intake` | `hestia` | Single customer-facing shell is cleaner than a one-off repo | Only if Hestia ownership reverses | Do not revive `ashton-booking-intake` as remote | Phase 3B.6 |
| Public booking UI in Hestia member app | Replaced by public `/intake` route | `hestia` | Intake is public and request-only, not member self-booking | Customer status/edit lines prove need | No member booking UI or instant booking | Phase 3B.6 |
| Ad hoc scattered deferment notes | Replaced by this register | `ashton-platform` | Closeouts were spreading coordination truth across docs | Every packet closeout | Do not leave new deferrals only in phase prose | Phase 3B.6.1 |

## AI Planning Rules

| Item | Current status | Owning repo or likely repo | Why deferred | Reopen trigger | Hard stop / non-goal | Last touched phase |
| --- | --- | --- | --- | --- | --- | --- |
| AI runtime planning | Docs-only watch later | Cross-repo | Deterministic APOLLO/ATHENA/HERMES truth is primary | Data exists and deterministic rails are stable | No runtime placeholders, UI, or tests for nonexistent AI | Phase 3B.6.1 |
| AI authority boundary | Active rule | Cross-repo | Models may explain, summarize, forecast, or recommend later | Any AI packet starts | AI must not become decision authority or bypass proposal/apply rails | Phase 3B.6.1 |
