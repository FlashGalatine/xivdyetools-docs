# Version Matrix

**Single source of truth for all XIV Dye Tools project versions**

*Last Updated: December 15, 2025*

---

## Current Versions

### Core Applications

| Project | Version | Package Name | Platform | Status |
|---------|---------|--------------|----------|--------|
| **Core Library** | v1.4.0 | `@xivdyetools/core` | npm | Active |
| **Web Application** | v3.1.0 | — | Cloudflare Pages | Active |
| **Discord Bot** | v2.2.0 | — | Cloudflare Workers | Active |
| **OAuth Worker** | v2.1.0 | — | Cloudflare Workers | Active |
| **Presets API** | v1.2.0 | — | Cloudflare Workers | Active |

### Developer Tools

| Project | Version | Package Name | Platform | Status |
|---------|---------|--------------|----------|--------|
| **Dye Maintainer** | v1.0.0 | — | Local (Vite + Express) | Active |

### Shared Packages

| Package | Version | Package Name | Platform | Status |
|---------|---------|--------------|----------|--------|
| **Types** | v1.0.0 | `@xivdyetools/types` | npm | Active |
| **Logger** | v1.0.0 | `@xivdyetools/logger` | npm | Active |
| **Test Utils** | v1.0.2 | `@xivdyetools/test-utils` | npm | Active |

### Deprecated

| Project | Last Version | Replacement |
|---------|--------------|-------------|
| xivdyetools-discord-bot | Archived | xivdyetools-discord-worker |

---

## Version History

### @xivdyetools/core

| Version | Date | Highlights |
|---------|------|------------|
| v1.4.0 | Dec 2025 | Current release |
| v1.3.7 | Dec 2025 | Bug fixes, performance improvements |
| v1.3.0 | Nov 2025 | K-means++ palette extraction |
| v1.2.0 | Nov 2025 | Preset service, localization |
| v1.0.0 | Nov 2025 | Initial release |

### xivdyetools-web-app

| Version | Date | Highlights |
|---------|------|------------|
| v3.1.0 | Dec 2025 | Current release, SVG icon redesign |
| v3.0.0 | Dec 2025 | UI/UX rehaul, new theme system |
| v2.6.0 | Dec 2025 | Community presets browser |
| v2.0.0 | Nov 2025 | Major release with 6 tools |
| v1.6.x | Legacy | Original HTML-based tools |

### xivdyetools-discord-worker

| Version | Date | Highlights |
|---------|------|------------|
| v2.2.0 | Dec 2025 | Current release, `/preset ban_user` and `unban_user` commands |
| v2.1.0 | Dec 2025 | Moderation infrastructure |
| v2.0.1 | Dec 2025 | Bug fixes |
| v2.0.0 | Dec 2025 | HTTP Interactions migration |
| v1.0.0 | Nov 2025 | Initial Cloudflare Worker release |

### xivdyetools-oauth

| Version | Date | Highlights |
|---------|------|------------|
| v2.1.0 | Dec 2025 | Current release |
| v1.1.0 | Dec 2025 | Refresh token improvements |
| v1.0.0 | Nov 2025 | Initial release with PKCE |

### xivdyetools-presets-api

| Version | Date | Highlights |
|---------|------|------------|
| v1.2.0 | Dec 2025 | Current release |
| v1.1.0 | Dec 2025 | Moderation pipeline |
| v1.0.0 | Nov 2025 | Initial release |

---

## Compatibility Matrix

| Consumer | Minimum Core Version | Notes |
|----------|---------------------|-------|
| Web App v3.x | @xivdyetools/core v1.3.0+ | Requires palette service |
| Discord Worker v2.x | @xivdyetools/core v1.3.0+ | Requires preset service |
| Presets API v1.x | @xivdyetools/core v1.2.0+ | Requires localization |

---

## Updating Versions

When releasing a new version:

1. **Core Library**:
   - Update `xivdyetools-core/package.json`
   - Run `npm publish`
   - Update this document

2. **Workers**:
   - Update respective `package.json`
   - Run `npm run deploy:production`
   - Update this document

3. **Web App**:
   - Update `xivdyetools-web-app/package.json`
   - Deploy to Cloudflare Pages
   - Update this document

See [Release Process](developer-guides/release-process.md) for detailed instructions.
