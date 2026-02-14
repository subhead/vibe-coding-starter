# Architecture Diagrams

## C4 Level 1 - System Context

```mermaid
flowchart TB
  Visitor[Site Visitor / Product User]
  Team[Engineering Team]
  App[Mevolut Next.js Application]
  Analytics[Analytics Providers]
  Newsletter[Newsletter Provider]

  Visitor -->|Browse pages, use UI| App
  Team -->|Change code/config| App
  App -->|Event telemetry| Analytics
  App -->|Newsletter API integration| Newsletter
```

## C4 Level 2 - Container View

```mermaid
flowchart LR
  Browser[Browser Client]
  NextApp[Next.js App Runtime]
  Config[Config Modules\ndata/config/*]
  API1[/api/newsletter]
  API2[/api/og]
  Static[Static Assets\npublic/*]
  ExtAnalytics[Analytics Services]
  ExtNewsletter[Newsletter Service]

  Browser --> NextApp
  NextApp --> Config
  NextApp --> API1
  NextApp --> API2
  API2 --> Static
  NextApp --> ExtAnalytics
  API1 --> ExtNewsletter
```

## C4 Level 3 - Component View (Application Runtime)

```mermaid
flowchart TB
  Layout[Root Layout\napp/layout.tsx]
  Theme[Theme Providers\napp/theme-providers.tsx]
  Search[Search Provider\ncomponents/shared/SearchProvider.tsx]
  Analytics[Analytics Wrapper\ncomponents/shared/Analytics.tsx]
  Header[Header\ncomponents/shared/Header.tsx]
  Footer[Footer\ncomponents/shared/Footer.tsx]
  Landing[Landing Components\ncomponents/landing/*]
  UI[UI Primitives\ncomponents/shared/ui/*]
  Home[Home Page\napp/page.tsx]
  Dashboard[Dashboard Page\napp/dashboard/page.tsx]

  Layout --> Theme
  Layout --> Search
  Layout --> Analytics
  Home --> Header
  Home --> Landing
  Home --> Footer
  Landing --> UI
  Dashboard --> UI
```

## Data Flow - Config to Runtime

```mermaid
flowchart LR
  Metadata[data/config/metadata.js]
  Settings[data/config/site.settings.js]
  Colors[data/config/colors.js]
  SEO[app/seo.tsx]
  Layout[app/layout.tsx]
  Tailwind[tailwind.config.js]
  OG[app/api/og/route.tsx]

  Metadata --> Layout
  Metadata --> SEO
  Metadata --> OG
  Settings --> Layout
  Colors --> Tailwind
  Colors --> Layout
```

## Public API Surface

```mermaid
sequenceDiagram
  participant Client as Browser/Consumer
  participant Newsletter as /api/newsletter
  participant OG as /api/og
  participant Provider as External Provider

  Client->>Newsletter: GET/POST newsletter request
  Newsletter->>Provider: Delegate via Pliny provider
  Provider-->>Newsletter: Provider response
  Newsletter-->>Client: API response

  Client->>OG: GET social image
  OG-->>Client: Generated image response
```

## Security Boundary View

```mermaid
flowchart TB
  Internet[Internet]
  Headers[Security Headers + CSP\nnext.config.js]
  App[Next.js Routes + API]
  ThirdParty[Third-party services]

  Internet --> Headers --> App --> ThirdParty
```
