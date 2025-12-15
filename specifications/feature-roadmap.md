# Feature Roadmap

**Tracking planned and completed features across XIV Dye Tools**

> Last Updated: December 2025

---

## Feature Status Overview

### ✅ Completed Features

| Feature | Platform | Completed |
|---------|----------|-----------|
| Multi-Color Palette Extraction | Web + Bot | Dec 2025 |
| Seasonal/Themed Preset Palettes | Web + Bot | Dec 2025 |
| Dye Collections/Favorites | Web + Bot | Dec 2025 |
| Community Presets System | Web + Bot + API | Dec 2025 |
| Random Dye Button | Web App | Nov 2025 |
| Market Prices in Commands | Discord Bot | Nov 2025 |
| Copy Dye Info Buttons | Web + Bot | Nov 2025 |
| 6-Language Localization | All | Nov 2025 |
| HTTP Interactions Bot Migration | Discord Bot | Dec 2025 |
| PKCE OAuth Authentication | Web App | Dec 2025 |

### 📋 Planned Features

| Feature | Platform | Priority | Effort |
|---------|----------|----------|--------|
| Budget-Aware Dye Suggestions | Web + Bot | Medium | Medium |
| XIVAuth Integration | OAuth | Low | Medium |
| Gear Preview Integration | Web App | Low | High |

---

## Completed Feature Details

### Multi-Color Palette Extraction
**Completed:** December 2025

Extract dominant colors from images using K-means++ clustering.

**Implementation:**
- K-means++ algorithm in `@xivdyetools/core`
- Web: File upload + canvas processing
- Discord: Image attachment + Photon WASM processing
- Quality settings: low, medium, high

### Community Presets System
**Completed:** December 2025

Full specification: [community-presets.md](community-presets.md)

**Implementation:**
- `xivdyetools-presets-api` Cloudflare Worker
- D1 SQLite database
- Multi-layer content moderation
- Cross-platform voting

### Collections System
**Completed:** December 2025

Full specification: [collections.md](collections.md)

**Implementation:**
- Web: localStorage with JSON serialization
- Discord: Cloudflare KV storage
- Max 50 collections, 20 dyes each

### HTTP Interactions Migration
**Completed:** December 2025

Migrated Discord bot from Gateway WebSocket to HTTP Interactions.

**Benefits:**
- Serverless (no persistent connection)
- Global edge deployment
- Cost reduction (~$0/month vs $5+/month)
- Auto-scaling

---

## Planned Feature Details

### Budget-Aware Dye Suggestions
**Priority:** Medium | **Effort:** Medium

Suggest affordable alternatives to expensive dyes.

**Concept:**
1. User selects a dye
2. System fetches current market price via Universalis
3. If price exceeds threshold, suggest similar but cheaper dyes
4. Show price comparison

**Requirements:**
- Real-time price fetching (existing in core)
- Visual similarity calculation (existing in core)
- Price threshold configuration
- UI for alternative display

**Technical Approach:**
```typescript
interface BudgetSuggestion {
  originalDye: Dye;
  originalPrice: number;
  alternatives: Array<{
    dye: Dye;
    price: number;
    deltaE: number;      // Visual difference
    savings: number;     // Gil saved
    savingsPercent: number;
  }>;
}
```

### XIVAuth Integration
**Priority:** Low | **Effort:** Medium

Add XIVAuth as alternative authentication provider.

**Benefits:**
- Verify FFXIV character ownership
- Character-linked favorites
- Role-based features (verified players)

**Requirements:**
- XIVAuth OAuth integration
- Account linking (Discord ↔ XIVAuth)
- JWT claims for character data

---

## Quick Wins Backlog

Small improvements that can be done in 1-2 days:

| Feature | Platform | Status |
|---------|----------|--------|
| Keyboard shortcuts help modal | Web App | Pending |
| Export collection to JSON | Web App | Pending |
| Share preset via URL | Web App | Pending |
| Batch dye comparison | Discord Bot | Pending |

---

## Version Milestones

### v3.0.0 (December 2025) ✅
- UI/UX redesign
- SVG icon system
- New theme system
- Community presets browser

### v3.1.0 (December 2025) ✅
- Collections system
- Improved preset browser
- Bug fixes

### v3.2.0 (Future)
- Budget-aware suggestions
- Performance improvements
- Additional themes

---

## Contributing Features

To propose a new feature:

1. Open a GitHub issue with the `feature-request` label
2. Include:
   - Problem statement
   - Proposed solution
   - Affected platforms
   - Effort estimate
3. Community discussion
4. If approved, create specification document

---

## Related Documentation

- [Specifications Index](index.md)
- [Community Presets](community-presets.md)
- [Budget-Aware Suggestions](budget-aware-suggestions.md)
