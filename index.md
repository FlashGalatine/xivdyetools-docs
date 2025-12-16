# XIV Dye Tools Documentation

**The comprehensive documentation bible for the XIV Dye Tools ecosystem**

This wiki-style documentation serves developers, end users, and maintainers with everything needed to understand, use, and contribute to XIV Dye Tools.

---

## Quick Navigation

| I want to... | Go to... |
|--------------|----------|
| Understand how projects connect | [Architecture Overview](architecture/overview.md) |
| Use the web app | [Web App User Guide](user-guides/web-app/getting-started.md) |
| Use the Discord bot | [Discord Bot Guide](user-guides/discord-bot/getting-started.md) |
| Set up development environment | [Local Setup](developer-guides/local-setup.md) |
| Integrate the core library | [Core Library Overview](projects/core/overview.md) |
| **Add new dyes after a patch** | [Dye Maintainer Tool](maintainer/dye-maintainer-tool.md) |
| Moderate community presets | [Moderation Guide](operations/MODERATION.md) |
| Check version numbers | [Version Matrix](versions.md) |
| Read feature specifications | [Specifications](specifications/index.md) |
| Review historical decisions | [History Archive](history/index.md) |

---

## Ecosystem at a Glance

```
                    ┌─────────────────────────────────┐
                    │        Shared Packages          │
                    │  @xivdyetools/types (v1.0.0)    │
                    │  @xivdyetools/logger (v1.0.0)   │
                    │  @xivdyetools/test-utils (v1.0.2)│
                    └────────────────┬────────────────┘
                                     │
                    ┌────────────────▼────────────────┐
                    │     @xivdyetools/core (v1.4.0)  │
                    │   136 dyes, color algorithms,   │
                    │   Universalis API, 6 languages  │
                    └──┬──────────────┬───────────┬───┘
                       │              │           │
         ┌─────────────▼──┐    ┌──────▼─────┐    │
         │   Web App      │    │  Discord   │    │
         │   (v3.1.0)     │    │  Worker    │    │
         │   6 tools,     │    │  (v2.2.0)  │    │
         │   12 themes    │    │  19 cmds   │    │
         └───────┬────────┘    └──────┬─────┘    │
                 │                    │          │
         ┌───────▼────────┐           │   ┌──────▼──────────┐
         │  OAuth Worker  │           │   │  Presets API    │
         │   (v2.1.0)     │◄──────────┴───│   (v1.2.0)      │
         │  PKCE + JWT    │               │  D1 + Moderation │
         └────────────────┘               └─────────────────┘
```

---

## Documentation Sections

### For Everyone

| Section | Description |
|---------|-------------|
| [Architecture](architecture/overview.md) | How all projects interconnect, service bindings, data flows |
| [Projects](projects/index.md) | Deep-dive documentation for each project |
| [Versions](versions.md) | Current version matrix and changelog links |

### For Users

| Section | Description |
|---------|-------------|
| [Web App Guides](user-guides/web-app/getting-started.md) | Step-by-step guides for all 6 web tools |
| [Discord Bot Guides](user-guides/discord-bot/getting-started.md) | Command reference and usage examples |

### For Developers

| Section | Description |
|---------|-------------|
| [Developer Guides](developer-guides/index.md) | Setup, testing, deployment, contributing |
| [Reference](reference/index.md) | Quick reference materials, glossary |
| [Specifications](specifications/index.md) | Feature specifications and roadmap |

### For Maintainers

| Section | Description |
|---------|-------------|
| [Maintainer Guide](maintainer/index.md) | Architecture decisions, known issues, tech debt |
| [History](history/index.md) | Development timeline organized by topic |

---

## Projects Overview

### Applications

| Project | Type | Version | Purpose |
|---------|------|---------|---------|
| [@xivdyetools/core](projects/core/overview.md) | npm library | v1.4.0 | Core color algorithms, 136-dye database, Universalis API |
| [xivdyetools-web-app](projects/web-app/overview.md) | Vite + Lit | v3.1.0 | Interactive web toolkit with 6 color tools |
| [xivdyetools-discord-worker](projects/discord-worker/overview.md) | CF Worker | v2.2.0 | Discord bot with 19 slash commands |
| [xivdyetools-oauth](projects/oauth/overview.md) | CF Worker | v2.1.0 | Discord OAuth + JWT issuance |
| [xivdyetools-presets-api](projects/presets-api/overview.md) | CF Worker + D1 | v1.2.0 | Community presets with moderation |

### Shared Libraries

| Project | Type | Version | Purpose |
|---------|------|---------|---------|
| [@xivdyetools/types](projects/types/overview.md) | npm library | v1.0.0 | Shared TypeScript type definitions |
| [@xivdyetools/logger](projects/logger/overview.md) | npm library | v1.0.0 | Unified logging across environments |
| [@xivdyetools/test-utils](projects/test-utils/overview.md) | npm library | v1.0.2 | Shared testing utilities |

### Developer Tools

| Project | Type | Version | Purpose |
|---------|------|---------|---------|
| [xivdyetools-maintainer](maintainer/dye-maintainer-tool.md) | Vue 3 + Express | v1.0.0 | GUI for adding new dyes to the core library |

---

## Recent Updates

*This section will be updated with links to recent changes and releases.*

- See [Release History](history/releases/index.md) for version changelog
- See [Feature Roadmap](specifications/feature-roadmap.md) for planned features

---

## Contributing

See the [Contributing Guide](developer-guides/contributing.md) for information on how to contribute to XIV Dye Tools.

---

## License

MIT License - See individual project repositories for details.

## Legal Notice

FINAL FANTASY is a registered trademark of Square Enix Holdings Co., Ltd. This project is not affiliated with or endorsed by Square Enix.
