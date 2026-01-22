# Command Reference v4

**Complete reference for XIV Dye Tools Discord Bot v4.0.0**

---

## Color Tools

### /harmony

Generate color harmony palettes based on color theory with a visual color wheel.

**Usage**: `/harmony <color> [type]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `color` | string | Yes | Hex color or dye name |
| `type` | choice | No | Harmony type (default: complementary) |

**Harmony Types**: complementary, analogous, triadic, split-complementary, tetradic, square, monochromatic

**V4 Color Wheel Visualization**:

The harmony command generates an SVG color wheel matching the web app's v4 design:

```
         ╭──────────────────────╮
        ╱    Color Spectrum      ╲
       │    ┌─────────────┐       │
       │   ╱   ╲     ╱   ╲        │
       │  ●─────●───●─────●       │  ← Harmony nodes on ring
       │   ╲   ╱     ╲   ╱        │
       │    ╲ ╱       ╲ ╱         │
       │     ●─────────●          │  ← Dashed lines to center
       │      ╲       ╱           │
       │       ╲     ╱            │
       │        ╲   ╱             │
       │       ┌─────┐            │
       │       │ ███ │            │  ← Center swatch (base color)
       │       │ ███ │            │     with colored glow
       │       └─────┘            │
       │     COMPLEMENTARY        │  ← Harmony type label
        ╲                        ╱
         ╰──────────────────────╯
```

| Element | Description |
|---------|-------------|
| **Center Swatch** | Large circular swatch showing the base/selected color with a colored glow effect |
| **Color Ring** | Smooth conic gradient spectrum (red → yellow → green → cyan → blue → magenta → red) |
| **Harmony Nodes** | Circular markers positioned at calculated angles around the ring |
| **Connection Lines** | Dashed white lines from center to each harmony node |
| **Type Label** | Harmony type name displayed below center |

**Output**: V4 color wheel visualization with harmony dyes displayed as Result Cards showing Technical data and match quality.

**Enhanced in v4.0.0** - Now uses v4-style color wheel with center swatch and connection lines.

---

### /extractor

Find matching FFXIV dyes from colors or images.

**Subcommands**:

#### /extractor color

Find closest dye matches for a color.

**Usage**: `/extractor color <color> [count]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `color` | string | Yes | Hex color or dye name |
| `count` | integer | No | Number of matches (1-10, default: 5) |

**Output**: Each matched dye displayed as a V4 Result Card with Technical data and Delta-E match quality.

#### /extractor image

Extract colors from an uploaded image.

**Usage**: `/extractor image <image> [colors]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `image` | attachment | Yes | Image file to analyze |
| `colors` | integer | No | Colors to extract (1-5, default: 3) |

**Output**: Extracted colors with matching dyes displayed as V4 Result Cards.

---

### /gradient

Create color gradients between two dyes.

**Usage**: `/gradient <start> <end> [steps]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start` | string | Yes | Starting color/dye |
| `end` | string | Yes | Ending color/dye |
| `steps` | integer | No | Intermediate steps (2-10, default: 5) |

**Output**: Gradient visualization with intermediate dyes displayed as V4 Result Cards showing Technical data and Delta-E from ideal gradient color.

---

### /mixer

Blend two dyes together to find dyes that match the blended result.

**Usage**: `/mixer <dye1> <dye2> [mode] [count]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `dye1` | string | Yes | First dye to blend |
| `dye2` | string | Yes | Second dye to blend |
| `mode` | choice | No | Color blending algorithm (default: rgb) |
| `count` | integer | No | Number of results (3-8, default: 5) |

**Blending Modes**:

| Mode | Description |
|------|-------------|
| `rgb` | **RGB Blending** (default) - Standard additive color mixing. Averages red, green, and blue channels. Best for digital/screen colors. |
| `ryb` | **RYB Blending** - Traditional artist's color wheel (Red-Yellow-Blue). Produces results closer to physical paint mixing. Red + Yellow = Orange, Blue + Yellow = Green. |
| `lab` | **LAB Blending** - Perceptually uniform mixing in CIELAB color space. Produces the most visually "balanced" blend between two colors. |

**Example Results** (Snow White + Soot Black):

| Mode | Result |
|------|--------|
| RGB | Mid-gray with neutral tone |
| RYB | Mid-gray (similar to RGB for neutrals) |
| LAB | Perceptually balanced gray |

**Output**: Blended color swatch with matching dyes displayed as V4 Result Cards showing Technical data and Delta-E from the blended color.

**New in v4.0.0**

---

## Analysis Tools

### /comparison

Compare 2-4 dyes side-by-side with visual swatches and color analysis.

**Usage**: `/comparison <dye1> <dye2> [dye3] [dye4]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `dye1` | string | Yes | First dye |
| `dye2` | string | Yes | Second dye |
| `dye3` | string | No | Third dye |
| `dye4` | string | No | Fourth dye |

**Comparison Card Contents**:

Each dye card displays:

| Data | Description |
|------|-------------|
| **Dye Name** | Full name with truncation indicator if needed |
| **Category** | Dye category (Reds, Blues, Yellows, etc.) |
| **HEX** | Hex color code |
| **RGB** | Red, Green, Blue values |
| **HSV** | Hue, Saturation, Value |
| **LAB** | CIELAB color space values |

**Color Analysis Section**:

| Metric | Description |
|--------|-------------|
| **Most Similar** | Pair with smallest color distance |
| **Most Different** | Pair with largest color distance |
| **Distance** | Euclidean color distance value |
| **Contrast** | WCAG contrast ratio with accessibility rating (AAA, AA, AA Large, Fail) |

**Enhanced in v4.0.0** - Now includes LAB color values for each dye.

---

### /accessibility

Colorblind simulation and contrast analysis.

**Usage**: `/accessibility <dye> [dye2] [dye3] [dye4] [vision]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `dye` | string | Yes | Primary dye |
| `dye2-4` | string | No | Additional dyes for contrast matrix |
| `vision` | choice | No | Vision type simulation |

