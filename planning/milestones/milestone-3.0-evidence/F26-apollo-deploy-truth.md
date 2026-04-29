# F26 - APOLLO Deploy Truth

Status: pass.

Evidence:
- Live image before roll-forward was `ghcr.io/ixxet/apollo:sha-bf3119b@sha256:ed3f3681b65a889ee563e8e0917fa3caba17cbceddb26a89393882ee287a3748`.
- Controller-approved roll-forward deployed `ghcr.io/ixxet/apollo:sha-6b27618@sha256:6c2d4ef8c67d0ea21d3b41963eb9709d0114b53bfea56c30d456cafbb677515a`.
- `kubectl rollout status deployment/apollo -n agents` passed.
- Live health, public readiness, and `/metrics` passed after roll-forward.

Ruling: APOLLO 3B deploy drift is resolved for Milestone 3.0.
