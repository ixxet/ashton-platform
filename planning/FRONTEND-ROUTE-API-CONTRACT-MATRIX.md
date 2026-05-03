# Frontend Route/API Contract Matrix

Date: 2026-05-03
Status: Packet 5 docs truth
Scope: Hestia, Themis, APOLLO, and platform planning docs

This matrix freezes current Hestia and Themis route/API consumption truth. It
does not add runtime behavior, product surface, API routes, schema, deployment
truth, or frontend formulas.

APOLLO remains source truth for auth/session, profile, presence, lobby,
booking, schedule, ops overview, competition, rating, public projections, game
identity, safety, and reliability. Hestia and Themis consume APOLLO contracts
only.

## Hard Boundaries

- Deployed truth is unchanged.
- Public tournaments remain blocked.
- OpenSkill active read path remains blocked.
- Public OpenSkill values remain blocked.
- Frontend-owned formulas for ratings, analytics, matchmaking, tournaments,
  safety, public projections, CP, badges, rivalry, or squads remain blocked.
- Public/member safety detail surfaces remain blocked.
- Messaging/chat, broad public social graph, public profiles/scouting, public
  self-booking, public self-edit/rebook, booking/commercial/proposal expansion,
  and deploy/GitOps work remain out of scope.

## Source Evidence

| Area | Evidence |
| --- | --- |
| Hestia routes | `hestia/src/routes`, especially `src/routes/api/v1/[...path]/+server.ts` |
| Hestia server API bridges | `hestia/src/lib/server/public-intake.ts`, `hestia/src/lib/server/public-competition.ts`, `hestia/src/lib/server/client.ts`, `hestia/src/lib/server/apollo.ts` |
| Hestia member client | `hestia/src/lib/api/client.ts` |
| Hestia tests | `hestia/tests/unit/*.test.ts`, `hestia/tests/e2e/*.spec.ts` |
| Themis routes | `themis/src/routes`, especially `src/routes/api/v1/[...path]/+server.ts` |
| Themis API clients | `themis/src/lib/api/client.ts`, `competition.ts`, `bookings.ts`, `schedule.ts`, `ops-overview.ts` |
| Themis server bridge | `themis/src/lib/server/client.ts`, `themis/src/lib/server/apollo.ts` |
| Themis tests | `themis/tests/unit/*.test.ts`, `themis/tests/e2e/shell.spec.ts` |
| APOLLO HTTP owner | `apollo/internal/server/server.go` |
| APOLLO launch truth | `apollo/docs/launch-expansion-audit.md`, `apollo/docs/roadmap.md` |

## APOLLO Contract Ownership Map

