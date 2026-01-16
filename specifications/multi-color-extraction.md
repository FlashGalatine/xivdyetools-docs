# Multi-Color Palette Extraction

**Extract dominant colors from images using K-means++ clustering**

> Status: ✅ Implemented (December 2025)
> Platforms: Web App + Discord Bot
> Core Library: `PaletteService`

---

## Overview

Extract the dominant colors from any image and match each to the closest FFXIV dye. Uses K-means++ clustering for accurate, reproducible results.

### User Value

- **Reference matching** - Upload a screenshot or reference image
- **Outfit planning** - Extract colors from inspiration images
- **Palette discovery** - Find harmonious colors from artwork
- **Cross-platform** - Works on web and Discord

---

## How It Works

```
Image Input → Sample Pixels → K-means++ Clustering → Dominant Colors → Dye Matching
```

### Step 1: Image Input

**Web App:**
- File upload (drag & drop or click)
- Paste from clipboard
- URL input (CORS permitting)

**Discord Bot:**
- Image attachment to `/match_image` command
- URL in command options

### Step 2: Pixel Sampling

To improve performance, not all pixels are analyzed:

| Quality | Sample Rate | Typical Time |
|---------|-------------|--------------|
| Low | 1/100 pixels | ~10ms |
| Medium | 1/25 pixels | ~50ms |
| High | 1/4 pixels | ~200ms |

### Step 3: K-means++ Clustering

K-means++ improves on standard K-means with smarter initialization:

1. **First centroid**: Random pixel
2. **Subsequent centroids**: Weighted probability based on distance to nearest existing centroid
3. **Iteration**: Assign pixels to nearest centroid, recalculate centroids
4. **Convergence**: Stop when centroids stabilize

### Step 4: Dye Matching

Each extracted color is matched to the closest FFXIV dye using:
- RGB Euclidean distance (fast)
- CIE deltaE (perceptual accuracy)

---

## API

### Core Library

```typescript
import { PaletteService } from '@xivdyetools/core';

const palette = await PaletteService.extractPalette(imageData, {
  numColors: 5,           // Number of colors to extract (2-10)
  quality: 'medium',      // 'low' | 'medium' | 'high'
  maxIterations: 100      // K-means max iterations
});

// Returns: HexColor[] - Array of hex color strings
```

### Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `numColors` | number | 5 | Colors to extract (2-10) |
| `quality` | string | 'medium' | Sampling quality |
| `maxIterations` | number | 100 | K-means iterations |

---

## Web App Implementation

```typescript
// In Color Matcher component

async function handleImageUpload(file: File) {
  // Load image to canvas
  const img = await loadImage(file);
  const canvas = document.createElement('canvas');
  const ctx = canvas.getContext('2d');
  ctx.drawImage(img, 0, 0);

  // Get ImageData
  const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

  // Extract palette
  const palette = await PaletteService.extractPalette(imageData, {
    numColors: 5,
    quality: 'medium'
  });

  // Match each color to dye
  const matches = palette.map(hex => dyeService.findClosestDye(hex));

  // Display results
  this.paletteResults = matches;
}
```

---

## Discord Bot Implementation

Uses `@cf-wasm/photon` for image processing in Workers:

```typescript
import { PhotonImage } from '@cf-wasm/photon';

async function handleMatchImage(
  interaction: DiscordInteraction,
  env: Env
) {
  // Get image attachment
  const attachment = interaction.data.resolved.attachments[0];
  const imageBuffer = await fetch(attachment.url).then(r => r.arrayBuffer());

  // Load with Photon
  const image = PhotonImage.new_from_byteslice(new Uint8Array(imageBuffer));

  // Get pixel data
  const pixels = image.get_raw_pixels();
  const width = image.get_width();
  const height = image.get_height();

  // Create ImageData-like object
  const imageData = { data: pixels, width, height };

  // Extract palette
  const palette = await PaletteService.extractPalette(imageData, {
    numColors: 5,
    quality: 'low'  // Lower quality for faster response
  });

  // Match and respond...
}
```

---

## K-means++ Algorithm Details

### Initialization (K-means++)

```typescript
function initializeCentroids(
  pixels: RGB[],
  k: number
): RGB[] {
  const centroids: RGB[] = [];

  // First centroid: random
  centroids.push(pixels[Math.floor(Math.random() * pixels.length)]);

  // Remaining centroids: distance-weighted probability
  for (let i = 1; i < k; i++) {
    const distances = pixels.map(p =>
      Math.min(...centroids.map(c => rgbDistance(p, c)))
    );
    const totalDistance = distances.reduce((a, b) => a + b, 0);

    // Weighted random selection
    let random = Math.random() * totalDistance;
    for (let j = 0; j < pixels.length; j++) {
      random -= distances[j];
      if (random <= 0) {
        centroids.push(pixels[j]);
        break;
      }
    }
  }

  return centroids;
}
```

### Convergence

```typescript
function centroidsConverged(
  old: RGB[],
  new_: RGB[],
  threshold: number = 1
): boolean {
  return old.every((o, i) =>
    rgbDistance(o, new_[i]) < threshold
  );
}
```

---

## Performance Considerations

### Web App

- Large images resized before processing
- Web Workers can be used for non-blocking extraction
- Canvas operations are hardware-accelerated

### Discord Bot

- Lower quality setting for faster responses
- Image size limits enforced by Discord (8MB/25MB)
- Deferred responses allow up to 15 minutes processing

---

## Related Documentation

- [Core Library: Algorithms](../projects/core/algorithms.md)
- [Palette Extractor Tool Guide](../user-guides/web-app/palette-extractor.md)
- [Discord Bot Commands](../projects/discord-worker/commands.md)
