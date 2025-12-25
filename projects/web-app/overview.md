# Web App Overview

**xivdyetools-web-app** v3.2.8 - Interactive browser-based toolkit for FFXIV dye colors

---

## What is the Web App?

A fully-featured web application built with Lit and Vite, offering 7 interactive tools for exploring FFXIV dye colors:

| Tool | Purpose |
|------|---------|
| **Dye Mixer** | Create gradients between two dyes |
| **Color Matcher** | Find closest dye to any color + palette extraction |
| **Color Harmony Explorer** | Discover harmonious dye combinations |
| **Dye Comparison** | Compare dyes side-by-side |
| **Accessibility Checker** | Colorblindness simulation |
| **Preset Browser** | Browse community dye palettes |
| **Budget Suggestions** | Find affordable dye alternatives using market data |

### Recent Features (v3.2.x)

- **Dye Action Dropdown** - Context menu for quick actions on dye matches
- **Slot Selection Modal** - Choose which slot to replace when Comparison/Mixer is full
- **Duplicate Detection** - Toast notifications for duplicate presets
- **SVG Icon Consolidation** - Shared icons reduce bundle size by ~10KB
- **SubscriptionManager** - Prevents memory leaks from orphaned reactive subscriptions
- **Theme Factory Pattern** - `createThemePalette()` for easy theme creation

---

## Quick Start (Development)

```bash
cd xivdyetools-web-app

# Install dependencies
npm install

# Start dev server (localhost:5173)
npm run dev

# Run tests
npm test

# Build for production
npm run build
```

---

## Technology Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| **Components** | Lit | Web components framework |
| **Build** | Vite | Fast bundler and dev server |
| **Styling** | Tailwind CSS | Utility-first CSS |
| **Testing** | Vitest + Playwright | Unit and E2E tests |
| **Core Logic** | @xivdyetools/core | Color algorithms, dye database |

---

## Architecture

```
src/
├── components/                 # Lit web components
│   ├── tools/                  # Tool-specific components
│   │   ├── dye-mixer/
│   │   ├── color-matcher/
│   │   ├── harmony-explorer/
│   │   ├── dye-comparison/
│   │   ├── accessibility-checker/
│   │   ├── preset-browser/
│   │   └── budget-suggestions/    # NEW: Affordable alternatives
│   ├── shared/                 # Reusable components
│   │   ├── color-swatch/
│   │   ├── dye-picker/
│   │   ├── dye-action-dropdown/   # NEW: Context menu actions
│   │   ├── slot-selection-modal/  # NEW: Slot replacement UI
│   │   └── ...
│   └── layout/                 # App shell components
├── services/                   # Business logic layer
│   ├── ThemeService.ts         # Theme management
│   ├── StorageService.ts       # localStorage persistence
│   ├── AuthService.ts          # OAuth integration
│   ├── PresetService.ts        # Preset API client
│   └── SubscriptionManager.ts  # NEW: Reactive subscription cleanup
├── styles/                     # Global styles
│   ├── themes/                 # 12 theme files
│   └── tailwind.css
└── utils/                      # Helper functions
```

---

## Features

### 12 Themes

The app includes 12 professionally designed themes:

- **Light themes**: Default Light, Forest, Ocean, Sunset
- **Dark themes**: Default Dark, Midnight, Void, Abyss
- **Special**: High Contrast, Eorzean Gold, Crystal Tower, Moogle

Themes use CSS custom properties for easy customization.

### PWA Support

- Installable as standalone app
- Offline caching for static assets
- Fast startup via service worker

### Responsive Design

- Mobile-first approach
- Breakpoints: 640px, 768px, 1024px, 1280px
- Touch-friendly interactions

### Localization Ready

- 6 languages via @xivdyetools/core
- Browser language detection
- Manual language selection

---

## Environment Variables

```bash
# .env.local
VITE_OAUTH_URL=https://oauth.xivdyetools.com
VITE_PRESETS_API_URL=https://presets.xivdyetools.com
VITE_ANALYTICS_ID=optional-analytics-id
```

---

## Deployment

The app is deployed to Cloudflare Pages:

```bash
# Build
npm run build

# Preview locally
npm run preview

# Deploy (via Cloudflare Pages GitHub integration)
git push origin main
```

---

## Related Documentation

- [Tools](tools.md) - Detailed guide to all 7 tools
- [Components](components.md) - Lit component architecture
- [Theming](theming.md) - Theme system documentation
- [Deployment](deployment.md) - Deployment procedures
- [User Guide](../../user-guides/web-app/getting-started.md) - End-user documentation
