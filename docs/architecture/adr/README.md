# ADR Process

Architecture Decision Records (ADRs) capture why important technical decisions were made, including alternatives and trade-offs.

## Purpose

- Preserve architectural intent across team and time.
- Make decision history discoverable and reviewable.
- Prevent repeated debates on already-settled decisions.

## Naming and numbering

- File naming format: `NNNN-short-kebab-title.md`
- Example: `0001-app-router-monolith.md`
- Keep numbers incremental and immutable.

## Lifecycle states

- `Proposed`: drafted, awaiting review.
- `Accepted`: approved and active.
- `Superseded`: replaced by newer ADR.
- `Deprecated`: no longer relevant.

## Required sections

Each ADR must include:

1. Title
2. Status
3. Date
4. Context
5. Decision
6. Alternatives considered
7. Consequences (positive and negative)
8. Rollout and validation plan
9. Follow-up actions

## Review workflow

1. Author creates ADR from template `0000-template.md`.
2. ADR linked in PR description.
3. At least one reviewer validates:
   - Problem statement is clear.
   - Alternatives are realistic.
   - Consequences and risks are explicit.
4. Merge with status `Accepted` or `Proposed` (if staged).

## When an ADR is mandatory

- Changes to system boundaries or deployment shape.
- New external dependencies in runtime path.
- Security model changes.
- Data model or persistence strategy changes.
- Cross-cutting behavior changes (theming, analytics, SEO foundations).

## Repository touchpoints

ADRs are especially expected when changing:

- `app/layout.tsx`
- `app/api/*`
- `next.config.js`
- `data/config/*`
- `components/shared/Analytics.tsx`

## Initial ADR backlog

- `0001`: App Router monolith boundary and rationale.
- `0002`: Config-first content and metadata strategy.
- `0003`: Analytics integration baseline and extensibility path.
- `0004`: Public API route hardening and abuse controls.
