# F13 - Themis Trusted-Surface Roles

Status: pass.

Evidence:
- Themis `npm run check`, `npm test`, and `npm run build` passed.
- Focused Themis Playwright desktop/mobile subset passed for manager safety, supervisor denials, and competition trusted-surface flows.
- APOLLO `go test -count=1 ./internal/authz` passed as part of targeted test command.

Ruling: tested staff routes require server-side trusted-surface and role capability.
