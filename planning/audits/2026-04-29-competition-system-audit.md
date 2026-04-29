# Competition System Audit

Date: 2026-04-29

Scope: shipped ASHTON competition repo/runtime truth through `3B-LC`, covering
APOLLO, Themis, Hestia, platform docs, and Prometheus status.

This audit is intentionally specific. It defines what the competition system
owns, what it reads, what it projects, what UI surfaces may consume, and what
must stay outside future competition packets unless a later packet explicitly
opens that scope.

## Audit Conclusion

The competition system is APOLLO-owned source truth plus APOLLO-owned derived
projection truth. Themis is an internal ops consumer. Hestia is a public/member
safe projection consumer. Platform records coordination truth. Prometheus is not
part of this repo/runtime spine unless a deployment packet explicitly changes
and verifies deployed truth.

The active shipped chain is:

1. Competition containers, queue, teams, rosters, matches.
2. Staff command/readiness and CLI parity.
3. Canonical lifecycle/result trust.
4. Versioned legacy active ratings.
5. Internal OpenSkill comparison only.
6. Internal ARES v2 queue intent and match-preview proposal only.
7. Internal derived analytics.
8. Staff/internal tournament runtime.
9. Manager/internal safety and reliability.
10. Public-safe readiness and leaderboards.
11. Public/member-safe game identity projections.

No UI owns source truth for ratings, analytics, matchmaking, tournaments,
safety, public projections, or game identity. No shipped path switches
OpenSkill onto active reads. No shipped public path exposes manager/internal
safety, commands, ARES proposals, OpenSkill comparison facts, tournament ops,
source result IDs, projection watermarks, raw user IDs, or analytics internals.

## Repo Ownership

| Repo | Competition role | Touch level |
| --- | --- | --- |
| `apollo` | Source truth and derived projections | Owns all competition persistence, state transitions, policy math, authz, public/member projection contracts, and CLI/API runtime |
| `themis` | Privileged internal ops consumer | Reads APOLLO internal ops contracts and sends APOLLO command DTOs from server actions; does not own competition truth |
| `hestia` | Customer/member-safe consumer | Reads APOLLO public-safe and member-safe projections only; does not compute competition truth |
| `ashton-platform` | Coordination docs | Records closeout, deferments, smoke matrix, and repo boundary truth |
| `Prometheus` | Deployment status only | Untouched by repo/runtime 3B-LC closeout; no deployed truth changes are inferred from source commits |

## APOLLO Source Modules

| Area | Primary files | Truth owned |
| --- | --- | --- |
| Authz/capability | `internal/authz/authz.go`, `internal/server/server.go` | Role-to-capability matrix and trusted-surface enforcement |
| Command/readiness | `internal/competition/command.go`, `internal/competition/service.go`, `internal/server/server.go`, `cmd/apollo/main.go` | Command definitions, dry-run/apply outcomes, CLI/API parity |
| Containers/execution | `internal/competition/service.go`, `internal/competition/repository.go`, `db/queries/competition.sql` | Sessions, queue, teams, rosters, matches, side slots |
| Result trust/history | `internal/competition/history.go`, `internal/competition/history_repository.go`, `db/queries/competition_history.sql` | Canonical result identity, lifecycle states, correction supersession, member stats/history |
| Legacy ratings | `internal/rating/legacy.go`, `internal/competition/history.go`, `db/queries/competition_history.sql` | Active rating math and projections over finalized/corrected canonical results |
| OpenSkill comparison | `internal/rating/openskill.go`, `db/queries/competition_history.sql` | Internal comparison events/rows and delta flags only |
| ARES v2 proposal | `internal/ares/competition.go`, `internal/competition/service.go`, `db/queries/competition.sql` | Queue intent facts and internal match-preview proposal facts |
| Analytics | `internal/competition/analytics.go`, `db/queries/competition_history.sql` | Internal stat events and analytics projections |
| Tournaments | `internal/competition/tournament.go`, `internal/competition/tournament_repository.go`, `db/queries/tournament.sql` | Staff/internal tournament, bracket, seed, snapshot, binding, advancement, event facts |
| Safety/reliability | `internal/competition/safety.go`, `internal/competition/safety_repository.go`, `db/queries/competition_safety.sql` | Manager/internal reports, blocks, reliability events, audit events |
| Public projections | `internal/competition/public.go`, `internal/competition/public_repository.go`, `internal/server/server.go` | Public-safe readiness and leaderboard contracts |
| Game identity | `internal/competition/game_identity.go`, `internal/competition/game_identity_repository.go`, `internal/server/server.go` | CP, badge, rivalry, and squad projections derived from public-safe rows |

## APOLLO HTTP Surface

### Public Unauthenticated

| Route | Method | Owner | Output boundary |
| --- | --- | --- | --- |
| `/api/v1/public/competition/readiness` | `GET` | APOLLO | Contract/projection version, status, finalized/corrected canonical result source, legacy active rating source, available counts, deferred feature keys |
| `/api/v1/public/competition/leaderboards` | `GET` | APOLLO | Redacted participant leaderboard rows over allowlisted stat/team-scope filters |
| `/api/v1/public/competition/game-identity` | `GET` | APOLLO | Redacted CP, badge, rivalry, and squad facts with explicit policy versions |

### Member Authenticated

| Route | Method | Owner | Output boundary |
| --- | --- | --- | --- |
| `/api/v1/competition/member-stats` | `GET` | APOLLO | Self-scoped member stats |
| `/api/v1/competition/history` | `GET` | APOLLO | Self-scoped competition history |
| `/api/v1/competition/game-identity` | `GET` | APOLLO | Self-scoped member game identity projection |

### Staff/Internal Reads

| Route | Method | Capability | Trusted surface | Boundary |
| --- | --- | --- | --- | --- |
| `/api/v1/competition/commands/readiness` | `GET` | Derived from role | No | APOLLO command readiness/capability truth |
| `/api/v1/competition/sessions` | `GET` | `competition_read` | No | Staff session summaries |
| `/api/v1/competition/sessions/{sessionID}` | `GET` | `competition_read` | No | Staff session detail, queue, teams, matches, lifecycle/result state |
| `/api/v1/competition/tournaments` | `GET` | `competition_read` | No | Internal tournament list |
| `/api/v1/competition/tournaments/{tournamentID}` | `GET` | `competition_read` | No | Internal tournament detail |
| `/api/v1/competition/safety/readiness` | `GET` | `competition_safety_review` | Yes | Manager/internal safety readiness and summary |
| `/api/v1/competition/safety/review` | `GET` | `competition_safety_review` | Yes | Manager/internal report, block, reliability, and audit rows |

### Staff/Internal Writes

| Route | Method | Capability | Version guard | Boundary |
| --- | --- | --- | --- | --- |
| `/api/v1/competition/commands` | `POST` | Per command definition | Command-dependent | Shared command DTO path; live apply requires trusted surface |
| `/api/v1/competition/sessions` | `POST` | `competition_structure_manage` | No | Create staff/internal competition session |
| `/api/v1/competition/sessions/{sessionID}/queue/open` | `POST` | `competition_live_manage` | No | Open queue |
| `/api/v1/competition/sessions/{sessionID}/queue/members` | `POST` | `competition_live_manage` | `queue_version` path via command or service | Add queue member |
| `/api/v1/competition/sessions/{sessionID}/queue/members/{userID}/remove` | `POST` | `competition_live_manage` | `queue_version` path via command or service | Remove queue member |
| `/api/v1/competition/sessions/{sessionID}/assignment` | `POST` | `competition_live_manage` | Queue state | Assign queue into teams/matches |
| `/api/v1/competition/sessions/{sessionID}/start` | `POST` | `competition_live_manage` | State machine | Start session |
| `/api/v1/competition/sessions/{sessionID}/archive` | `POST` | `competition_live_manage` | State machine | Archive eligible session |
| `/api/v1/competition/sessions/{sessionID}/teams` | `POST` | `competition_structure_manage` | State machine | Create draft team |
| `/api/v1/competition/sessions/{sessionID}/teams/{teamID}/remove` | `POST` | `competition_structure_manage` | Team reference guard | Remove unreferenced draft team |
| `/api/v1/competition/sessions/{sessionID}/teams/{teamID}/members` | `POST` | `competition_structure_manage` | Roster uniqueness | Add roster member |
| `/api/v1/competition/sessions/{sessionID}/teams/{teamID}/members/{userID}/remove` | `POST` | `competition_structure_manage` | Roster guard | Remove roster member |
| `/api/v1/competition/sessions/{sessionID}/matches` | `POST` | `competition_structure_manage` | State machine | Create draft match |
| `/api/v1/competition/sessions/{sessionID}/matches/{matchID}/archive` | `POST` | `competition_structure_manage` | Match guard | Archive eligible draft match |
| `/api/v1/competition/sessions/{sessionID}/matches/{matchID}/result` | `POST` | `competition_live_manage` | `expected_result_version` | Record non-final canonical result |
| `/api/v1/competition/sessions/{sessionID}/matches/{matchID}/result/finalize` | `POST` | `competition_live_manage` | `expected_result_version` | Finalize result for rating consumption |
| `/api/v1/competition/sessions/{sessionID}/matches/{matchID}/result/dispute` | `POST` | `competition_live_manage` | `expected_result_version` | Mark canonical result disputed |
| `/api/v1/competition/sessions/{sessionID}/matches/{matchID}/result/correct` | `POST` | `competition_live_manage` | `expected_result_version` | Create corrected canonical result |
| `/api/v1/competition/sessions/{sessionID}/matches/{matchID}/result/void` | `POST` | `competition_live_manage` | `expected_result_version` | Void canonical result |
| `/api/v1/competition/tournaments` | `POST` | `competition_structure_manage` | No | Create staff/internal tournament |
| `/api/v1/competition/tournaments/{tournamentID}/seed` | `POST` | `competition_structure_manage` | `expected_tournament_version` | Seed bracket from APOLLO team truth |
| `/api/v1/competition/tournaments/{tournamentID}/teams/lock` | `POST` | `competition_structure_manage` | `expected_tournament_version` | Lock immutable team snapshot |
| `/api/v1/competition/tournaments/{tournamentID}/matches/bind` | `POST` | `competition_structure_manage` | `expected_tournament_version` | Bind APOLLO match to tournament slot |
| `/api/v1/competition/tournaments/{tournamentID}/rounds/advance` | `POST` | `competition_live_manage` | `expected_tournament_version` | Advance from finalized/corrected canonical result |
| `/api/v1/competition/safety/reports` | `POST` | `competition_safety_review` | Safety command actual version | Record manager/internal safety report |
| `/api/v1/competition/safety/blocks` | `POST` | `competition_safety_review` | Safety command actual version | Record manager/internal safety block |
| `/api/v1/competition/reliability/events` | `POST` | `competition_safety_review` | Safety command actual version | Record manager/internal reliability event |

## CLI Surface

The active competition CLI path is service-backed and shares APOLLO command
truth. The audit-relevant command is:

| Command | Runtime owner | Boundary |
| --- | --- | --- |
| `apollo competition command run` | APOLLO | Shared command/outcome/readiness contract; dry-run is non-mutating; live apply keeps APOLLO capability and trusted-surface enforcement |

APOLLO also exposes tournament CLI read helpers in the shipped docs:
`apollo competition tournament list` and `apollo competition tournament show`.
Those are internal read helpers only and do not create public tournament truth.

## Capability Matrix

| Role | Competition capabilities | Meaning |
| --- | --- | --- |
| member | None for staff ops | May use self-scoped member stats/history/game identity routes only |
| supervisor | `competition_read`, `competition_live_manage` | Can read internal competition ops and perform live-management commands where APOLLO permits; no structure-management or safety-review capability |
| manager | `competition_read`, `competition_live_manage`, `competition_structure_manage`, `competition_safety_review` | Full internal competition ops, tournament structure commands, and manager/internal safety review |
| owner | `competition_read`, `competition_live_manage`, `competition_structure_manage`, `competition_safety_review` | Same competition capability set as manager |

Trusted-surface proof is required for staff-sensitive mutations and safety
reads/writes that are manager/internal. Themis supplies trusted-surface headers
only from private server environment variables. Hestia strips
`x-apollo-trusted-surface` and `x-apollo-trusted-surface-token` from browser
proxy requests.

## Database Inventory

