# ASHTON Deferments Register

This is the living control-plane register for deferred, closed, deprecated,
replaced, and post-launch work. Every future packet must update this file when
scope is deferred, closed, deprecated, replaced, or moved post-launch.

Repo-local docs and `planning/IMPLEMENTATION-BOARD.md` remain implementation
truth. This file records coordination truth and hard stops.

## Active Deferred Items

| Item | Current status | Owning repo or likely repo | Why deferred | Reopen trigger | Hard stop / non-goal | Last touched phase |
| --- | --- | --- | --- | --- | --- | --- |
| Customer status/communication | Deferred | `apollo`, `hestia`; Themis only for staff triage | Public intake now stops at neutral receipt | Public submitters need honest status/contact updates | No raw conflict truth, staff notes, self-booking, payment, or deploy claim | Phase 3B.6 |
| Edit/rebook flow | Deferred | `apollo`, `themis` | Approval/cancellation truth closed first | Operators need request changes without schedule-truth bypass | No payment sprawl, owner policy writes, or Hestia staff controls | Phase 3B.6 |
| Bounded staff scheduling controls | Deferred | `apollo`, `themis` | Current line proves booking request workflow only | Operators need direct schedule rails before more booking work | No public availability calendar, admin blob, ATHENA widening, gateway, or deploy claim | Phase 3B.6 |
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

## Post-Launch Watch Later

| Item | Current status | Owning repo or likely repo | Why deferred | Reopen trigger | Hard stop / non-goal | Last touched phase |
| --- | --- | --- | --- | --- | --- | --- |
| AI sentiment on feedback | Watch later | `apollo` or analytics repo if earned | No useful feedback corpus yet | Enough labeled feedback exists | AI may summarize, not decide policy | Phase 3B.6.1 |
| AI recommendations | Watch later | `apollo` | Deterministic rails remain primary | Deterministic recommendation gaps are measured | No LLM-first decision core | Phase 3B.6.1 |
| Occupancy forecasting | Watch later | `athena`, maybe `hermes` | Historical data volume is not ready | Stable occupancy history accumulates | No forecast-as-truth in ops decisions | Phase 3B.6.1 |
| Churn prediction | Watch later | `apollo` or analytics repo if earned | Membership/billing truth is not present | Retention dataset and policy exist | No automated adverse decisions | Phase 3B.6.1 |
| Tap anomaly detection | Watch later | `athena`, `hermes` | Need stable tap/visit baselines first | False-positive-tolerant ops workflow exists | No silent enforcement | Phase 3B.6.1 |
| Summarization / Q&A helpers | Watch later | `hermes`, `apollo`, gateway if earned | Helper reads exist but runtime authority is narrow | Staff/member support load justifies it | No model-owned durable truth or writes | Phase 3B.6.1 |

## Closed / Resolved Deferred Items

| Item | Current status | Owning repo or likely repo | Why deferred | Reopen trigger | Hard stop / non-goal | Last touched phase |
| --- | --- | --- | --- | --- | --- | --- |
| Public booking-request intake | Closed as request-only intake | `apollo`, `hestia`, `themis` | Deferred until internal booking truth existed | Reopen only for status/edit/scheduling/payment planning | No self-booking, availability calendar, payment, or deploy claim | Phase 3B.6 |
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