**Vision Types**: protanopia, deuteranopia, tritanopia, achromatopsia

---

### /swatch

Match character colors (eyes, hair, skin) to dyes.

**Usage**: `/swatch <type> <color> [clan] [gender] [count]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `type` | choice | Yes | Color type to match |
| `color` | string | Yes | Hex color to match |
| `clan` | choice | Conditional | Character clan/sub-race (required for skin, hair, eyes) |
| `gender` | choice | Conditional | Character gender (required for skin, hair, eyes) |
| `count` | integer | No | Number of results (1-10, default: 5) |

**Color Types**:

| Type | Clan/Gender Required | Description |
|------|---------------------|-------------|
| `skin` | Yes | Character skin tone |
| `hair` | Yes | Character hair color |
| `eyes` | Yes | Character eye color |
| `lips` | No | Lip color (universal) |
| `tattoo` | No | Tattoo/limbal ring color (universal) |
| `facepaint` | No | Face paint color (universal) |

**Clans (Sub-races)**:

| Race | Clans |
|------|-------|
| Hyur | Midlander, Highlander |
| Miqo'te | Seeker of the Sun, Keeper of the Moon |
| Lalafell | Plainsfolk, Dunesfolk |
| Roegadyn | Sea Wolf, Hellsguard |
| Elezen | Wildwood, Duskwight |
| Au Ra | Raen, Xaela |
| Viera | Rava, Veena |
| Hrothgar | Helions, The Lost |

**Genders**: Male, Female

**Note**: Clan and gender are required for skin, hair, and eyes because FFXIV uses different color palettes for each clan/gender combination. For example, Keeper of the Moon Miqo'te have different skin tones than Seekers of the Sun. Lips, tattoo, and facepaint use universal palettes shared across all races.

**Output**: Matching dyes displayed as V4 Result Cards with Technical data, Acquisition info, and Delta-E match quality.

**New in v4.0.0**

---

## Community

### /preset

Browse and submit community color presets.

**Subcommands**: `list`, `show`, `random`, `submit`, `vote`, `edit`

See `/manual preset` for detailed usage.

---

## Market

### /budget

Find affordable dye alternatives.

**Usage**: `/budget find <target_dye> [world]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `target_dye` | string | Yes | Expensive dye to find alternatives for |
| `world` | string | No | FFXIV world for pricing data |

**Output**: Alternative dyes displayed as V4 Result Cards with Technical data, Acquisition info, Delta-E match quality, and Market Board pricing.

---

## Database

### /dye

Search and explore the FFXIV dye database.

**Subcommands**:

#### /dye search

Search dyes by name.

**Usage**: `/dye search <query>`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Search term (partial name match) |

#### /dye info

Get detailed dye information with a visual Result Card matching the web app style.

**Usage**: `/dye info <name>`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Exact dye name (autocomplete enabled) |

**Result Card Contents**:

| Section | Data | Description |
|---------|------|-------------|
| **Header** | Dye Name | Centered name with category color |
| **Preview** | Color Swatch | Visual color preview |
| **Technical** | HEX | Hex color code (e.g., `#F1A1B2`) |
| | RGB | Red, Green, Blue values (e.g., `241, 161, 178`) |
| | HSV | Hue, Saturation, Value (e.g., `350°, 33%, 95%`) |
| | LAB | CIELAB color space values |
| **Acquisition** | Source | How to obtain (e.g., "Crafting", "Dye Vendor") |
| | Vendor Cost | Gil price from vendor (if applicable) |

**Note**: Market Board data is not included in `/dye info`. Use `/budget` for market pricing.

**Enhanced in v4.0.0** - Now displays a full Result Card with Technical and Acquisition sections.

#### /dye list

List dyes by category.

**Usage**: `/dye list [category]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `category` | choice | No | Filter by category (shows summary if omitted) |

**Categories**: Red, Brown, Yellow, Green, Blue, Purple, White, Black, Grey, Metallic, Special

#### /dye random

Show random dyes for inspiration.

**Usage**: `/dye random [unique]`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `unique` | boolean | No | If true, shows 1 dye per category |

**Output**: Five random dyes displayed as V4 Result Cards with Technical and Acquisition data.

**Enhanced in v4.0.0** - Now generates a visual infographic with Result Cards.

---

## Utility

### /language

Manage language preference.

**Subcommands**:

- `/language set <locale>` - Set language (en, ja, de, fr, ko, zh)
- `/language show` - Show current setting
- `/language reset` - Reset to Discord language

### /manual

Display help documentation.

**Usage**: `/manual [topic]`

### /about

Show bot information and version.

### /stats

View usage statistics (authorized users only).
