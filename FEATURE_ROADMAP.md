# XIV Dye Tools - Feature Roadmap

> Last Updated: December 4, 2024

This document outlines planned features for the XIV Dye Tools monorepo, prioritized by implementation phase.

---

## Overview

### Priority Features

| Feature | Platform | Core Changes | Effort | Status |
|---------|----------|--------------|--------|--------|
| Multi-Color Palette Extraction | Web + Bot | Yes | Medium-High | Planned |
| Seasonal/Themed Preset Palettes | Web + Bot | Yes | Medium | Planned |
| Dye Collections/Favorites | Web + Bot | No | Medium | Planned |
| Budget-Aware Dye Suggestions | Web + Bot | Optional | Medium | Planned |

### Quick Wins

| Feature | Platform | Effort | Status |
|---------|----------|--------|--------|
| Random Dye Button | Web App | Low | ✅ Done |
| Market Prices in Commands | Discord Bot | Low | ✅ Done |
| Copy Dye Info Buttons | Web App | Low | ✅ Done |
| Copy Dye Info Buttons | Discord Bot | Low | Planned |

### Housekeeping

| Task | Location | Status |
|------|----------|--------|
| Dead code cleanup (12-slot remnants) | Web App - Accessibility Checker | ✅ Done |

---

## Implementation Phases

### Phase 1: Quick Wins (1-2 days each)

#### 1. Random Dye Button (Web App) ✅

Port the Discord bot's `/dye random` functionality to the web app.

**Implemented Features:**
- ✅ "Random Dye" button with SVG dice icon in dye selector search bar
- ✅ Respects current category filter and search query
- ✅ Selects from filtered dye list (contextual randomness)

**Not Implemented (Future Enhancement):**
- Filter options: exclude metallic, pastel, dark, cosmic, expensive
- Count selector (1-5 random dyes)
- Keyboard shortcut: `R` for random

**Files modified:**
- `xivdyetools-web-app/src/components/dye-search-box.ts` - Added random button UI
- `xivdyetools-web-app/src/components/dye-selector.ts` - Added `selectRandomDye()` method
- `xivdyetools-web-app/src/shared/ui-icons.ts` - Added `ICON_DICE` SVG
- `xivdyetools-web-app/src/locales/en.json` - Added localization keys

---

#### 2. Copy Dye Info Buttons (Both Platforms) - Web App ✅

One-click copy buttons for dye information.

**Implemented (Web App):**
- ✅ Hex code (`#FF0000`) - click to copy
- ✅ RGB (`rgb(255, 0, 0)`) - click to copy
- ✅ HSV (`hsv(0, 100%, 100%)`) - click to copy
- ✅ Visual feedback: "Copied!" text replacement for 2 seconds
- ✅ Hover states with blue highlight

**Not Implemented (Future Enhancement):**
- Full info (Name + Hex + Category)
- CSS variable (`--dye-dalamud-red: #FF0000;`)
- Dedicated copy icon buttons (currently uses clickable text)

**Files modified:**
- `xivdyetools-web-app/src/components/color-display.ts` - Added `data-copy` attributes to RGB/HSV values

**Discord Bot:** Planned - Formatted embed fields for easy mobile copy

---

#### 3. Dead Code Cleanup (Web App) ✅

Remove remnants of the 12-slot system (2 per equipment piece) from the Accessibility Checker.

**Completed:**
- ✅ Deleted `outfit-slot-selector.ts` - Unused component (291 lines)
- ✅ Deleted `outfit-slot-selector.test.ts` - Associated tests
- ✅ Removed `OutfitSlotSelector` export from `components/index.ts`
- ✅ Removed `AccessibilityState` interface from `shared/types.ts`
- ✅ Removed stale tests from `accessibility-checker-tool.test.ts`:
  - "should maintain dual dyes toggle state"
  - "should handle all 6 outfit slots filled"
  - "should handle mixed dual/single dyes"

**Total lines removed:** ~1,298 lines of dead code

---

### Phase 2: Foundation (1 week)

#### 4. Market Prices in Discord Bot ✅

Add Universalis price data to existing commands.