| Category | APOLLO endpoints consumed by frontends | Frontend consumer | Source-truth owner |
| --- | --- | --- | --- |
| Auth/session | `POST /api/v1/auth/verification/start`, `GET/POST /api/v1/auth/verify`, `POST /api/v1/auth/logout` | Hestia, Themis | APOLLO auth/session |
| Profile/member shell | `GET/PATCH /api/v1/profile` | Hestia, Themis session check | APOLLO profile |
| Presence/member home | `GET /api/v1/presence`, `GET/POST /api/v1/presence/claims`, `GET /api/v1/presence/facilities`, `GET /api/v1/presence/facilities/{facilityKey}/calendar` | Hestia | APOLLO presence/member facility calendar |
| Lobby/member home | `GET /api/v1/lobby/eligibility`, `GET /api/v1/lobby/membership`, `POST /api/v1/lobby/membership/join`, `POST /api/v1/lobby/membership/leave`, `GET /api/v1/lobby/match-preview` | Hestia | APOLLO lobby/ARES preview |
| Public booking intake | `GET /api/v1/public/booking/options`, `GET /api/v1/public/booking/options/{optionID}/availability`, `POST /api/v1/public/booking/requests` | Hestia | APOLLO booking |
| Public booking status | `GET /api/v1/public/booking/requests/status?receipt_code=...` | Hestia | APOLLO booking |
| Member booking | None | None | Blocked; no Hestia member booking route |
| Staff booking ops | `GET/POST /api/v1/booking/requests`, `GET /api/v1/booking/requests/{id}`, `POST /api/v1/booking/requests/{id}/edit`, `/rebook`, `/public-message`, `/review`, `/needs-changes`, `/reject`, `/cancel`, `/approve` | Themis | APOLLO booking |
| Staff schedule ops | Current Themis use: `GET /api/v1/schedule/blocks?facility_key=...`, `POST /api/v1/schedule/blocks`, `POST /api/v1/schedule/blocks/{id}/cancel`. APOLLO also owns `GET /api/v1/schedule/calendar` and `POST /api/v1/schedule/blocks/{id}/exceptions`, but current Themis routes do not consume them. | Themis | APOLLO schedule |
| Public competition readiness | `GET /api/v1/public/competition/readiness` | Hestia | APOLLO competition public projection |
| Public leaderboard | `GET /api/v1/public/competition/leaderboards` | Hestia | APOLLO competition public projection |
| Public game identity | `GET /api/v1/public/competition/game-identity` | Hestia | APOLLO game identity projection |
| Member competition stats/history/game identity | `GET /api/v1/competition/member-stats`, `GET /api/v1/competition/history`, `GET /api/v1/competition/game-identity` | Hestia | APOLLO competition/game identity |
| Internal competition commands/sessions | `GET /api/v1/competition/commands/readiness`, `POST /api/v1/competition/commands`, `GET /api/v1/competition/sessions`, `GET /api/v1/competition/sessions/{id}` | Themis | APOLLO competition command/session |
| Internal safety/reliability | `GET /api/v1/competition/safety/readiness`, `GET /api/v1/competition/safety/review` | Themis | APOLLO manager/internal safety/reliability |
| Ops overview | `GET /api/v1/ops/facilities/{facilityKey}/overview?from=...&until=...&bucket_minutes=...` | Themis | APOLLO ops overview over APOLLO schedule and ATHENA occupancy/analytics |

## Hestia Route Matrix

Hestia is the customer-facing shell. It must not own staff controls or formulas.

