# F09 - Member Self Scope

Status: repo/local pass.

Evidence:
- APOLLO authz and competition targeted tests passed.
- Hestia private/member route and proxy boundary tests passed.
- No live Hestia deployment exists in the inspected cluster, so this is repo-local truth.

Ruling: member competition reads remain self-scoped in tested contracts.