**Implemented Features:**
- ✅ `/match` - Shows market price of matched dye
- ✅ `/harmony` - Shows market prices for base + all companion dyes
- ✅ `/dye info` - Shows current market price
- ✅ PriceService singleton with RedisCacheBackend (10-minute TTL)
- ✅ Graceful degradation when Universalis API unavailable
- ✅ API availability check with 60-second cooldown
- ✅ Translation keys for price display

**Not Implemented:**
- `/comparison` - prices in comparison view (future)
- Optional `show_prices` parameter (always enabled)
- "prices may be outdated" disclaimer (implicit in cache)

**Files created/modified:**
- `xivdyetools-discord-bot/src/services/price-service.ts` (new)
- `xivdyetools-discord-bot/src/commands/match.ts`
- `xivdyetools-discord-bot/src/commands/harmony.ts`
- `xivdyetools-discord-bot/src/commands/dye.ts`
- `xivdyetools-discord-bot/src/utils/embed-builder.ts`
- `xivdyetools-discord-bot/src/i18n/translations/en.json`

---

#### 5. Dye Collections / Favorites (Web App first, then Discord Bot)
Save favorite dyes and organize into named collections.

**Features:**
- Star/favorite individual dyes (quick access)
- Create named collections ("My Red Mage palette", "Casual looks")
- Import/export collections as JSON
- Quick-add from any dye selector

See [COLLECTIONS_SPEC.md](./COLLECTIONS_SPEC.md) for detailed specification.

---

### Phase 3: Major Features (2-3 weeks)

#### 6. Seasonal/Themed Preset Palettes
Pre-made color palettes for common themes.

**Preset Categories:**
- **FFXIV Jobs:** Red Mage, Black Mage, White Mage, etc.
- **Grand Companies:** Maelstrom, Twin Adders, Immortal Flames
- **Seasons:** Spring, Summer, Autumn, Winter
- **FFXIV Events:** Starlight, Moonfire, Rising
- **Aesthetics:** Gothic, Pastel, Military, Royal

See [PRESET_PALETTES.md](./PRESET_PALETTES.md) for detailed specification.

---

#### 7. Multi-Color Palette Extraction
Extract multiple dominant colors from an image (3-5 colors) instead of just one.

**User Value:**
- Match entire glamour screenshots, not just single pieces
- Create palettes from inspiration images
- Capture the "vibe" of an image with a full palette

See [MULTI_COLOR_EXTRACTION.md](./MULTI_COLOR_EXTRACTION.md) for detailed specification.

---

#### 8. Budget-Aware Dye Suggestions
Find dyes similar to a target color within a budget.

**User Value:**
- "I want something like Jet Black but cheaper"
- Find affordable alternatives for expensive dyes
- Plan glamours within budget constraints

**Web App Implementation:**
- Budget slider/input in Color Matcher
- "Find cheaper alternatives" button on match results
- Sort results by: Best Match, Lowest Price, Best Value
- Show savings compared to exact match

**Discord Bot Implementation:**
- `/match [color] max_price:[amount]` - Optional budget filter
- `/dye alternatives [dye_name]` - Show cheaper similar dyes
- Include price + color distance in results

---

## Declined Features

| Feature | Reason |
|---------|--------|
| Outfit Builder / Glamour Planner | Serious glamour makers use Glamourer plugin in-game; limited value for this toolset |
| Similar Dye Finder | Functionality covered by Budget-Aware Suggestions |

---

## Technical Notes

### Universalis API Rate Limits
- **Rate limit:** 25 req/s (50 req/s burst)
- **Connections:** 8 simultaneous per IP
- **Strategy:** Batch requests, aggressive caching (5+ min TTL)
- **UX:** Show "prices may be outdated" disclaimers

### Core Library Changes
Features requiring core library modifications:
- Multi-color extraction → Add clustering algorithm
- Preset palettes → Add `PresetService` or extend `DyeService`
- Budget filtering → Optional enhancement to `DyeService`

### Platform Parity
All features developed for both platforms simultaneously where applicable.

---

## Related Documents

- [MULTI_COLOR_EXTRACTION.md](./MULTI_COLOR_EXTRACTION.md) - Technical spec for palette extraction
- [PRESET_PALETTES.md](./PRESET_PALETTES.md) - Preset data structure and palette list
- [COLLECTIONS_SPEC.md](./COLLECTIONS_SPEC.md) - Collections/favorites feature spec
