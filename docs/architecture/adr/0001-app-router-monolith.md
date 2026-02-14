# ADR 0001: App Router Monolith Boundary

- **Status**: Accepted
- **Date**: 2026-02-14
- **Deciders**: Engineering
- **Technical area**: platform

## Context

The project currently serves marketing pages, product-style UI, SEO metadata, and lightweight API routes from one Next.js codebase.

Current constraints:

- Team and scope favor fast iteration.
- There is no first-party database or heavy backend domain yet.
- The app already uses App Router and route handlers under `app/api/*`.

## Decision

Keep a single Next.js App Router monolith as the primary system boundary for the current stage.

This includes:

- UI routes in `app/*`
- Route handlers in `app/api/*`
- Shared config in `data/config/*`

## Alternatives considered

### Alternative A: Split frontend and backend services now

- Pros
  - Stronger isolation and independent scaling.
  - Clearer ownership boundaries.
- Cons
  - Higher operational complexity.
  - Slower delivery for current scope.
- Why not selected
  - Premature for current product and team stage.

### Alternative B: Static-only frontend with external APIs only

- Pros
  - Simpler hosting model.
  - Low backend maintenance.
- Cons
  - Less control over OG/newsletter handling.
  - More dependency on external glue and tooling.
- Why not selected
  - Existing route handlers provide practical value with low complexity.

## Consequences

### Positive

- Faster development loops.
- Unified deployment and configuration model.
- Lower ops overhead.

### Negative

- Backend and frontend concerns can blur over time.
- Public route handlers require deliberate hardening.
- Future service extraction may require migration effort.

## Rollout plan

- Continue using current App Router structure.
- Keep system boundaries documented in `docs/architecture/README.md`.
- Reassess when persistence-heavy or high-throughput backend needs appear.

## Validation plan

- Measure release velocity and incident volume quarterly.
- Track if route handlers and runtime concerns become too coupled.
- Trigger architecture review if new domains require independent scaling.

## Follow-up actions

- [ ] Re-evaluate boundary decision when database-backed features are introduced.
- [ ] Define extraction criteria for future backend services.

## Related artifacts

- PRs:
- Issues:
- Docs: `docs/architecture/README.md`, `docs/architecture/diagrams.md`
- Supersedes/Superseded by:
