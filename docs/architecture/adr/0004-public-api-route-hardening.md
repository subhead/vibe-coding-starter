# ADR 0004: Public API Route Hardening for Newsletter and OG Endpoints

- **Status**: Proposed
- **Date**: 2026-02-14
- **Deciders**: Engineering
- **Technical area**: security

## Context

The app exposes public route handlers:

- `app/api/newsletter/route.ts`
- `app/api/og/route.tsx`

These are reachable without authentication and currently rely mostly on framework/provider defaults.

## Decision

Introduce a lightweight hardening baseline for all public API route handlers.

Baseline controls:

1. Input validation and explicit failure responses.
2. Basic abuse mitigation (rate limiting and bot friction where applicable).
3. Structured error handling and sanitized error surfaces.
4. Minimal operational telemetry (request volume, error counts, latency bands).

## Alternatives considered

### Alternative A: Keep current implementation as-is

- Pros
  - No immediate implementation work.
  - Lowest short-term complexity.
- Cons
  - Higher abuse and reliability risk.
  - Weak operational visibility.
- Why not selected
  - Public endpoints should meet a minimum reliability/security baseline.

### Alternative B: Add full auth wall to all API endpoints

- Pros
  - Strong request control.
  - Reduced anonymous abuse.
- Cons
  - Breaks intended public usage patterns (for example OG rendering).
  - Adds product and UX friction.
- Why not selected
  - Not aligned with current endpoint responsibilities.

## Consequences

### Positive

- Better resilience against misuse.
- More predictable failure behavior.
- Better incident diagnosis.

### Negative

- Added implementation and maintenance overhead.
- Possible false positives from abuse mitigation if poorly tuned.

## Rollout plan

- Add explicit request validation and response contracts in both route handlers.
- Add rate limiting strategy appropriate for deployment platform.
- Add consistent error envelope and logging hooks.
- Document operational runbook for endpoint incidents.

## Validation plan

- Load test expected and abusive traffic patterns.
- Verify error responses are stable and non-sensitive.
- Observe endpoint metrics in staging before production rollout.

## Follow-up actions

- [ ] Implement hardening baseline for `/api/newsletter`.
- [ ] Implement hardening baseline for `/api/og`.
- [ ] Add endpoint operational playbook.

## Related artifacts

- PRs:
- Issues:
- Docs: `docs/architecture/README.md`, `docs/architecture/diagrams.md`
- Supersedes/Superseded by:
