# V4 Parity Update

**Bringing the Discord Bot to feature parity with the Web App v4.0.0**

---

## Overview

Version 4.0.0 aligns the Discord bot's command names and features with the web app. This is a breaking change release with no backwards compatibility for old command names.

## Version Change

- **Previous**: v2.3.3
- **New**: v4.0.0 (matching web app version)

---

## Command Changes Summary

### Renamed Commands

| v2 Command | v4 Command | Notes |
|------------|------------|-------|
| `/match <color>` | `/extractor color <color>` | Now a subcommand |
| `/match_image <image>` | `/extractor image <image>` | Merged under /extractor |
| `/mixer <start> <end>` | `/gradient <start> <end>` | Frees up /mixer for new feature |

### New Commands

| Command | Description | Web App Equivalent |
|---------|-------------|-------------------|
| `/mixer <dye1> <dye2> [mode]` | Blend two dyes with selectable algorithm | Dye Mixer tool |
| `/swatch <type> <color> [clan] [gender]` | Match character colors to dyes | Swatch Matcher tool |

**`/mixer` Blending Modes**:
- `rgb` - Standard additive color mixing (default)
- `ryb` - Traditional artist's color wheel (Red-Yellow-Blue)
- `lab` - Perceptually uniform CIELAB blending

### Removed Commands

| Command | Reason |
|---------|--------|
| `/favorites` | Functionality covered by /preset |
| `/collection` | Functionality covered by /preset |

### Enhanced Commands

| Command | Enhancement |
|---------|-------------|
| `/harmony` | V4-style color wheel with center swatch and connection lines |
| `/comparison` | Now includes LAB color values for each dye |
| `/dye info` | Full Result Card with Technical and Acquisition sections |
| `/dye random` | Visual infographic with five Result Cards |

---

**`/harmony` V4 Color Wheel** - The color wheel visualization now matches the web app's v4 design:

| Element | Description |
|---------|-------------|
| **Center Swatch** | Large circular swatch showing the base color with colored glow |
| **Color Ring** | Smooth conic gradient spectrum around the wheel |
| **Connection Lines** | Dashed white lines from center to each harmony node |
| **Harmony Nodes** | Circular markers at calculated angles on the ring |
| **Type Label** | Harmony type name displayed below the center |

---

**`/dye info` Result Card** - The info subcommand now generates a visual Result Card containing:

| Section | Contents |
|---------|----------|
| **Technical** | HEX, RGB, HSV, LAB color values |
| **Acquisition** | Source (how to obtain), Vendor Cost |

**Note**: Market Board data is intentionally excluded from `/dye info`. Use `/budget` for market pricing.

### Unchanged Commands

| Command | Status |
|---------|--------|
| `/harmony` | ✅ Structure unchanged (visualization enhanced) |
| `/comparison` | ✅ Structure unchanged (LAB values added) |
| `/accessibility` | ✅ No changes |
| `/preset` | ✅ No changes |
| `/budget` | ✅ No changes |
| `/dye` | ✅ Structure unchanged (info subcommand enhanced) |
| `/language` | ✅ No changes |
| `/manual` | ✅ No changes |
| `/about` | ✅ No changes |
| `/stats` | ✅ No changes |

---

## Feature Parity Matrix

| Web App Tool | Discord Command | Status |
|--------------|-----------------|--------|
| Harmony Explorer | `/harmony` | ✅ Aligned |
| Palette Extractor | `/extractor` | ✅ Renamed |
| Accessibility Checker | `/accessibility` | ✅ Aligned |
| Dye Comparison | `/comparison` | ✅ Aligned |
| Gradient Builder | `/gradient` | ✅ Renamed |
| Dye Mixer | `/mixer` | 📋 New |
| Community Presets | `/preset` | ✅ Aligned |
| Budget Suggestions | `/budget` | ✅ Aligned |
| Swatch Matcher | `/swatch` | 📋 New |

---

## V4 Design Standards

### Result Cards in Infographics

All commands that generate infographics displaying dye results should include **V4 Result Cards** where appropriate. This ensures visual consistency with the web app.

**Commands using Result Cards**:

| Command | Result Card Usage |
|---------|-------------------|
| `/harmony` | Each harmony node displays a Result Card with matched dye info |
| `/extractor` | Each matched dye shown as a Result Card |
| `/gradient` | Intermediate dyes displayed as Result Cards |
| `/mixer` | Blended result matches shown as Result Cards |
| `/comparison` | Each dye displayed in card format (already card-based) |
| `/budget` | Alternative dyes shown as Result Cards with pricing |
| `/swatch` | Matched dyes displayed as Result Cards |
| `/dye info` | Single dye shown as full Result Card |
| `/dye random` | Five random dyes shown as Result Cards |

**Result Card Standard Contents**:

| Section | Data |
|---------|------|
| **Header** | Dye name with category indicator |
| **Preview** | Color swatch |
| **Technical** | HEX, RGB, HSV, LAB |
| **Acquisition** | Source, Vendor Cost |
| **Match Quality** | Delta-E, Hue Deviance (when comparing to target) |
| **Pricing** | Market price (only for `/budget` command) |

**Note**: Not all fields are shown on every card. Context determines which fields are relevant:
- `/dye info`, `/dye random` show Technical + Acquisition (no Delta-E, no pricing)
- `/budget` shows all fields including pricing
- `/harmony`, `/extractor`, `/mixer`, `/gradient`, `/swatch` show Technical + Delta-E

---

## Migration Notes

### For Users

- Old commands (`/match`, `/match_image`, `/mixer`) will no longer work
- Use `/extractor color` instead of `/match`
- Use `/extractor image` instead of `/match_image`
- Use `/gradient` instead of `/mixer` for creating color gradients
- Favorites and collections have been removed; use `/preset` for saved color schemes

### For Developers

- Handler files renamed to match new command names
- `match.ts` → `extractor.ts` (with subcommand handlers)
- `mixer.ts` → `gradient.ts`
- New files: `mixer.ts` (dye blending), `swatch.ts` (character colors)
- Remove: `favorites.ts`, `collection.ts`
- Update command registration in `register-commands.ts`

---

## Discord-Only Features

These commands exist only in the Discord bot (not in web app):

| Command | Purpose |
|---------|---------|
| `/dye` | Search and explore the dye database |
| `/language` | Set language preference |
| `/manual` | View help documentation |
| `/about` | Bot information and version |
| `/stats` | Usage statistics (admin only) |
