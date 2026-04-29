# F29 - Public Leak Corpus

Status: pass for live readiness and repo/browser smoke; non-empty live leaderboard/identity rows unavailable.

Evidence:
- Live public readiness response contained no `user_id`, request IDs, match IDs, canonical result IDs, watermarks, sample sizes, OpenSkill values, ARES internals, safety facts, command internals, or trusted-surface fields.
- Hestia Playwright desktop/mobile suite passed for public competition/proxy boundaries.
- Themis focused Playwright subset passed for trusted-surface/proxy boundaries.

Ruling: leak corpus is accepted for the available surfaces; residual risk remains for absent live non-empty leaderboard/game-identity rows.