| Route | Classification | Auth/session and role behavior | APOLLO endpoints and methods | Mediation | States | Production/stub/mock status | Tests | Owner and blocked adjacent scope |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `/` | Public entry redirect | Reads APOLLO session; unauthenticated or unavailable redirects to `/login`; authenticated redirects to `/app/home` | `GET /api/v1/profile` | Server-mediated via `createApolloServerClient` | No empty/loading view; no denied view; APOLLO failure routes to login | Production-backed APOLLO profile; tests use mock APOLLO | `tests/e2e/shell.spec.ts` root route test | APOLLO auth/profile owns truth; no landing/product widening |
| `/login` | Public auth | Authenticated sessions redirect to `/app/home`; missing session renders APOLLO verification UI | `GET /api/v1/profile`, `POST /api/v1/auth/verification/start`, `POST /api/v1/auth/verify` | Profile check server-mediated; auth form calls same-origin proxy | Loading/pending text for verification; backend unavailable state; auth errors from APOLLO; no role-specific denial | Production-backed APOLLO auth; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/api-client.test.ts` | APOLLO owns auth/session cookie; no frontend auth model |
| `/intake` | Public | No member session required | `GET /api/v1/public/booking/options`, `GET /api/v1/public/booking/options/{optionID}/availability`, `POST /api/v1/public/booking/requests` | Server-mediated public APOLLO calls; browser `/api/v1/public/*` proxy denied | Empty options state; no separate SSR loading state; availability form transition/pending; options unavailable; availability unavailable with request still possible; validation/conflict/unavailable submit messages; unsupported direct public proxy is 404 | Production-backed public booking contracts; Playwright/mock APOLLO coverage | `tests/unit/public-intake.test.ts`, `tests/e2e/intake.spec.ts` | APOLLO booking owns validation, idempotency, source/channel, availability, and no-reservation-on-submit truth; no confirmed booking, self-booking, payment, quote, deposit, invoice, staff, or public self-edit/rebook |
| `/intake/status` | Public | No member session required | `GET /api/v1/public/booking/requests/status?receipt_code=...` | Server-mediated public APOLLO call; browser `/api/v1/public/*` proxy denied | Empty initial form; no separate loading state; unknown receipt neutral failure; 4xx guidance; 5xx unavailable; unsupported direct public proxy is 404 | Production-backed public status contract; Playwright/mock APOLLO coverage | `tests/unit/public-intake.test.ts`, `tests/e2e/intake.spec.ts` | APOLLO booking owns opaque receipt/status/public message; no UUID, internal notes, conflicts, staff IDs, payment/quote fields, edit/rebook, or broader customer portal |
| `/competition` | Public | No member session required | `GET /api/v1/public/competition/readiness`, `GET /api/v1/public/competition/leaderboards?stat_type=wins&team_scope=all&limit=10`, `GET /api/v1/public/competition/game-identity?team_scope=all&limit=10` | Server-mediated public APOLLO calls; browser `/api/v1/public/*` proxy denied | Empty public records/CP/badges/rivalry/squads; no separate loading state; records unavailable state; unsupported direct public proxy is 404 | Production-backed public projection contracts; tests use mock APOLLO | `tests/unit/public-competition.test.ts`, `tests/e2e/shell.spec.ts` public competition tests | APOLLO competition/game identity owns projections; no public tournaments, public profiles/scouting, public safety, OpenSkill values, internal analytics, or frontend CP/badge/rivalry/squad formulas |
| `/app` | Member | Requires APOLLO session through app layout; unauthenticated redirects to `/login`; route redirects to `/app/home` | `GET /api/v1/profile` | Server-mediated app layout and redirect | No empty/loading view; bootstrap error comes from app layout | Production-backed APOLLO profile; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/routing.test.ts` | APOLLO auth/profile owns shell bootstrap; no new app section |
| `/app/home` | Member | Requires APOLLO session; member-only shell; section blocked when app bootstrap fails | `GET /api/v1/profile`, `GET /api/v1/presence`, `GET/POST /api/v1/presence/claims`, `GET /api/v1/presence/facilities`, `GET /api/v1/presence/facilities/{facilityKey}/calendar`, `GET /api/v1/lobby/eligibility`, `GET /api/v1/lobby/membership`, `GET /api/v1/recommendations/workout`, `GET /api/v1/recommendations/coaching`, `GET /api/v1/recommendations/nutrition` | Server-mediated load; browser proxy for tap-claim mutation | Empty linked visits, recent taps, tap claims, facility hours, calendar; no separate SSR loading state; tap claim pending/error; section error; calendar scoped error; bootstrap blocked | Production-backed APOLLO member-safe contracts; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/member-ui.test.ts`, `tests/unit/sections.test.ts`, `tests/unit/api-client.test.ts` | APOLLO presence/lobby/recommendation owns truth; no schedule staff route, booking UI, staff controls, or inferred physical truth |
| `/app/workouts` | Member | Requires APOLLO session; section blocked when app bootstrap fails | `GET /api/v1/workouts`, `POST /api/v1/workouts`, `PUT /api/v1/workouts/{id}`, `POST /api/v1/workouts/{id}/finish`, `GET /api/v1/planner/templates`, `GET /api/v1/planner/exercises`, `GET /api/v1/planner/equipment`, `GET /api/v1/recommendations/coaching` | Server-mediated load; browser proxy for workout create/update/finish | Empty workout list and no selection; form pending/success/error; section error; bootstrap blocked | Production-backed APOLLO workout/planner/coaching contracts; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/member-ui.test.ts`, `tests/unit/api-client.test.ts` | APOLLO workouts/planner/coaching owns truth; no recommendation formula or staff schedule/booking surface |
| `/app/meals` | Member | Requires APOLLO session; section blocked when app bootstrap fails | `GET /api/v1/recommendations/nutrition`, `GET /api/v1/nutrition/meal-logs`, `GET /api/v1/nutrition/meal-templates` | Server-mediated load | Empty meal logs/templates; no separate SSR loading state; section error; bootstrap blocked | Production-backed APOLLO nutrition contracts; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/api-client.test.ts` | APOLLO nutrition owns truth; no diagnostic/diet app widening |
| `/app/tournaments` | Member route with legacy label; member competition/lobby surface, not public tournaments | Requires APOLLO session; section blocked when app bootstrap fails | `GET /api/v1/lobby/membership`, `POST /api/v1/lobby/membership/join`, `POST /api/v1/lobby/membership/leave`, `GET /api/v1/lobby/match-preview`, `GET /api/v1/competition/member-stats`, `GET /api/v1/competition/history`, `GET /api/v1/competition/game-identity?team_scope=all&limit=1` | Server-mediated load; browser proxy for lobby join/leave; member game identity fetched server-side with cookie | Empty match preview, stats, game identity, badge awards, history; join/leave pending/error; section error; bootstrap blocked | Production-backed APOLLO member competition/lobby/game identity contracts; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/public-competition.test.ts`, `tests/unit/api-client.test.ts` | APOLLO owns member stats/history/game identity and ARES preview; public tournaments, public rankings, OpenSkill values, safety details, and UI formulas blocked |
| `/app/settings` | Member | Requires APOLLO session through app layout | `GET /api/v1/profile`, `PATCH /api/v1/profile` | Profile load from app layout; browser proxy for profile patch | No empty state beyond profile defaults; save pending/success/error; bootstrap unavailable state | Production-backed APOLLO profile contract; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/member-ui.test.ts`, `tests/unit/api-client.test.ts` | APOLLO profile owns preferences; no role widening or frontend auth/session truth |
| `/app/[...path]` | Member normalization guard | Requires APOLLO session through app layout; unknown member path redirects to `/app/home` | `GET /api/v1/profile` via app layout | Server-mediated layout plus redirect | No empty/loading/error specific to guard; bootstrap errors remain app-layout-owned | Production-backed profile guard; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/routing.test.ts` | Route drift guard; no hidden sections |
| `/api/v1/[...path]` | Proxy | Browser-visible same-origin proxy; APOLLO still enforces session; Hestia allowlist only | Allowed: auth verification/verify/logout, profile, presence/claims/facilities/calendar, lobby eligibility/membership/join/leave/match-preview, competition member-stats/game-identity/history, planner, recommendations, nutrition, workouts. Some allowed client/proxy contracts are broader than current page usage, including planner writes/details, planner weeks, nutrition writes, workout detail, and effort/recovery feedback. Denied: all `/api/v1/public/*`, staff schedule, booking ops, competition sessions/commands/safety/tournaments, and unlisted methods | Browser proxy to APOLLO with path preserved; strips trusted-surface headers; mirrors APOLLO session cookie updates | 404 for denied/unsupported paths; APOLLO status/errors pass through for allowed paths; query semantics are APOLLO-owned, not proxy-owned | Production proxy over APOLLO; tests use mock APOLLO | `tests/unit/apollo-proxy.test.ts`, `tests/e2e/shell.spec.ts`, `tests/e2e/intake.spec.ts`, `tests/unit/api-client.test.ts` | APOLLO owns every proxied contract; Hestia must not become staff shell or public proxy passthrough |

## Themis Route Matrix

Themis is the privileged internal ops shell. It must not expose public pages or
own formulas.

| Route | Classification | Auth/session and role behavior | APOLLO endpoints and methods | Mediation | States | Production/stub/mock status | Tests | Owner and blocked adjacent scope |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `/` | Internal entry redirect | No direct APOLLO read on the route; redirects to `/ops` | None | SvelteKit redirect | No empty/loading/error state | Production route guard; no APOLLO contract | `tests/e2e/shell.spec.ts` unknown/root routing coverage | Route guard only; no public surface |
| `/login` | Public auth entry for internal shell | Authenticated sessions redirect to `/ops/facilities/{defaultFacilityKey}`; missing session renders APOLLO verification UI | `GET /api/v1/profile`, `POST /api/v1/auth/verification/start`, `POST /api/v1/auth/verify` | Profile check server-mediated; auth form calls same-origin proxy | Verification pending; backend unavailable; APOLLO auth errors; no role-specific denial until ops route | Production-backed APOLLO auth; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/api-client.test.ts` | APOLLO owns auth/session cookie; no frontend auth or public ops access |
| `/ops` | Internal staff default | Ops layout requires APOLLO session; unauthenticated redirects to `/login`; page redirects to default facility overview | `GET /api/v1/profile` | Server-mediated layout and redirect | Bootstrap unavailable state in layout; no empty state | Production-backed APOLLO profile; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/routing.test.ts` | APOLLO auth/profile owns entry; no public route |
| `/ops/facilities/:facilityKey` | Internal staff | Requires APOLLO session; supervisor/manager/owner may read; member/unsupported role gets unsupported role state | `GET /api/v1/profile`, `GET /api/v1/ops/facilities/{facilityKey}/overview?from=...&until=...&bucket_minutes=...` | Server-mediated APOLLO read | Empty upcoming occurrences and flow buckets; no separate SSR loading state; unsupported role; degraded 503; unavailable 502/bridge; malformed payload; generic error | Production-backed APOLLO ops overview; tests use mock APOLLO/ATHENA-shaped payloads | `tests/unit/ops-overview.test.ts`, `tests/unit/ops-view.test.ts`, `tests/e2e/shell.spec.ts` | APOLLO ops owns facility overview; no writes, no public analytics, no HERMES/gateway/deploy widening |
| `/ops/facilities/:facilityKey/schedule` | Internal staff | Requires APOLLO session; supervisor read-only; manager/owner create/cancel APOLLO-supported one-off blocks; member unsupported | `GET /api/v1/schedule/blocks?facility_key=...`, `POST /api/v1/schedule/blocks`, `POST /api/v1/schedule/blocks/{id}/cancel` | Server-mediated load/actions; trusted-surface headers injected only by server actions | Empty one-off block list; create/cancel pending; supervisor read-only state; unsupported role; malformed/unavailable/error; stale/conflict/trusted-surface action errors | Production-backed APOLLO schedule contracts; tests use mock APOLLO | `tests/unit/schedule-view.test.ts`, `tests/unit/api-client.test.ts`, `tests/e2e/shell.spec.ts` | APOLLO schedule owns block truth; no recurring schedule policy, broad hours editing, owner policy editor, booking-linked reservation cancellation through generic schedule |
| `/ops/competition` | Internal staff | Requires APOLLO session; member gets unsupported readiness; supervisor reads/dry-runs where APOLLO capabilities permit; manager/owner can run APOLLO-supported live commands; safety/reliability requires manager/owner capability and trusted surface | `GET /api/v1/competition/commands/readiness`, `GET /api/v1/competition/sessions`, `GET /api/v1/competition/sessions/{id}`, `POST /api/v1/competition/commands`, `GET /api/v1/competition/safety/readiness`, `GET /api/v1/competition/safety/review?limit=20` | Server-mediated load/actions; trusted-surface headers injected for live apply and safety reads, not browser proxy | Empty sessions/matches/results/safety facts; dry-run pending and non-mutating result; command accepted/denied/rejected; unsupported role; safety denied; malformed/unavailable/error | Production-backed APOLLO competition and safety contracts; tests use mock APOLLO | `tests/unit/api-client.test.ts`, `tests/e2e/shell.spec.ts` competition/safety tests | APOLLO owns command/session/result/safety/reliability truth; no local result/rating/preview/safety formulas, public tournaments, public safety UI, OpenSkill read path, or public social surface |
| `/ops/bookings` | Internal staff | Requires APOLLO session; supervisor read-only; manager/owner can enter create/detail flows; member unsupported | `GET /api/v1/booking/requests` | Server-mediated APOLLO read | Empty request queue; unsupported role; malformed/unavailable/error; no separate SSR loading state | Production-backed APOLLO booking contracts; tests use mock APOLLO | `tests/unit/bookings.test.ts`, `tests/unit/api-client.test.ts`, `tests/e2e/shell.spec.ts` | APOLLO booking owns request truth; no public intake page, payment/quote/invoice/deposit, or Hestia/member booking |
| `/ops/bookings/new` | Internal manager/owner staff action | Requires APOLLO session; uses APOLLO manage hint; supervisor sees request entry unavailable; manager/owner create with idempotency and trusted surface | `GET /api/v1/booking/requests`, `POST /api/v1/booking/requests` | Server-mediated manage-hint load and server action; trusted-surface headers injected by server action | Denied/read-only create state; create pending; missing idempotency key; APOLLO validation/conflict/trusted-surface/upstream errors | Production-backed APOLLO booking create; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/api-client.test.ts` | APOLLO booking owns availability/conflict/idempotency; no public/customer self-service or payment/quote |
| `/ops/bookings/:requestId` | Internal staff detail/action | Requires APOLLO session; supervisor read-only; manager/owner edit pending/open requests, create approved replacement requests, save public-safe message, and transition/cancel where APOLLO permits | `GET /api/v1/booking/requests/{id}`, `POST /api/v1/booking/requests/{id}/edit`, `POST /api/v1/booking/requests/{id}/rebook`, `POST /api/v1/booking/requests/{id}/public-message`, `POST /api/v1/booking/requests/{id}/review`, `/needs-changes`, `/reject`, `/cancel`, `/approve` | Server-mediated load/actions; trusted-surface headers injected by server actions | Request unavailable; unsupported role; malformed/unavailable/error; edit/rebook/decision pending; stale/conflict/invalid/trusted-surface/upstream errors; no fake fallback request | Production-backed APOLLO booking lifecycle contracts; tests use mock APOLLO | `tests/unit/bookings.test.ts`, `tests/unit/api-client.test.ts`, `tests/e2e/shell.spec.ts` | APOLLO booking owns lifecycle, public-safe message, linked reservation approval/cancellation; no in-place approved edit, public self-edit/rebook, proposal workflow, payment/quote |
| `/[...path]` | Internal normalization guard | Unknown routes redirect to `/ops`; no public catch-all content | None | SvelteKit redirect | No empty/loading/error state | Production route guard | `tests/e2e/shell.spec.ts`, `tests/unit/routing.test.ts` | Prevent route drift; no Themis public surface |
| `/api/v1/[...path]` | Proxy | Browser-visible same-origin proxy; APOLLO still enforces session, role, capability, and trusted-surface where required | Allowed: auth/profile, ops overview, schedule blocks/cancel, booking requests prefix, competition commands/readiness/sessions/session detail/safety readiness/safety review. The proxy is path-based rather than method-specific for allowed prefixes. Denied: `path === public` and every `/api/v1/public/*`; unlisted paths return 404 | Browser proxy to APOLLO with path preserved; strips trusted-surface headers; server actions/loads use private trusted-surface injection instead | 404 for public/unlisted paths; APOLLO status/errors pass through for allowed paths | Production proxy over APOLLO; tests use mock APOLLO | `tests/e2e/shell.spec.ts`, `tests/unit/api-client.test.ts` | APOLLO owns authority. Drift note: `booking/requests/*` and `auth/*` are broad internal prefixes and must be revisited if APOLLO adds new subroutes; no direct public APOLLO passthrough |

## Browser-Visible Public Proxy Denials

| Repo | Denied paths | Mechanism | Evidence |
| --- | --- | --- | --- |
| Hestia | All `/api/v1/public/*`, including public booking and public competition | Hestia proxy only allows exact member-safe route patterns; public paths are absent from allowlist and return 404 | `hestia/src/routes/api/v1/[...path]/+server.ts`; `hestia/tests/e2e/intake.spec.ts`; `hestia/tests/e2e/shell.spec.ts` |
| Themis | `path === public` and every `/api/v1/public/*` | Themis proxy explicitly rejects `public` and `public/` prefixes before APOLLO forwarding | `themis/src/routes/api/v1/[...path]/+server.ts`; `themis/tests/e2e/shell.spec.ts` |

Both proxies strip browser-supplied trusted-surface headers. Themis injects
trusted-surface proof only from server-side loads/actions using private
environment configuration; Hestia never injects trusted-surface proof.

## Contract Gaps And Drift Risks

| Finding | Severity | Ruling |
| --- | --- | --- |
| The matrix is docs-backed, not generated from route code or OpenAPI | Medium | Future frontend packets must update this file when adding routes or APOLLO calls |
| Themis proxy uses path prefixes rather than per-method/per-endpoint contracts for some allowed internal paths | Low | Documented as drift risk; APOLLO still owns session/capability/trusted-surface enforcement, browser trusted-surface headers are stripped, and `/api/v1/public/*` remains denied |
| Hestia proxy/client allow some member-safe APOLLO contracts not used by the current visible routes | Low | Documented as drift risk; current routes do not call staff/public/private endpoints, and APOLLO owns query/body semantics for allowed paths |
| Themis booking/schedule affordance helpers gate UI actions locally from APOLLO-returned status fields | Low | Documented as UI drift risk; APOLLO remains final enforcement for booking transitions, schedule cancellation, role, capability, and trusted-surface proof |
| Themis `/ops/bookings/new` collapses APOLLO list-load failures into `canManage: false` read-only state | Low | Documented as state-coverage debt; no fake booking mutation is claimed, and create still requires APOLLO manage/trusted-surface proof |
| Themis `OPS_ROLE_PATH` constant points at `/ops/unsupported-role`, but no route currently exists for that path | Low | Documented as stale route smell; current unsupported role states render inside concrete ops routes and catch-all normalizes unknown paths to `/ops` |
| Successful public intake/status HTTP responses with malformed payloads can surface as route errors instead of neutral unavailable copy | Low | Documented as state-coverage debt; parsers are allowlisted and APOLLO remains contract owner |
| Hestia `/app/tournaments` is a legacy member route label for lobby/member competition, not public tournament readiness | Low | Documented explicitly; public tournaments remain blocked |
| Loading states are mostly SSR route loads plus client form pending states, not app-wide skeleton loaders | Low | Current UI behavior documented; no frontend changes in this packet |
| Hestia/Themis tests are mock-APOLLO repo tests, not deployed proof | Medium | Deployed truth remains separate; Milestone 3.0 records Hestia/Themis as repo-only |

No frontend route currently requires a new APOLLO endpoint, schema, migration,
new public route, Hestia staff control, Themis public surface, frontend formula,
OpenSkill active read switch, or deploy/GitOps change.

## Closeout Truth

- Repo/docs truth: this file is the current route/API contract baseline for
  Hestia and Themis.
- Runtime truth: unchanged by this packet.
- Deployed truth: unchanged by this packet.
- Deferred truth: public tournaments, OpenSkill active reads, public/member
  safety detail UI, messaging/chat, broad public social graph, public profiles,
  public self-service booking expansion, booking/commercial/proposal workflows,
  gateway/deploy work, and generated contract enforcement remain deferred.
