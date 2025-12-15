# Community Presets System

**User-submitted dye palettes with voting and moderation**

> Status: ✅ Implemented (December 2025)
> Platforms: Web App + Discord Bot + Cloudflare Worker API

---

## Overview

An automated community submission system for preset palettes with centralized storage, duplicate detection, voting, and content moderation.

### User Value

- **Community-driven content** - Users contribute palettes for others to discover
- **No duplicates** - Submitting an existing palette automatically votes for it
- **Quality through voting** - Popular palettes rise to the top
- **Cross-platform** - Same presets available on web app and Discord bot
- **Instant publishing** - Auto-moderation enables immediate visibility

---

## Architecture

```
┌─────────────────┐     ┌──────────────────────────┐     ┌─────────────────┐
│  Discord Bot    │────▶│  xivdyetools-presets-api │◀────│    Web App      │
│                 │     │  (Cloudflare Worker + D1) │     │                 │
│ /preset submit  │     │                          │     │ Preset Browser  │
│ /preset vote    │     │  GET  /api/v1/presets    │     │                 │
│ /preset list    │     │  POST /api/v1/presets    │     │                 │
└─────────────────┘     │  POST /api/v1/votes/:id  │     └─────────────────┘
                        │  PATCH /moderation/:id   │
                        └──────────────────────────┘
```

---

## Key Features

### 1. Preset Submission

Users can submit presets via:
- **Discord**: `/preset submit` command (opens modal)
- **Web App**: Preset Browser submit button

**Submission Data:**
- Name (3-50 characters)
- Description (optional, max 200 characters)
- 2-5 dye selections
- Category selection

### 2. Duplicate Detection

Before creating a new preset:
1. Generate `dye_signature` (sorted dye IDs joined)
2. Check for existing preset with same signature
3. If duplicate exists, auto-vote for it instead

### 3. Voting System

- Upvote or downvote presets
- One vote per user per preset
- Vote counts displayed on preset cards
- Sort by popularity (upvotes - downvotes)

### 4. Content Moderation

Three-layer moderation pipeline:

1. **Local Profanity Filter** (instant)
   - Word lists in 6 languages
   - Pattern matching for variations

2. **Perspective API** (optional)
   - ML-based toxicity detection
   - Requires Google API key

3. **Manual Review**
   - Moderator approval queue
   - Approve/reject with reason

### 5. Rate Limiting

- 10 submissions per user per day (UTC)
- Tracked via database query
- Prevents spam while allowing active contributors

---

## Database Schema

```sql
CREATE TABLE presets (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  description TEXT,
  dye_signature TEXT NOT NULL,
  colors TEXT NOT NULL,          -- JSON array
  category_id INTEGER NOT NULL,
  author_discord_id TEXT NOT NULL,
  author_discord_name TEXT NOT NULL,
  upvotes INTEGER DEFAULT 0,
  downvotes INTEGER DEFAULT 0,
  status TEXT DEFAULT 'pending', -- pending, approved, rejected
  is_curated INTEGER DEFAULT 0,
  created_at TEXT DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE votes (
  id TEXT PRIMARY KEY,
  preset_id TEXT NOT NULL,
  user_discord_id TEXT NOT NULL,
  vote TEXT NOT NULL,            -- 'up' or 'down'
  UNIQUE(preset_id, user_discord_id)
);

CREATE TABLE moderation_log (
  id TEXT PRIMARY KEY,
  preset_id TEXT NOT NULL,
  action TEXT NOT NULL,
  moderator_discord_id TEXT NOT NULL,
  reason TEXT,
  created_at TEXT DEFAULT CURRENT_TIMESTAMP
);
```

---

## API Endpoints

See [API Contracts](../architecture/api-contracts.md) for full specification.

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/api/v1/presets` | GET | No | List presets |
| `/api/v1/presets` | POST | Yes | Submit preset |
| `/api/v1/presets/:id` | GET | No | Get single preset |
| `/api/v1/presets/mine` | GET | Yes | User's presets |
| `/api/v1/votes/:id` | POST | Yes | Vote on preset |
| `/api/v1/votes/:id` | DELETE | Yes | Remove vote |
| `/api/v1/moderation/pending` | GET | Mod | Review queue |
| `/api/v1/moderation/:id/status` | PATCH | Mod | Approve/reject |

---

## Implementation Details

### Dye Signature Generation

```typescript
function generateDyeSignature(dyeIds: number[]): string {
  return [...dyeIds].sort((a, b) => a - b).join('-');
}

// Example: [42, 12, 78] → "12-42-78"
```

### Category Mapping

| ID | Name | Slug |
|----|------|------|
| 1 | Glamour | glamour |
| 2 | Housing | housing |
| 3 | Roleplay | roleplay |
| 4 | Seasonal | seasonal |
| 5 | Other | other |

---

## Related Documentation

- [Presets API Overview](../projects/presets-api/overview.md)
- [API Contracts](../architecture/api-contracts.md)
- [Data Flow](../architecture/data-flow.md#preset-submission-flow)
