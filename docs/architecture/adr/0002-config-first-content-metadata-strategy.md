# ADR 0002: Config-First Content and Metadata Strategy

- **Status**: Accepted
- **Date**: 2026-02-14
- **Deciders**: Engineering
- **Technical area**: data

## Context

The application currently stores core content and behavior settings in repository modules under `data/config/*`.

Examples include:

- `metadata.js` for SEO and branding values
- `site.settings.js` for feature flags and provider options
- `colors.js` for design token source values

The project does not yet use a CMS or first-party database.

## Decision

Adopt a config-first strategy for content and metadata at the current stage, with `data/config/*` as the primary source of truth.

## Alternatives considered

### Alternative A: Introduce headless CMS now

- Pros
  - Non-engineering editing workflows.
  - Strong content governance tooling.
- Cons
  - Integration overhead and new operational surface.
  - Increased complexity for current scope.
- Why not selected
  - Current content footprint is manageable in repo.

### Alternative B: Move most values to environment variables

- Pros
  - Runtime mutability without code changes.
  - Good for secret/provider config.
- Cons
  - Poor fit for rich structured content and text.
  - Harder local discoverability and review.
- Why not selected
  - Most current values are versioned content, not secrets.

## Consequences

### Positive

- High transparency in pull requests.
- Fast local iteration.
- Versioned change history for content and settings.

### Negative

- Content updates require code changes.
- Risk of duplicated config sources if not disciplined.
- Potential drift between typed interfaces and JS config modules.

## Rollout plan

- Keep config in `data/config/*`.
- Consolidate duplicated domain configs where found.
- Document source-of-truth ownership per config file.

## Validation plan

- Track config change frequency and incident causes.
- Validate no duplicate source files per domain.
- Add periodic architecture review for CMS migration trigger.

## Follow-up actions

- [ ] Consolidate pricing data to one source-of-truth module.
- [ ] Define migration criteria for CMS adoption.

## Related artifacts

- PRs:
- Issues:
- Docs: `docs/architecture/README.md`
- Supersedes/Superseded by:
