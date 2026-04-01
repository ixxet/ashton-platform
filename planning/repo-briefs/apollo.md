# apollo

Member-facing multi-mode platform for Ashtonbee Athletics. Members manage privacy and availability, track visits and workouts, receive AI-powered training recommendations, and optionally opt into the ARES skill-rating and matchmaking system.

## Architecture

```
  ┌──────────┐     ┌────────────────────────────────────────────────────┐
  │ PWA      │     │                       APOLLO                        │
  │(SvelteKit│────▶│                                                    │
  │  served  │     │  Go API ──▶ Profile / Presence State ──▶ Postgres │
  │  by Go)  │     │    │                                               │
  └──────────┘     │    ├──▶ Visit History / Workout History           │
                   │    │                                               │
  ┌──────────┐     │    ├──▶ Recommendation Engine                     │
  │ CLI /    │────▶│    │      LangGraph + vLLM + Mem0                │
  │ Agent    │     │    │                                               │
  └──────────┘     │    └──▶ ARES Matchmaking                         │
                   │           OpenSkill (Weng-Lin Bayesian)           │
                   │           Lobby and team formation                │
                   │                                                    │
                   │  NATS: consumes athena presence, publishes        │
                   │        workout.logged and match.played            │
                   └────────────────────────────────────────────────────┘
```

## Tech Stack

| Component | Technology | Why |
|-----------|-----------|-----|
| API | Go + chi | Consistent with ATHENA and HERMES. Shared patterns. |
| Frontend | SvelteKit (pre-built, served by Go) | PWA-capable. Offline support. Consistent with capstone. |
| CLI | cobra + viper | Same CLI patterns across all ASHTON services. |
| Database | Postgres 16 (apollo schema) | Relational data for users, visits, workouts, exercises, ratings, and matches; flexible user state starts in JSONB preferences. |
| SQL | sqlc | Type-safe, auditable queries. |
| Recommendations | LangGraph + vLLM | Agent pipeline with training data as tool context. |
| Memory | Mem0 | User goals, injury history, preferences across sessions. |
| Matchmaking | OpenSkill (Weng-Lin) | License-free Bayesian skill rating. Team and 1v1 support. |
| Offline | Service worker + IndexedDB (Serwist) | Log workouts offline, sync when connected. |
| Events | NATS | Consumes ATHENA presence events and publishes workout and match events. |
| Metrics | prometheus/client_golang | Workout volume, recommendation latency, matchmaking quality. |

## Project Structure

```
apollo/
├── cmd/
│   └── apollo/
│       └── main.go
├── internal/
│   ├── profile/               # User profile, visibility, availability, coaching state
│   │   ├── service.go
│   │   └── repository.go
│   ├── presence/              # Presence resolution and visit history
│   │   ├── service.go
│   │   └── repository.go
│   ├── workout/               # Workout logging domain
│   │   ├── service.go
│   │   ├── repository.go
│   │   └── analytics.go       # Volume, intensity, frequency calculations
│   ├── recommend/             # Recommendation pipeline
│   │   ├── pipeline.go        # Orchestrates: history → features → LLM → validate
│   │   ├── features.go        # Training feature extraction from history
│   │   └── schema.go          # Recommendation output schema for guided generation
│   ├── ares/                  # ARES matchmaking subsystem
│   │   ├── rating.go          # OpenSkill rating engine (Go implementation)
│   │   ├── matchmaker.go      # Lobby management + match formation
│   │   ├── teams.go           # Team balancing algorithm
│   │   ├── service.go         # ARES domain service
│   │   └── repository.go
│   ├── server/
│   │   ├── server.go
│   │   ├── handlers.go
│   │   ├── handlers_ares.go   # Matchmaking-specific endpoints
│   │   ├── handlers_profile.go
│   │   ├── handlers_presence.go
│   │   └── middleware.go
│   └── config/
│       └── config.go
├── web/                       # SvelteKit frontend
│   ├── src/
│   │   ├── routes/
│   │   │   ├── +page.svelte              # Dashboard
│   │   │   ├── profile/+page.svelte      # User profile + goals
│   │   │   ├── presence/+page.svelte     # On-site state and facility context
│   │   │   ├── log/+page.svelte          # Workout logging
│   │   │   ├── history/+page.svelte      # Visit + workout history
│   │   │   ├── recommend/+page.svelte    # AI recommendations
│   │   │   └── match/+page.svelte        # ARES matchmaking lobby
│   │   └── lib/
│   │       ├── api.ts                 # API client
│   │       ├── offline.ts             # IndexedDB + sync logic
│   │       └── stores.ts              # Svelte stores
│   ├── static/
│   │   └── manifest.json             # PWA manifest
│   ├── svelte.config.js
│   └── package.json
├── pkg/
│   └── client/
│       └── client.go
├── db/
│   ├── migrations/
│   └── queries/
├── deploy/
├── docs/
│   ├── growing-pains.md
│   ├── adr/
│   │   ├── 001-openskill-over-trueskill.md
│   │   └── 002-recommendation-pipeline.md
│   ├── ares-algorithm.md      # OpenSkill explanation + team formation logic
│   └── grafana/
├── tools.json                 # MCP tool manifest (workout + recommendation + ARES)
├── Dockerfile
├── Makefile
├── .github/workflows/ci.yml
├── go.mod
└── go.sum
```

## Multi-Mode Member Model

APOLLO owns member intent. It decides how on-site presence should affect the user experience.

The minimum independent states are:

- `presence_state`: `offsite` or `onsite`
- `visibility_mode`: `ghost` or `discoverable`
- `availability_mode`: `unavailable`, `available_now`, or `with_team`
- `coaching_profile`: optional body metrics, goals, and nutrition preferences

This means:

- a user can be on-site and remain invisible
- a user can be on-site and use coaching features only
- a user can be discoverable but unavailable for recruiting
- a user can appear as already with a team without entering open matchmaking

Visit history and workout history are separate concerns. A gym visit is not automatically a workout log.

## Member Authentication

APOLLO uses a zero-external-dependency auth path for the first implementation wave.

1. the member registers with student ID and email
2. APOLLO sends an email verification link or code
3. after verification, APOLLO issues a signed `HTTPOnly`, `Secure`, `SameSite=Strict` session cookie

This keeps the PWA self-hosted and avoids institutional OAuth dependency. OAuth or SSO can be added later if a real provider becomes available.

Auth and identity linkage are related but separate:

- auth proves who owns the APOLLO account
- claimed tags or other presence tokens link ATHENA presence to that account

## State Storage Strategy

Start with flexible state storage in `users.preferences`:

- `visibility_mode`
- `availability_mode`
- `coaching_profile`
- other emerging member-intent fields

Keep stable high-value domains relational:

- `visits`
- `workouts`
- `exercises`
- `ares_*`

If member-state fields stabilize and need tighter indexing or constraints, split them into dedicated columns or tables later.

## ARES Matchmaking System

ARES uses **OpenSkill (Weng-Lin Bayesian ranking)** — a license-free alternative to Microsoft's TrueSkill. Every player has two values per activity:

- **μ (mu):** estimated skill level (starts at 25.0)
- **σ (sigma):** uncertainty in that estimate (starts at 8.33, decreases with more games)

### Supported Modes

| Mode | Description | Example |
|------|-------------|---------|
| 1v1 | Two players, direct matchup | Badminton singles, table tennis |
| Team | Balanced teams, any size | 5v5 basketball, 3v3 volleyball |
| FFA | Free-for-all, individual ranking | Race, fitness challenge |

### Team Formation Algorithm

1. Filter eligible players by activity, explicit availability, and constraints
2. Sort by μ (skill estimate)
3. Serpentine draft: assign to teams alternating high→low→low→high
4. Validate: total team μ within balance threshold (configurable, default ±5%)
5. If unbalanced, swap the player with the smallest |Δμ| improvement
6. Output: balanced teams with expected win probability

### Rating Update

After a match, OpenSkill adjusts both μ and σ for all participants:
- Winners: μ increases, σ decreases
- Losers: μ decreases, σ decreases
- Upset victories: larger μ shift (beating higher-rated opponents rewards more)
- Sigma always decreases with more games (system becomes more confident)

