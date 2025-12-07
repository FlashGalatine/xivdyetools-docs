# XIV Dye Tools Documentation

> Feature specifications, roadmaps, and design documents for the XIV Dye Tools ecosystem.

## Overview

This repository contains planning documents, feature specifications, and design documentation for the XIV Dye Tools project family. These documents guide development and serve as reference material.

## Contents

### Feature Specifications

| Document | Description |
|----------|-------------|
| [COMMUNITY_PRESETS_SPEC.md](./COMMUNITY_PRESETS_SPEC.md) | Community preset submission, voting, and moderation system |
| [COLLECTIONS_SPEC.md](./COLLECTIONS_SPEC.md) | User collections feature for organizing favorite dyes |
| [MULTI_COLOR_EXTRACTION.md](./MULTI_COLOR_EXTRACTION.md) | K-means++ palette extraction from images |
| [PRESET_PALETTES.md](./PRESET_PALETTES.md) | Curated preset color palettes |
| [BUDGET_AWARE_SUGGESTIONS.md](./BUDGET_AWARE_SUGGESTIONS.md) | Price-conscious dye recommendations |

### Roadmaps

| Document | Description |
|----------|-------------|
| [FEATURE_ROADMAP.md](./FEATURE_ROADMAP.md) | Planned features and development priorities |

### Project-Specific Documentation

| Folder | Description |
|--------|-------------|
| [20251207-DiscordBotMigration/](./20251207-DiscordBotMigration/) | Discord bot migration from Gateway to HTTP Interactions |
| [20251207-PresetRefinements/](./20251207-PresetRefinements/) | Preset system refinements and improvements |
| [historical/](./historical/) | Archived documentation from previous development phases |

## Related Projects

This documentation covers the following projects:

| Project | Description |
|---------|-------------|
| **xivdyetools-core** | Core color algorithms and dye database (npm library) |
| **xivdyetools-web-app** | Interactive web-based color tools |
| **xivdyetools-discord-bot** | Traditional Discord bot (Discord.js + Gateway) |
| **xivdyetools-discord-worker** | Serverless Discord bot (Cloudflare Workers) |
| **xivdyetools-oauth** | Discord OAuth authentication worker |
| **xivdyetools-presets-api** | Community presets REST API |

## Contributing

When adding new documentation:

1. Use clear, descriptive filenames (e.g., `FEATURE_NAME_SPEC.md`)
2. Include a header with status, date, and author
3. Follow the existing format for consistency
4. Update this README if adding new categories

### Document Template

```markdown
# Feature Name

**Status**: Draft | In Progress | Complete
**Date**: YYYY-MM-DD
**Author**: Your Name

## Overview

Brief description of the feature.

## Requirements

- Requirement 1
- Requirement 2

## Design

Technical design details...

## Implementation Notes

Any implementation-specific details...
```

## License

MIT © 2025 Flash Galatine

## Legal Notice

**This is a fan-made tool and is not affiliated with or endorsed by Square Enix Co., Ltd. FINAL FANTASY is a registered trademark of Square Enix Holdings Co., Ltd.**

## Support

- **Issues**: [GitHub Issues](https://github.com/FlashGalatine/xivdyetools/issues)
- **Discord**: [Join Server](https://discord.gg/rzxDHNr6Wv)

---

**Made with ❤️ for the FFXIV community**
