# Hardening Artifacts

Use standalone hardening artifacts only where the audited evidence is strong
enough to support exact rerun commands, expected destructive failures versus
real regressions, verified versus unverified truth, carry-forward gaps, and a
clear verdict.

Policy:

- Tracers 0-3 predate the standalone hardening-artifact discipline
- do not fabricate full hardening artifacts for Tracers 0-3
- keep Tracers 0-3 represented in repo docs and the tracer matrix
- backfill standalone artifacts from Tracer 4 onward and for Milestones 1,
  1.5, and 1.6 where the audited evidence exists
- fractional milestones are bounded deployment or hardening workstreams inside
  an already-open milestone sequence; keep the historical 1.5 / 1.6 labels as
  aliases rather than renaming shipped history

Platform-level artifacts:

- [Milestone 1](milestone-1.md)
- [Milestone 1.5](milestone-1-5.md)
- [Milestone 1.6](milestone-1-6.md)

Repo-local artifacts:

- [APOLLO hardening artifacts](https://github.com/ixxet/apollo/blob/main/docs/hardening/README.md)
- [APOLLO Tracer 12 hardening](https://github.com/ixxet/apollo/blob/main/docs/hardening/tracer-12.md)
- [HERMES Tracer 8 hardening](https://github.com/ixxet/hermes/blob/main/docs/hardening/tracer-8.md)
