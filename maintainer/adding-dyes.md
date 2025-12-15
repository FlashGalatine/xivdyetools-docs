# Adding New Dyes

**How to update the dye database when FFXIV releases new dyes**

> This guide covers the process for adding new dyes after a FFXIV patch introduces them.

---

## Overview

When Square Enix releases a new FFXIV patch that includes new dyes, the `xivdyetools-core` library needs to be updated with:

1. **Item ID** - The unique identifier for the dye item
2. **Localized Names** - Names in all 6 supported languages
3. **Color Values** - RGB/HSV values for the actual dye color
4. **Acquisition Data** - How to obtain the dye (vendor, crafting, etc.)

---

## Step 1: Get Item IDs

### Method A: Scraping Universalis (Bulk)

Best for discovering all dyes in a category:

1. Go to [universalis.app](https://universalis.app)
2. Click on **"Market"**
3. Click on the **Dye bucket icon** (Dyes category)
4. Right-click on **"Dyes - X items"** and choose **"Inspect"**
5. Look for `<div class="market-category">` and copy Inner HTML
6. Un-minify the code
7. Extract the Item IDs from the list
8. **Repeat for each language** to get localized names

### Method B: XIVAPI Search (Individual)

Best for adding a specific dye by name:

**JavaScript (using ofetch):**
```javascript
import { ofetch } from 'ofetch'

const response = await ofetch('https://v2.xivapi.com/api/search', {
  query: {
    query: 'Name="Soot Black Dye"',
    sheets: 'Item',
    language: 'en'
  }
})

console.log(response.results[0].row_id) // Item ID
```

**cURL:**
```bash
curl 'https://v2.xivapi.com/api/search?query=Name%3D%22Soot%20Black%20Dye%22&sheets=Item&language=en'
```

**Example Response:**
```json
{
  "schema": "exdschema@2:rev:6a5085f56918e526c457fd3e9dfd27d3572c72a7",
  "version": "3309dd1cf84f989d",
  "results": [
    {
      "score": 1,
      "sheet": "Item",
      "row_id": 5734,
      "fields": {
        "Icon": {
          "id": 22807,
          "path": "ui/icon/022000/022807.tex",
          "path_hr1": "ui/icon/022000/022807_hr1.tex"
        },
        "Name": "Soot Black Dye",
        "Singular": "pot of soot black dye"
      }
    }
  ]
}
```

The `row_id` field is the **Item ID** (e.g., `5734` for Soot Black Dye).

### Getting All Language Names

Query XIVAPI with each language code:

```bash
# English
curl 'https://v2.xivapi.com/api/search?query=Name%3D%22Soot%20Black%20Dye%22&sheets=Item&language=en'

# Japanese
curl 'https://v2.xivapi.com/api/search?query=Name%3D%22スートブラック%22&sheets=Item&language=ja'

# German
curl 'https://v2.xivapi.com/api/search?query=Name%3D%22Rußschwarz%22&sheets=Item&language=de'

# French
curl 'https://v2.xivapi.com/api/search?query=Name%3D%22Noir%20de%20suie%22&sheets=Item&language=fr'
```

---

## Step 2: Get Color Values (RGB/HSV)

**This is the trickiest part** - FFXIV doesn't expose dye RGB values in its data files.

### Method: Visual Color Picking from Lodestone

1. Go to [FFXIV Lodestone](https://na.finalfantasyxiv.com/lodestone/)
2. Search for characters using the new dye on their public profiles
3. Find a character with the dye applied to visible gear
4. Use a color picker tool to sample the color:
   - **Windows**: [PowerToys Color Picker](https://learn.microsoft.com/en-us/windows/powertoys/color-picker) (Win+Shift+C)
   - **macOS**: Digital Color Meter (built-in)
   - **Browser**: Any eyedropper extension
5. Record the **hex code** (e.g., `#2B2B2B`)

### Converting to HSV

Once you have RGB, calculate HSV:

```javascript
function rgbToHsv(r, g, b) {
  r /= 255; g /= 255; b /= 255;
  const max = Math.max(r, g, b), min = Math.min(r, g, b);
  const d = max - min;

  let h = 0;
  if (d !== 0) {
    switch (max) {
      case r: h = ((g - b) / d + (g < b ? 6 : 0)) / 6; break;
      case g: h = ((b - r) / d + 2) / 6; break;
      case b: h = ((r - g) / d + 4) / 6; break;
    }
  }

  return {
    h: Math.round(h * 360),
    s: Math.round((max === 0 ? 0 : d / max) * 100),
    v: Math.round(max * 100)
  };
}
```

Or use the core library:

```typescript
import { ColorService } from 'xivdyetools-core';
const hsv = ColorService.hexToHsv('#2B2B2B');
```

---

## Step 3: Get Acquisition Data

Use [Garland Tools](https://www.garlandtools.org/db/) or in-game resources:

1. Search for the dye by name
2. Note the acquisition method:
   - **Vendor**: NPC name, location, cost, currency
   - **Crafting**: Recipe, materials
   - **Achievement**: Achievement name
   - **Event**: Event name, availability

---

## Step 4: Update the Database

### File Location

```
xivdyetools-core/src/data/colors_xiv.json
```

### Data Structure

```json
{
  "id": 5734,
  "name": {
    "en": "Soot Black",
    "ja": "スートブラック",
    "de": "Rußschwarz",
    "fr": "Noir de suie",
    "ko": "검댕 블랙",
    "zh": "煤黑"
  },
  "hex": "#2B2B2B",
  "rgb": { "r": 43, "g": 43, "b": 43 },
  "hsv": { "h": 0, "s": 0, "v": 17 },
  "category": "black",
  "rarity": "common",
  "source": "vendor"
}
```

### Categories

| Category | Description |
|----------|-------------|
| `white` | White/snow dyes |
| `black` | Black/grey dyes |
| `red` | Red/wine dyes |
| `orange` | Orange dyes |
| `yellow` | Yellow/gold dyes |
| `green` | Green dyes |
| `blue` | Blue dyes |
| `purple` | Purple/violet dyes |
| `brown` | Brown/tan dyes |
| `pink` | Pink dyes |
| `metallic` | Metallic sheen dyes |
| `pastel` | Pastel series |

### Rarity

| Rarity | Description |
|--------|-------------|
| `common` | Vendor-purchasable with gil |
| `uncommon` | Crafted or dungeon drops |
| `rare` | Event-exclusive or achievement |

---

## Step 5: Test and Publish

```bash
cd xivdyetools-core

# Run tests to ensure data integrity
npm test

# Verify the new dye appears in searches
npm test -- --grep "new dye name"

# Build
npm run build

# Bump version
npm version patch

# Publish
npm publish
```

---

## Step 6: Update Consumers

After publishing the core library:

```bash
# Web app
cd ../xivdyetools-web-app
npm update xivdyetools-core
npm run build

# Discord worker
cd ../xivdyetools-discord-worker
npm update xivdyetools-core
npm run deploy:production
```

---

## Checklist for New Dyes

- [ ] Item ID obtained (from Universalis or XIVAPI)
- [ ] All 6 language names collected
- [ ] RGB color sampled from Lodestone
- [ ] HSV calculated from RGB
- [ ] Category assigned
- [ ] Rarity assigned
- [ ] Source documented
- [ ] Added to `colors_xiv.json`
- [ ] Tests pass
- [ ] Core library published
- [ ] Web app updated
- [ ] Discord worker updated

---

## Useful Resources

| Resource | URL | Use |
|----------|-----|-----|
| XIVAPI v2 | https://v2.xivapi.com | Item IDs, names |
| Universalis | https://universalis.app | Market data, item lists |
| Garland Tools | https://www.garlandtools.org/db/ | Acquisition data |
| FFXIV Lodestone | https://na.finalfantasyxiv.com/lodestone/ | Color sampling |
| Teamcraft | https://ffxivteamcraft.com | Recipe data |

---

## Related Documentation

- [Core Library Overview](../projects/core/overview.md)
- [Publishing Guide](../projects/core/publishing.md)
- [Deployment Guide](../developer-guides/deployment.md)
