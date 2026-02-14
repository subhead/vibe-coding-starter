# Architecture Documentation

This document captures the current architecture of `vibe-coding-starter` and provides a maintainable framework for future evolution.

## 1) Architecture Analysis and Discovery

### Current architectural style

- **Primary style**: Next.js App Router monolith (frontend + lightweight backend routes in one deployable app).
- **Composition style**: Config-driven page composition using shared landing and UI primitives.
- **Runtime split**: Server components by default, with explicit client islands (`'use client'`) for interactive UI.

### Key design decisions (as implemented)

- Root orchestration is centralized in `app/layout.tsx`.
- The app uses file-based routing under `app/` with route handlers under `app/api/*`.
- Site behavior and branding are config-first via `data/config/*`.
- Theme and analytics are cross-cutting concerns wired globally in layout.

### System boundaries and dependencies

- **Internal boundary**: This repository owns UI rendering, route handling, metadata generation, and static content.
- **External dependencies**:
  - `@shipixen/pliny` for analytics, search provider, newsletter route handler integration.
  - `@vercel/analytics` for client-side analytics fallback.
  - `@vercel/og` for social image generation.
  - `next-themes` for theme state and hydration.
- **No first-party persistence layer** (no ORM/database integration currently present).

### Architectural debt and opportunities

- `app/page.tsx` uses `// @ts-nocheck`.
- `app/api/newsletter/route.ts` and parts of `components/shared/ui/sidebar.tsx` use `@ts-ignore`.
- Configuration appears duplicated for pricing (`app/pricing/pricingData.tsx` and `data/config/pricingData.tsx`).
- No test suite files are present (`*.test.*` / `*.spec.*` not found).
- No deployment manifest (for example `vercel.json`, `Dockerfile`) is present; deployment assumptions should be documented explicitly.

## 2) Documentation Framework

This architecture documentation uses a hybrid approach:

- **C4 model (lightweight)** for Context/Container/Component views.
- **arc42-style narrative sections** for quality attributes, constraints, risks, and operations.
- **ADR process** under `docs/architecture/adr/`.
- **Mermaid diagrams** in `docs/architecture/diagrams.md` for diagram-as-code maintenance.

## 3) System Context

### Stakeholders and personas

- **Site visitor / prospect**: consumes public marketing pages.
- **Product user**: interacts with dashboard and account-like flows.
- **Content/marketing editor**: updates copy/config in `data/config/*` and app route pages.
- **Engineering team**: evolves components, routes, and platform configuration.

### External systems and integrations

- **Browser clients** (desktop/mobile).
- **Analytics providers** via Pliny/Vercel wrappers.
- **Newsletter provider** abstracted through Pliny (`siteConfig.newsletter.provider` when enabled).
- **Search document source** (`search.json`) consumed by KBar provider.

### System responsibilities

- Serve public pages and interactive product UI.
- Produce SEO metadata, sitemap, robots directives, and Open Graph images.
- Expose lightweight API routes for newsletter and OG image generation.
- Apply baseline security headers and CSP.

## 4) Container and Service Architecture

### Runtime containers

1. **Next.js App Runtime**
   - Routes and layout: `app/layout.tsx`, `app/page.tsx`, `app/*/page.tsx`.
   - API handlers: `app/api/newsletter/route.ts`, `app/api/og/route.tsx`.
2. **Client UI Runtime (Browser)**
   - Interactive pages/components (`'use client'`), for example:
     - `app/dashboard/page.tsx`
     - `app/pricing/page.tsx`
     - `app/theme-providers.tsx`
3. **Configuration/Data layer (in-repo static modules)**
   - `data/config/site.settings.js`
   - `data/config/metadata.js`
   - `data/config/colors.js`
   - other config and content modules in `data/config/*`.

### Communication patterns

- **UI -> API route**: newsletter form flows can post to `/api/newsletter` (provider-dependent behavior).
- **Metadata -> OG route**: metadata references `socialBanner: '/api/og'`, and the OG route renders dynamic image response.
- **Layout -> cross-cutting providers**: root layout wraps all pages with theme, search, and analytics providers.

### Deployment view (current)

- Single Next.js deployment unit.
- Static assets from `public/`.
- Build pipeline from `package.json` scripts and Next.js build.
- Security headers configured in `next.config.js`.

## 5) Component and Module Documentation

### App shell and routing

- `app/layout.tsx`: global shell, metadata, providers, fonts, and root CSS variable wiring.
- `app/page.tsx`: primary landing composition.
- `app/*/page.tsx`: static content pages and feature pages.
- `app/not-found.tsx`: not-found boundary.

### API modules

- `app/api/newsletter/route.ts`: delegates request handling to Pliny newsletter API helper.
- `app/api/og/route.tsx`: constructs Open Graph image response from static logo + metadata.

### Shared UI and design system

- `components/shared/*`: app-level shared widgets (`Header`, `Footer`, `ThemeSwitch`, `SearchProvider`, analytics wrapper).
- `components/shared/ui/*`: reusable UI primitives.
- `components/landing/*`: composable landing blocks; `components/landing/index.ts` is the aggregation entry.

### Utilities and scripts

- `lib/utils.ts`: shared utility functions.
- `scripts/generateAppInfo.mjs`: build-time metadata generation (`data/app-info.ts`).

## 6) Data Architecture

### Data model strategy (current)

- **Configuration-first data model** using in-repo TS/JS modules.
- No local database schema or persistence layer currently defined.
- Static assets and static text pages constitute core content store.

### Data flow

- `data/config/metadata.js` + `data/config/site.settings.js` -> `app/layout.tsx` and `app/seo.tsx` for SEO metadata and page defaults.
- `data/config/colors.js` -> `tailwind.config.js` + CSS variables in `app/layout.tsx`.
- `data/config/pricingData.tsx` (and mirrored pricing data file in app scope) -> pricing page rendering.

### Governance and lifecycle

- Source of truth should be consolidated per domain (for example one pricing data source).
- Configuration changes should be tracked by ADR when they impact architecture/runtime behavior.

## 7) Security and Compliance Architecture

### Security controls implemented

- CSP and security headers in `next.config.js`:
  - `Content-Security-Policy`
  - `Referrer-Policy`
  - `X-Frame-Options`
  - `X-Content-Type-Options`
  - `X-DNS-Prefetch-Control`
  - `Strict-Transport-Security`
  - `Permissions-Policy`

### Trust boundaries

- Public browser boundary to all page routes.
- Public API boundary for `/api/newsletter` and `/api/og`.
- Third-party boundary for analytics/newsletter providers.

### Compliance-relevant assets

- Legal/policy pages: `app/privacy/page.tsx`, `app/terms/page.tsx`, `app/cookies/page.tsx`.
- Security messaging page: `app/security/page.tsx`.

### Gaps and recommendations

- Document explicit authentication/authorization strategy if product scope expands beyond marketing + demo.
- Add explicit rate limiting and abuse controls for public API routes when operationalized.

## 8) Quality Attributes and Cross-Cutting Concerns

### Performance and scalability

- Leverages Next.js rendering model and static assets.
- `dynamic = 'force-static'` used in sitemap/robots/newsletter route for static behavior where applicable.
- Bundle analyzer is available via `ANALYZE=true` (`next.config.js`, package scripts).

### Reliability and availability

- Single deployable unit simplifies operations.
- Reliability risk concentrated in third-party provider configuration (analytics/newsletter).

### Observability

- `components/shared/Analytics.tsx` initializes analytics globally.
- No structured logging layer or error telemetry abstraction currently documented in-repo.

### Maintainability

- Strong modular component organization.
- Risk areas: type suppressions, duplicated pricing data sources, mixed JS/TS patterns in config.

### Business continuity

- No explicit disaster recovery runbook is currently documented in repository.
- Recommend adding an operations appendix with rollback, config validation, and provider outage procedures.

## 9) Architecture Decision Records (ADR)

ADR docs are stored in `docs/architecture/adr/`.

- Process and conventions: `docs/architecture/adr/README.md`
- Template: `docs/architecture/adr/0000-template.md`

Suggested initial ADR backlog:

- ADR-0001: Why App Router monolith is retained for this project stage.
- ADR-0002: Why config-first content model is preferred over CMS/database now.
- ADR-0003: Observability baseline (Pliny + Vercel analytics) and upgrade path.
- ADR-0004: Public API hardening strategy for `/api/newsletter` and `/api/og`.

## 10) Documentation Automation and Maintenance

### Automation recommendations

- Keep Mermaid diagrams in version control (`docs/architecture/diagrams.md`).
- Add CI checks for markdown and link integrity.
- Add a lightweight architecture review checklist in PR template for changes touching:
  - `app/layout.tsx`
  - `app/api/*`
  - `data/config/*`
  - `next.config.js`

### Review cadence

- Review architecture docs monthly, or immediately after any significant route/config/platform change.
- Require ADR creation for decisions that alter boundaries, dependencies, or quality attributes.

### Ownership

- Primary owner: engineering team.
- Secondary reviewers: product and design for context/flow changes.

## Source Map (quick references)

- Platform/config: `package.json`, `next.config.js`, `tsconfig.json`, `tailwind.config.js`
- App shell/routing: `app/layout.tsx`, `app/page.tsx`, `app/*/page.tsx`
- API routes: `app/api/newsletter/route.ts`, `app/api/og/route.tsx`
- Shared modules: `components/shared/*`, `components/shared/ui/*`, `components/landing/*`
- Data/config: `data/config/*`, `data/app-info.ts`
- Utilities/scripts: `lib/utils.ts`, `scripts/generateAppInfo.mjs`
