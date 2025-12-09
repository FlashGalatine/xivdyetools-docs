# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Monorepo Overview

This is a monorepo containing six active projects for Final Fantasy XIV dye color tools:

| Project | Type | Version | Purpose |
|---------|------|---------|---------|
| **xivdyetools-core** | npm library | 1.3.7 | Core color algorithms, 136-dye database, Universalis API, 6-language i18n |
| **xivdyetools-web-app** | Vite + Lit web app | 2.6.0 | Interactive web-based color tools (6 tools) |
| **xivdyetools-discord-worker** | Cloudflare Worker | 2.0.1 | Serverless Discord bot using HTTP Interactions |
| **xivdyetools-oauth** | Cloudflare Worker | 1.1.0 | Discord OAuth + JWT issuance for web auth |
| **xivdyetools-presets-api** | Cloudflare Worker + D1 | 1.1.0 | Community presets API with moderation |
| **xivdyetools-docs** | Documentation | — | Feature specs, roadmaps, deployment guides |

### Deprecated Projects

| Project | Status | Notes |
|---------|--------|-------|
| **xivdyetools-discord-bot** | DEPRECATED | Moved to `_deprecated/`. Replaced by xivdyetools-discord-worker. |

### Dependency Graph

```
xivdyetools-core (npm library)
    ├── xivdyetools-web-app
    ├── xivdyetools-discord-worker
    └── xivdyetools-presets-api

xivdyetools-oauth ←──JWT──→ xivdyetools-presets-api
                            ↕ Service Binding
                     xivdyetools-discord-worker
```

Changes to core require publishing to npm before consumers can use them.

---

## Quick Commands

### xivdyetools-core (Library)
```bash
cd xivdyetools-core
npm run build              # Compile TypeScript + build locales
npm test                   # Run vitest
npm run test:coverage      # Test with coverage (85% threshold)
npm run test:integration   # Integration tests only
npm run lint               # ESLint check
npm run type-check         # TypeScript check only
npm run docs               # Generate TypeDoc
```

### xivdyetools-web-app (Web App)
```bash
cd xivdyetools-web-app
npm run dev                # Start dev server (localhost:5173)
npm run build              # Production build
npm run test               # Run vitest
npm run preview            # Preview production build
npm run build:css          # Rebuild Tailwind CSS
```

### xivdyetools-discord-worker (Discord Bot)
```bash
cd xivdyetools-discord-worker
npm run dev                # Wrangler local dev server
npm run deploy             # Deploy to Cloudflare (staging)
npm run deploy:production  # Deploy to production
npm run test               # Run vitest
npm run register-commands  # Register slash commands
npm run upload-emojis      # Upload emoji mappings
```

### xivdyetools-oauth (Auth Worker)
```bash
cd xivdyetools-oauth
npm run dev                # Local dev server (port 8788)
npm run deploy             # Deploy to Cloudflare
npm run type-check         # TypeScript validation
```

### xivdyetools-presets-api (Presets Worker)
```bash
cd xivdyetools-presets-api
npm run dev                # Local dev server (port 8787)
npm run deploy             # Deploy to staging
npm run deploy:production  # Deploy to production
npm run db:migrate:local   # Apply schema to local D1
npm run db:migrate         # Apply schema to production D1
```

---

## Architecture

### xivdyetools-core Service Layer

The core library provides services used by all consumers:

| Service | Purpose | Key Methods |
|---------|---------|-------------|
| **ColorService** | Color math, conversion, accessibility | `hexToRgb`, `rgbToHsv`, `simulateColorblindness` |
| **DyeService** | 136-dye database, matching, harmony | `findClosestDye`, `findTriadicDyes`, `searchByCategory` |
| **APIService** | Universalis API with pluggable cache | `getPriceData`, `getPricesForItems` |
| **LocalizationService** | 6-language translation support | `translate`, `setLocale` |
| **PaletteService** | K-means++ palette extraction | `extractPalette`, `analyzeDominantColors` |
| **PresetService** | Curated dye preset palettes | `getPresets`, `getPresetsByCategory` |

```
src/
├── services/
│   ├── ColorService.ts      # Facade for color operations
│   ├── DyeService.ts        # Facade for dye operations
│   ├── APIService.ts        # Universalis API wrapper
│   ├── LocalizationService.ts
│   ├── PaletteService.ts    # K-means++ clustering
│   ├── PresetService.ts     # Curated presets
│   ├── color/               # Color sub-modules
│   │   ├── ColorConverter.ts
│   │   ├── ColorAccessibility.ts
│   │   ├── ColorManipulator.ts
│   │   └── ColorblindnessSimulator.ts
│   ├── dye/                 # Dye sub-modules
│   │   ├── DyeDatabase.ts   # k-d tree indexing
│   │   ├── DyeSearch.ts
│   │   └── HarmonyGenerator.ts
│   └── localization/
├── types/                   # TypeScript types + branded types
├── constants/               # Color theory constants, API config
└── utils/                   # Helpers (kd-tree, validation)
```

#### Performance Optimizations
- **k-d Tree**: DyeDatabase uses k-d tree in RGB space for O(log n) nearest-neighbor matching
- **Hue Bucketing**: HarmonyGenerator uses 10° hue buckets (36 total) for 70-90% faster harmony lookups
- **LRU Caching**: ColorConverter maintains 5 caches × 1000 entries for repeated conversions

### xivdyetools-web-app Architecture

Lit web components with service layer pattern. See `xivdyetools-web-app/CLAUDE.md` for details.

### xivdyetools-discord-worker Architecture

Cloudflare Worker using HTTP Interactions (stateless, globally distributed):

```
Discord → POST / → Ed25519 Verification → Hono Router → Handler
```

```
src/
├── handlers/
│   ├── commands/        # Slash command handlers (17 commands)
│   ├── buttons/         # Button interaction handlers
│   └── modals/          # Modal submission handlers
├── services/
│   ├── svg/             # SVG generation + PNG via resvg-wasm
│   ├── image/           # Image processing via Photon WASM
│   ├── analytics.ts     # Analytics Engine + KV-based stats
│   ├── rate-limiter.ts  # Per-user sliding window (KV-backed)
│   ├── user-storage.ts  # Favorites & collections (KV-backed)
│   └── preset-api.ts    # Service Binding to presets-api
└── utils/
    └── verify.ts        # Ed25519 signature verification
```

**Available Commands:**
- `/harmony`, `/match`, `/match_image`, `/mixer` - Color tools
- `/dye` (search/info/list/random) - Dye database
- `/comparison`, `/accessibility` - Analysis tools
- `/favorites`, `/collection` - User data management
- `/preset` (list/show/submit/vote/edit/moderate) - Community presets
- `/language`, `/manual`, `/about`, `/stats` - Utility commands

### xivdyetools-oauth Architecture

PKCE-secured Discord OAuth flow with JWT issuance:

```
Frontend (PKCE) → /auth/discord → Discord OAuth → /auth/callback → JWT
```

### xivdyetools-presets-api Architecture

Hono + Cloudflare D1 with moderation pipeline:

```
Request → Auth Middleware → Handler → D1 Database
                ↓
        Local Profanity Filter → Perspective API (optional)
```

---

## Working with Core Library Changes

When modifying `xivdyetools-core`:

1. Make changes in `xivdyetools-core/src/`
2. Run tests: `npm test`
3. Build: `npm run build`
4. Bump version in `package.json`
5. Publish: `npm publish`
6. Update version in consumer `package.json` files
7. Run `npm install` in consumer projects
8. Test all four consumers to verify no regressions

### Testing Single File
```bash
npm test -- src/services/__tests__/ColorService.test.ts
npm test -- --grep "hexToRgb"  # Run tests matching pattern
```

---

## Environment Setup

### xivdyetools-discord-worker
Set via `wrangler secret put`:
```
DISCORD_TOKEN           # Bot token for sending messages
DISCORD_PUBLIC_KEY      # For Ed25519 signature verification
BOT_API_SECRET          # Shared secret for presets API auth
STATS_AUTHORIZED_USERS  # Comma-separated user IDs for /stats access
MODERATOR_IDS           # Comma-separated user IDs for preset moderation
```

### xivdyetools-oauth
Set via `wrangler secret put`:
```
DISCORD_CLIENT_SECRET
JWT_SECRET
```

### xivdyetools-presets-api
Set via `wrangler secret put`:
```
BOT_API_SECRET
JWT_SECRET
PERSPECTIVE_API_KEY  # Optional, for ML moderation
```

---

## Key Patterns

### Service Singletons (Core)
```typescript
import { ColorService, DyeService, dyeDatabase } from 'xivdyetools-core';

// ColorService is static
const rgb = ColorService.hexToRgb('#FF6B6B');

// DyeService needs database injection
const dyeService = new DyeService(dyeDatabase);
```

### Cache Backend Interface (Core)
APIService accepts pluggable cache backends:
```typescript
interface ICacheBackend {
  get(key: string): Promise<CachedData | null>;
  set(key: string, value: CachedData): Promise<void>;
  delete(key: string): Promise<void>;
  clear(): Promise<void>;
}
```

### Service Bindings (Cloudflare Workers)
Prefer Service Bindings for worker-to-worker communication:
```typescript
// No HTTP overhead between workers
if (env.PRESETS_API) {
  return env.PRESETS_API.fetch(request);
}
```

### D1 Database Pattern
Parameterized queries with typed results:
```typescript
const result = await env.DB.prepare(
  'SELECT * FROM presets WHERE id = ?'
).bind(presetId).first<Preset>();
```

### HTTP Interactions Command Pattern
```typescript
export async function handleCommand(
  interaction: DiscordInteraction,
  env: Env,
  ctx: ExecutionContext
): Promise<Response> {
  await sendDeferredResponse(interaction, env);
  // Process and send follow-up
  return new Response(null, { status: 202 });
}
```

### Dual Authentication (Presets API)
```typescript
// 1. Bot Auth: Bearer <BOT_API_SECRET> + X-User-Discord-ID header
// 2. Web Auth: JWT bearer token from OAuth worker
```

### UI/Graphics Guidelines
- Favor SVG files over emojis for graphical elements
- Use resvg-wasm for SVG→PNG in Workers
- Use Photon WASM for image processing in Workers

---

## Localization

Core library supports 6 languages: `en`, `ja`, `de`, `fr`, `ko`, `zh`

Locale files are built from CSV source:
```bash
cd xivdyetools-core
npm run build:locales  # Generates dist/locales/*.json
```

All consumers auto-detect locale from user settings (Discord locale, browser lang).

---

## Version Management

When releasing:
1. Update version in respective `package.json`
2. For core: `npm publish` (triggers prepublishOnly build)
3. For web app: Deploy built `dist/` folder
4. For Workers: `npm run deploy:production`

---

## Project-Specific Documentation

- **Core Library**: See `xivdyetools-core/CLAUDE.md`
- **Web App**: See `xivdyetools-web-app/CLAUDE.md`
- **Discord Bot (Worker)**: See `xivdyetools-discord-worker/CLAUDE.md`
- **OAuth Worker**: See `xivdyetools-oauth/CLAUDE.md`
- **Presets API**: See `xivdyetools-presets-api/CLAUDE.md`
- **Feature Specs**: See `xivdyetools-docs/` folder