| Fact family | Tables |
| --- | --- |
| Competition containers | `apollo.competition_sessions`, `apollo.competition_session_queue_members`, `apollo.competition_session_teams`, `apollo.competition_team_roster_members`, `apollo.competition_matches`, `apollo.competition_match_side_slots` |
| Staff attribution | `apollo.competition_staff_action_attributions` |
| Result trust | `apollo.competition_match_results`, `apollo.competition_match_result_sides`, `apollo.competition_lifecycle_events` |
| Legacy rating and active projection | `apollo.competition_rating_events`, `apollo.competition_member_ratings` |
| OpenSkill comparison | `apollo.competition_rating_comparisons` plus comparison event rows in `apollo.competition_rating_events` |
| ARES v2 proposal | `apollo.competition_queue_intents`, `apollo.competition_queue_intent_events`, `apollo.competition_match_previews`, `apollo.competition_match_preview_members`, `apollo.competition_match_preview_events` |
| Analytics | `apollo.competition_analytics_events`, `apollo.competition_analytics_projections` |
| Tournament runtime | `apollo.competition_tournaments`, `apollo.competition_tournament_brackets`, `apollo.competition_tournament_seeds`, `apollo.competition_tournament_team_snapshots`, `apollo.competition_tournament_team_snapshot_members`, `apollo.competition_tournament_match_bindings`, `apollo.competition_tournament_advancements`, `apollo.competition_tournament_events` |
| Safety/reliability | `apollo.competition_safety_reports`, `apollo.competition_safety_blocks`, `apollo.competition_reliability_events`, `apollo.competition_safety_events` |
| Public readiness/leaderboards | No separate public table; APOLLO projects from finalized/corrected canonical result, legacy rating, and analytics projection truth |
| Game identity | No separate game-identity table in the current migration inventory; APOLLO computes from public-safe competition projection rows and explicit policy constants |

## Projection Chain

The competition fact chain is directional:

1. Staff/session inputs create APOLLO sessions, queue rows, teams, rosters, and
   matches.
2. Result commands create canonical result and lifecycle facts.
3. Finalized/corrected canonical results feed legacy rating events and active
   rating projections.
4. The same trusted result/rating substrate feeds internal OpenSkill comparison
   rows, but OpenSkill does not become the active read path.
5. Queue intent and rating facts feed ARES v2 preview proposal facts. ARES does
   not own match lifecycle, result, rating, tournament, analytics, public, or
   game identity truth.
6. Finalized/corrected canonical results plus legacy active rating facts feed
   internal analytics stat events and projections.
7. Public readiness and leaderboards read only public-safe projection facts.
8. Game identity reads only public-safe competition projection rows and emits
   CP, badge, rivalry, and squad facts.

Safety/reliability and tournament facts are sidecar/internal consumers of the
trusted competition spine:

- Tournament advancement consumes finalized/corrected canonical result truth but
  does not mutate result, rating, analytics, ARES, public, or game identity
  truth.
- Safety/reliability facts are manager/internal and do not mutate canonical
  result, rating, analytics, ARES, tournament, public, member, or game identity
  truth.

## Public Output Contract

Public competition readiness exposes:

- `contract_version = apollo_public_competition_v1`
- `projection_version = competition_analytics_v1`
- `result_source = finalized_or_corrected_canonical_results`
- `rating_source = legacy_elo_like_active_projection`
- available leaderboard and canonical result counts
- explicit deferred feature keys

Public leaderboards expose:

- rank
- redacted `participant_N` label
- sport, mode, facility, team scope
- allowlisted stat type and value
- last result timestamp, if present
- computed timestamp

Public game identity exposes:

- `contract_version = apollo_game_identity_v1`
- `projection_version = competition_game_identity_v1`
- `cp_policy_version = apollo_cp_v1`
- `badge_policy_version = apollo_badge_awards_v1`
- `rivalry_policy_version = apollo_rivalry_state_v1`
- `squad_policy_version = apollo_squad_identity_v1`
- CP rows from `matches_played`, `wins`, `draws`, and `losses`
- badge awards for first match, first win, and regular competitor thresholds
- rivalry states grouped within the same sport/mode/facility/team-scope context
- squad identities grouped within the same sport/mode/facility/team-scope context

Public output must not expose:

- raw `user_id`
- internal request or result IDs
- `competition_match_id`
- `canonical_result_id`
- `source_result_id`
- `projection_watermark`
- `sample_size`
- `confidence`
- OpenSkill `mu`, `sigma`, comparison deltas, or flags
- ARES match quality, predicted win probability, queue intent IDs, or
  explanation internals
- safety report IDs, safety block IDs, reliability facts, or manager review
  details
- command readiness internals or trusted-surface fields
- tournament ops truth
- internal notes or staff notes

## Themis Touchpoints

Themis touches the competition system only through APOLLO internal contracts.

| Themis file or route | Competition touch |
| --- | --- |
| `src/routes/ops/competition/+page.server.ts` | Loads APOLLO readiness, session detail, command outcomes, and safety review/readiness; sends APOLLO command DTOs from server actions |
| `src/routes/ops/competition/+page.svelte` | Renders APOLLO-provided internal competition state, result lifecycle state, command outcomes, and manager/internal safety summary |
| `src/lib/api/competition.ts` | Parses APOLLO competition payloads; validates shape but does not compute source truth |
| `src/lib/api/client.ts` | Calls APOLLO competition readiness, sessions, commands, and safety review endpoints |
| `src/lib/server/client.ts` | Injects trusted-surface headers only server-side when configured |
| `src/lib/server/apollo.ts` | Strips trusted-surface headers from browser-origin proxy requests |
| `src/routes/api/v1/[...path]/+server.ts` | Allows internal APOLLO competition ops routes and blocks `/api/v1/public/*` |

Themis must not touch:

- APOLLO public competition routes as a public surface
- Hestia public/member competition pages
- rating formulas
- OpenSkill active read path
- ARES proposal logic
- analytics formulas
- tournament bracket computation outside APOLLO
- safety policy logic outside APOLLO
- CP/badge/rivalry/squad formulas
- browser-delivered trusted-surface credentials

## Hestia Touchpoints

Hestia touches the competition system only through APOLLO public/member-safe
contracts.

| Hestia file or route | Competition touch |
| --- | --- |
| `src/routes/competition/+page.server.ts` | Loads public competition state server-side |
| `src/routes/competition/+page.svelte` | Renders public readiness, redacted leaderboard rows, CP, badges, rivalry, and squad facts |
| `src/lib/server/public-competition.ts` | Calls APOLLO public competition readiness, public leaderboards, public game identity, and member game identity; parses through allowlists |
| `src/routes/app/tournaments/+page.server.ts` | Loads member-safe lobby membership, match preview, member stats/history, and member game identity |
| `src/routes/app/tournaments/+page.svelte` | Renders self-scoped member competition state and game identity |
| `src/routes/api/v1/[...path]/+server.ts` | Allows only `competition/member-stats`, `competition/game-identity`, and `competition/history` through the authenticated member proxy |
| `src/lib/server/apollo.ts` | Strips trusted-surface request headers from member proxy requests |

Hestia must not touch:

- staff/internal sessions route
- commands route
- safety/reliability routes
- tournament ops routes
- public APOLLO routes through the browser-visible proxy
- local rating or CP formulas
- local analytics formulas
- OpenSkill comparison facts
- ARES proposal facts
- raw APOLLO IDs or projection watermarks

## Platform And Prometheus Touchpoints

Platform touches competition only as coordination truth:

- `README.md` records current repo/runtime state and next-line boundary.
- `planning/IMPLEMENTATION-BOARD.md` records active blockers and fresh-packet
  rules.
- `planning/FLOWS.md` records expected flow behavior and the smoke matrix.
- `planning/DEFERMENTS.md` records closed and deferred competition items.
- `planning/repo-briefs/apollo.md` records APOLLO ownership and current slice.

Prometheus is not a competition runtime participant for this closeout. It may
carry future deployment manifests or observability changes only if a deployment
or observability packet explicitly opens that scope.

## Adjacent Systems The Competition System References

| Adjacent system | How competition touches it | Boundary |
| --- | --- | --- |
| Auth/session | APOLLO authenticates member/staff routes and maps principals to roles | Competition does not mint sessions |
| Authz/trusted surface | APOLLO checks competition capabilities and trusted-surface proof | Browser clients never own trusted-surface proof |
| Sports/facility capability | Competition rows carry `sport_key`, `facility_key`, `zone_key`, mode, and side-count facts | Competition does not own facility hours or physical occupancy truth |
| Member/profile/users | Competition stores participant user IDs internally and self-scopes member reads | Public projections redact identities |
| Lobby/match preview | Existing lobby membership and ARES preview are adjacent; ARES v2 preview is internal proposal truth | Match lifecycle/result ownership stays in competition service |
| Schedule/booking | Themis shares an ops shell with booking/schedule, and competition uses facility keys | Competition does not mutate bookings, public intake, or generic schedule blocks |
| ATHENA | Ops overview composes ATHENA occupancy elsewhere | Competition truth does not consume raw tap hashes or occupancy analytics |

## Hard Non-Touches

Future competition packets must not implicitly create or modify:

- messaging/chat
- broad public social graph
- public profiles/scouting
- public/member safety UI
- public safety report/block/reliability details
- public tournaments
- OpenSkill active read path
- booking/commercial/proposal workflows
- public self-booking, public self-edit/rebook, payments, quotes, invoices,
  deposits, or checkout
- recurring schedule policy or broad operating-hours editing
- project-wide SemVer governance
- deployment truth or Prometheus manifests
- Hestia staff controls
- Themis public surface
- UI-owned formulas for ratings, analytics, matchmaking, tournaments, safety, or
  game identity

## Test And Smoke Anchors

APOLLO anchors:

- `internal/competition/public_test.go`
- `internal/server/competition_history_integration_test.go`
- `internal/server/competition_command_integration_test.go`
- `internal/server/competition_safety_integration_test.go`
- `internal/server/competition_tournament_integration_test.go`
- `internal/rating/legacy_test.go`
- `internal/rating/openskill_test.go`
- `internal/ares/competition_test.go`

Hestia anchors:

- `tests/unit/public-competition.test.ts`
- `tests/unit/apollo-proxy.test.ts`
- `tests/e2e/shell.spec.ts` public competition, unavailable state, member proxy,
  and private route denial cases

Themis anchors:

- `tests/unit/api-client.test.ts`
- `tests/e2e/shell.spec.ts` competition readiness, command, result lifecycle,
  safety review, safety denial, and public proxy denial cases

Standard 3B-LC smoke commands:

```bash
# APOLLO
git status -sb
git diff --check
sqlc generate -f db/sqlc.yaml
go test ./internal/competition ./internal/rating ./internal/ares -run 'Test(Analytics|Competition|Public|Member|StoredDelta|Tournament|Legacy|OpenSkill|BuildCompetition|GetLobby|BuildPreview)'
go test ./internal/server -run 'TestCompetition(Authz|Command|Execution|History|Result|Analytics|Safety|Tournament|Queue|Runtime|Public)'
go vet ./...
go build ./cmd/apollo
go test ./...

# Hestia
git status -sb
git diff --check
npm run check
npm test
npm run build
npx playwright test tests/e2e/shell.spec.ts -g 'same-origin APOLLO proxy blocks private competition safety paths|public competition page consumes public-safe APOLLO contracts only|public competition page renders unavailable state without fake records'

# Themis scoped shell smoke
git status -sb
npx playwright test tests/e2e/shell.spec.ts -g 'member sessions see disabled competition readiness only|supervisor reads competition commands without structure apply controls|manager reads APOLLO safety reliability review without fake public state|manager safety reliability denial preserves APOLLO reason without pending facts|manager runs competition dry-run and accepted commands through APOLLO|manager operates competition result lifecycle through APOLLO|Themis blocks APOLLO public booking API proxy paths'
```

## Change Review Checklist

Before modifying any competition code or surface:

1. Identify which layer is being changed: command, result, rating, OpenSkill,
   ARES, analytics, tournament, safety, public projection, or game identity.
2. Confirm APOLLO still owns the source fact or projection.
3. Confirm any UI change consumes APOLLO DTOs and does not compute source truth.
4. Confirm public/member output is allowlisted and redacted.
5. Confirm manager/internal facts stay behind APOLLO authz and trusted-surface
   boundaries.
6. Confirm OpenSkill remains comparison-only unless a future packet explicitly
   opens the read-path switch.
7. Confirm ARES remains proposal-only and does not own lifecycle/result/rating.
8. Confirm tournament advancement consumes result truth and does not mutate it.
9. Confirm safety/reliability facts do not mutate result, rating, analytics,
   ARES, tournament, public, member, or game identity truth.
10. Confirm platform docs and deferments do not claim deployed truth changed
    without deployment verification.

## Current Residual Risks

1. Deployment-level smoke remains a separate future proof because 3B-LC changed
   repo/runtime docs, not deployed truth.
2. Game identity policies are intentionally first-version policies and may need
   usage-driven hardening once real competition data accumulates.
3. Themis full e2e has unrelated dated fixture/test-selector debt outside the
   competition trust-spine smoke path; do not treat that as competition truth
   drift without a targeted packet.
4. Any public tournament, public safety, OpenSkill read-path, messaging/chat, or
   broad social graph work must start as a new scoped packet, not as a
   continuation of this closeout.