## CLI Reference

```bash
# Workout management
apollo workout log    [--exercise] [--sets] [--reps] [--weight] [--rpe]
apollo workout list   [--user] [--since]
apollo history        [--user] [--exercise] [--range]

# Profile and presence
apollo profile show   [--user]
apollo profile update [--user]
apollo presence show  [--user]
apollo visit list     [--user] [--since]

# Recommendations
apollo recommend      [--user] [--format]

# ARES matchmaking
apollo lobby join     [--activity]
apollo lobby leave    [--activity]
apollo match find     [--activity] [--mode] [--players]
apollo match result   [--match-id] [--outcome]
apollo match history  [--user] [--activity]
apollo rating show    [--user] [--activity]
apollo rating leaderboard [--activity] [--top]

# User management
apollo user create    [--name] [--email]
apollo version
```

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/v1/health` | Health check |
| GET/PATCH | `/api/v1/profile/:user` | Profile, privacy, and availability settings |
| GET | `/api/v1/presence/:user` | Current presence state |
| GET | `/api/v1/visits/:user` | Visit history |
| POST | `/api/v1/workouts` | Log a workout |
| GET | `/api/v1/workouts` | List workouts |
| GET | `/api/v1/history/:user` | Training history + analytics |
| POST | `/api/v1/recommend` | Generate training recommendation |
| POST | `/api/v1/ares/lobby` | Join matchmaking lobby |
| POST | `/api/v1/ares/match` | Record match result |
| GET | `/api/v1/ares/rating/:user` | Get player ratings |
| GET | `/api/v1/ares/leaderboard/:activity` | Activity leaderboard |
| GET | `/metrics` | Prometheus metrics |

## Prometheus Metrics

| Metric | Type | Description |
|--------|------|-------------|
| `apollo_workouts_logged_total` | counter | Workouts logged |
| `apollo_visits_recorded_total` | counter | Visit records created |
| `apollo_exercises_logged_total{exercise}` | counter | Per-exercise tracking |
| `apollo_recommendations_generated_total` | counter | Recs generated |
| `apollo_recommendation_feedback{rating}` | counter | User feedback |
| `apollo_vllm_inference_duration_seconds` | histogram | LLM latency |
| `apollo_ares_matches_played_total{activity}` | counter | Matches completed |
| `apollo_ares_lobby_size{activity}` | gauge | Players waiting in the matchmaking lobby |
| `apollo_ares_team_balance_score` | histogram | Team formation quality |
| `apollo_presence_state_total{state}` | gauge | Users by current presence state |
| `apollo_matchmaking_availability_total{mode}` | gauge | Users by matchmaking availability mode |
| `apollo_active_users` | gauge | Users with activity in last 7 days |

## Status

| Milestone | Status |
|-----------|--------|
| Project scaffold | Not started |
| Database schema (profiles + visits + workouts + ARES) | Not started |
| Profile and presence state model | Not started |
| Workout logging service | Not started |
| Training history analytics | Not started |
| Recommendation pipeline (LangGraph) | Not started |
| OpenSkill rating engine | Not started |
| Matchmaking lobby + team formation | Not started |
| SvelteKit PWA frontend | Not started |
| Offline support (service worker) | Not started |
| CLI | Not started |
| Prometheus metrics | Not started |
| NATS event publishing | Not started |
| Grafana dashboard | Not started |
| MCP tool manifest | Not started |
| CI pipeline | Not started |
| Cluster deployment via Flux | Not started |

## Part of the ASHTON Platform

```
                   ashton-proto
                        │
    ┌───────────────────┼───────────────────┐
    ▼                   ▼                   ▼
  athena ◄──────── hermes ────────▶ [apollo]
    │                   │                   │
    └───────────────────┼───────────────────┘
                        ▼
                ashton-mcp-gateway
```

APOLLO is the member-facing application. ATHENA provides facility presence and context. APOLLO decides whether that presence should remain private, support coaching only, or enter matchmaking explicitly.

## License

MIT
