# Architecture Overview

**How the XIV Dye Tools ecosystem interconnects**

This document provides a high-level view of how all projects in the XIV Dye Tools ecosystem work together to deliver dye color tools across web and Discord platforms.

---

## Ecosystem Diagram

```mermaid
graph TB
    subgraph "Shared Foundation"
        TYPES["@xivdyetools/types<br/>v1.0.0<br/>─────────────<br/>Type definitions,<br/>branded types"]
        LOGGER["@xivdyetools/logger<br/>v1.0.0<br/>─────────────<br/>Multi-environment<br/>logging"]
        TEST["@xivdyetools/test-utils<br/>v1.0.2<br/>─────────────<br/>Mocks, factories,<br/>helpers"]
    end

    subgraph "Core Library"
        CORE["@xivdyetools/core<br/>v1.4.0<br/>─────────────<br/>136 dyes, color algorithms,<br/>Universalis API, 6 languages,<br/>K-means++ palette extraction"]
    end

    subgraph "Consumer Applications"
        WEB["xivdyetools-web-app<br/>v3.1.0<br/>─────────────<br/>6 interactive tools,<br/>12 themes, PWA,<br/>Vite + Lit"]
        DISCORD["xivdyetools-discord-worker<br/>v2.1.0<br/>─────────────<br/>17 slash commands,<br/>SVG/PNG rendering,<br/>HTTP Interactions"]
    end

    subgraph "Backend Services"
        OAUTH["xivdyetools-oauth<br/>v2.1.0<br/>─────────────<br/>Discord OAuth, PKCE,<br/>JWT issuance,<br/>24h refresh grace"]
        PRESETS["xivdyetools-presets-api<br/>v1.2.0<br/>─────────────<br/>Community presets,<br/>D1 database,<br/>Moderation pipeline"]
    end

    subgraph "External Services"
        DISCORD_API["Discord API"]
        UNIVERSALIS["Universalis API<br/>(Market Prices)"]
        PERSPECTIVE["Perspective API<br/>(Content Moderation)"]
    end

    %% Shared package dependencies
    TYPES --> CORE
    TYPES --> WEB
    TYPES --> DISCORD
    TYPES --> OAUTH
    TYPES --> PRESETS
    LOGGER --> CORE
    LOGGER --> WEB
    LOGGER --> DISCORD
    LOGGER --> OAUTH
    LOGGER --> PRESETS

    %% Core library consumers
    CORE --> WEB
    CORE --> DISCORD
    CORE --> PRESETS

    %% Application relationships
    WEB --> OAUTH
    WEB --> PRESETS
    DISCORD -.->|"Service Binding"| PRESETS
    PRESETS --> OAUTH

    %% External API connections
    CORE -.-> UNIVERSALIS
    DISCORD --> DISCORD_API
    OAUTH --> DISCORD_API
    PRESETS -.-> PERSPECTIVE

    classDef shared fill:#e1f5fe,stroke:#01579b
    classDef core fill:#fff3e0,stroke:#e65100
    classDef app fill:#e8f5e9,stroke:#2e7d32
    classDef backend fill:#fce4ec,stroke:#880e4f
    classDef external fill:#f5f5f5,stroke:#616161

    class TYPES,LOGGER,TEST shared
    class CORE core
    class WEB,DISCORD app
    class OAUTH,PRESETS backend
    class DISCORD_API,UNIVERSALIS,PERSPECTIVE external
```

---

## Project Relationships

### Dependency Layers

```
Layer 4: External Services
├── Discord API (authentication, interactions)
├── Universalis API (FFXIV market prices)
└── Perspective API (ML content moderation)

Layer 3: Backend Services (Cloudflare Workers)
├── xivdyetools-oauth → JWT issuance
└── xivdyetools-presets-api → Community presets

Layer 2: Consumer Applications
├── xivdyetools-web-app → Browser-based tools
└── xivdyetools-discord-worker → Discord bot

Layer 1: Core Library
└── @xivdyetools/core → Color algorithms, dye database

Layer 0: Shared Foundation
├── @xivdyetools/types → Type definitions
├── @xivdyetools/logger → Logging
└── @xivdyetools/test-utils → Testing utilities
```

### Data Flow Summary

| Flow | Path | Purpose |
|------|------|---------|
| **Color Matching** | User → Web/Discord → Core → Response | Find closest dye to input color |
| **Market Prices** | Core → Universalis API → Core → Consumer | Real-time price data |
| **Authentication** | User → OAuth → Discord API → JWT → Consumer | User identity |
| **Preset Submission** | User → Client → Presets API → Moderation → Storage | Community content |
| **Preset Voting** | User → Client → Presets API → Database | Community curation |

---

## Project Summaries

### @xivdyetools/core (v1.4.0)

**Purpose**: Core TypeScript library providing color algorithms and the 136-dye database.

**Key Capabilities**:
- Color conversion (RGB, HSV, HSL, LAB)
- Nearest-neighbor dye matching via k-d tree
- Color harmony generation (complementary, triadic, analogous, etc.)
- Colorblindness simulation (Brettel algorithm)
- K-means++ palette extraction from images
- Universalis API integration for market prices
- 6-language localization (en, ja, de, fr, ko, zh)

**Consumed By**: Web app, Discord worker, Presets API

---

### xivdyetools-web-app (v3.1.0)

**Purpose**: Browser-based interactive toolkit for exploring FFXIV dye colors.

**6 Tools**:
1. **Dye Mixer** - Create gradients between two dyes
2. **Color Matcher** - Find closest dye to any color
3. **Color Harmony Explorer** - Discover harmonious combinations
4. **Dye Comparison** - Side-by-side dye analysis
5. **Accessibility Checker** - Colorblindness simulation
6. **Preset Browser** - Community preset discovery

**Technology**: Vite, Lit web components, Tailwind CSS, 12 themes

---

### xivdyetools-discord-worker (v2.1.0)

**Purpose**: Discord bot bringing dye tools to servers via slash commands.

**17 Commands** organized into categories:
- **Color Tools**: `/harmony`, `/match`, `/match_image`, `/mixer`
- **Dye Database**: `/dye search`, `/dye info`, `/dye list`, `/dye random`
- **Analysis**: `/comparison`, `/accessibility`
- **User Data**: `/favorites`, `/collection`
- **Community**: `/preset list`, `/preset show`, `/preset submit`, `/preset vote`
- **Utility**: `/language`, `/manual`, `/about`, `/stats`

**Technology**: Cloudflare Workers, HTTP Interactions, Hono, resvg-wasm, Photon WASM

---

### xivdyetools-oauth (v2.1.0)

**Purpose**: OAuth2 authentication provider for the ecosystem.

**Features**:
- Discord OAuth2 with PKCE flow
- JWT issuance with HS256 signing
- 24-hour refresh token grace period
- Account merging support
- XIVAuth integration (planned)

**Technology**: Cloudflare Workers, Hono, D1 database

---

### xivdyetools-presets-api (v1.2.0)

**Purpose**: REST API for community dye preset management.

**Features**:
- CRUD operations for presets
- Voting system with per-user tracking
- Multi-layer moderation pipeline:
  - Local profanity filtering (6 languages)
  - Perspective API ML moderation (optional)
  - Manual moderator review queue
- Rate limiting (10 submissions/user/day)
- Dual authentication (bot API key + JWT)

**Technology**: Cloudflare Workers, Hono, D1 SQLite database

---

### Shared Packages

| Package | Purpose |
|---------|---------|
| **@xivdyetools/types** | Branded types (HexColor, DyeId), interfaces for all domain objects |
| **@xivdyetools/logger** | Unified logging that works in browser, Node.js, and Workers |
| **@xivdyetools/test-utils** | Cloudflare bindings mocks, domain factories, test helpers |

---

## Communication Patterns

### Service Bindings (Worker-to-Worker)

Cloudflare Service Bindings enable zero-latency communication between workers:

```typescript
// Discord Worker calling Presets API
if (env.PRESETS_API) {
  // Service Binding (no HTTP overhead)
  return env.PRESETS_API.fetch(request);
}
// Fallback to HTTP
return fetch(`${env.PRESETS_API_URL}/presets`, options);
```

**Binding Map**:
```
xivdyetools-discord-worker
├── PRESETS_API → xivdyetools-presets-api (Service Binding)
└── KV_STORAGE → Rate limits, user preferences (KV Binding)

xivdyetools-presets-api
├── DB → D1 Database (presets, votes, moderation)
└── KV_CACHE → Response caching (KV Binding)
```

### REST API Communication

| Caller | Target | Authentication |
|--------|--------|----------------|
| Web App | OAuth Worker | N/A (initiates OAuth flow) |
| Web App | Presets API | JWT Bearer token |
| Discord Worker | Presets API | `BOT_API_SECRET` + user headers |
| Presets API | OAuth Worker | JWT verification (shared secret) |

---

## Deployment Architecture

```
                        ┌─────────────────────────────────────┐
                        │          Cloudflare Edge            │
                        │         (Global Distribution)       │
                        └─────────────────┬───────────────────┘
                                          │
        ┌─────────────────────────────────┼─────────────────────────────────┐
        │                                 │                                 │
        ▼                                 ▼                                 ▼
┌───────────────────┐         ┌───────────────────┐         ┌───────────────────┐
│  Cloudflare Pages │         │  Cloudflare       │         │  Cloudflare       │
│                   │         │  Workers          │         │  D1 Database      │
│  xivdyetools      │         │                   │         │                   │
│  web-app          │         │  • discord-worker │         │  • presets        │
│  (Static assets)  │         │  • oauth          │         │  • votes          │
│                   │         │  • presets-api    │         │  • users          │
└───────────────────┘         └───────────────────┘         └───────────────────┘
                                          │
                                          │ KV Storage
                                          ▼
                              ┌───────────────────┐
                              │  Cloudflare KV    │
                              │                   │
                              │  • Rate limits    │
                              │  • User prefs     │
                              │  • Cache          │
                              └───────────────────┘
```

---

## Related Documentation

- [Dependency Graph](dependency-graph.md) - Detailed npm and service dependencies
- [Service Bindings](service-bindings.md) - Worker-to-worker communication
- [Data Flow](data-flow.md) - Sequence diagrams for key flows
- [API Contracts](api-contracts.md) - Inter-service API specifications
