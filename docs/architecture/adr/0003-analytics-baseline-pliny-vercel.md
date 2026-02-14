# ADR 0003: Analytics Baseline with Pliny and Vercel Fallback

- **Status**: Accepted
- **Date**: 2026-02-14
- **Deciders**: Engineering
- **Technical area**: platform

## Context

Analytics is initialized globally through `components/shared/Analytics.tsx` and injected from `app/layout.tsx`.

Current behavior:

- If `siteConfig.analytics` is configured, use Pliny analytics integrations.
- If no provider config exists and analytics are not disabled, use `@vercel/analytics/react`.
- If `disableAnalytics` is true, collect no analytics events.

## Decision

Keep the current layered analytics strategy:

1. Provider-specific analytics through Pliny when configured.
2. Vercel Analytics fallback when no explicit provider is configured.
3. Global disable switch via site config.

## Alternatives considered

### Alternative A: Single mandatory analytics provider

- Pros
  - Simple behavior and fewer branches.
  - Consistent event destination.
- Cons
  - Lower flexibility for deployment contexts.
  - Harder migration between providers.
- Why not selected
  - Current starter should remain provider-flexible.

### Alternative B: No analytics until explicitly configured

- Pros
  - Zero telemetry by default.
  - Minimal data-privacy footprint.
- Cons
  - No baseline observability in common deployments.
  - Slower feedback on user behavior.
- Why not selected
  - Lightweight fallback provides practical default visibility.

## Consequences

### Positive

- Works across multiple deployment preferences.
- Keeps analytics concern centralized in one wrapper.
- Allows explicit opt-out.

### Negative

- Behavior depends on config branch clarity.
- Event schema consistency across providers is not guaranteed.
- Limited route-level telemetry unless expanded later.

## Rollout plan

- Preserve wrapper pattern in `components/shared/Analytics.tsx`.
- Keep global injection in `app/layout.tsx`.
- Document provider config expectations in architecture docs.

## Validation plan

- Verify one analytics path is active per deployment.
- Confirm disable flag fully suppresses analytics components.
- Add periodic checks for event integrity when changing providers.

## Follow-up actions

- [ ] Define minimal event taxonomy shared across providers.
- [ ] Add route-level error/performance telemetry plan.

## Related artifacts

- PRs:
- Issues:
- Docs: `docs/architecture/README.md`
- Supersedes/Superseded by:
