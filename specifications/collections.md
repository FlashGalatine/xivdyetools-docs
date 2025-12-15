# Collections System

**Organize favorite dyes into named groups**

> Status: ✅ Implemented (December 2025)
> Platforms: Web App + Discord Bot

---

## Overview

Collections allow users to organize their favorite dyes into named groups. Unlike single favorites, collections provide a way to group related dyes together (e.g., "Tank Glamour", "Beach House", "Healer Set").

### User Value

- **Organization** - Group dyes by purpose, project, or theme
- **Multiple sets** - Maintain separate collections for different characters/projects
- **Cross-reference** - Same dye can be in multiple collections
- **Persistence** - Collections saved across sessions

---

## Features

### Create Collection

**Web App:**
1. Click "Collections" in sidebar
2. Click "New Collection"
3. Enter name (3-50 characters)
4. Optionally add description

**Discord:**
```
/collection create name:Tank Glamour
```

### Add Dyes to Collection

**Web App:**
- Click heart icon on any dye
- Select "Add to Collection"
- Choose collection

**Discord:**
```
/collection add collection:Tank Glamour dye:Dalamud Red
```

### View Collection

**Web App:**
- Click collection name in sidebar
- See all dyes with swatches

**Discord:**
```
/collection show name:Tank Glamour
```

### Remove Dye from Collection

**Web App:**
- Open collection
- Click remove icon on dye

**Discord:**
```
/collection remove collection:Tank Glamour dye:Dalamud Red
```

### Delete Collection

**Web App:**
- Open collection settings
- Click "Delete Collection"

**Discord:**
```
/collection delete name:Tank Glamour
```

---

## Limits

| Limit | Value | Reason |
|-------|-------|--------|
| Max collections per user | 50 | Storage constraints |
| Max dyes per collection | 20 | UI/UX practicality |
| Collection name length | 3-50 chars | Display constraints |
| Description length | 0-200 chars | Optional |

---

## Storage

### Web App (localStorage)

```typescript
interface StoredCollections {
  collections: Collection[];
  version: number;
  lastUpdated: string;
}

interface Collection {
  id: string;
  name: string;
  description?: string;
  dyeIds: number[];
  createdAt: string;
  updatedAt: string;
}

// Key: 'xivdye:collections'
localStorage.setItem('xivdye:collections', JSON.stringify(data));
```

### Discord Bot (Cloudflare KV)

```typescript
// Key format: xivdye:collections:{userId}
// Value: JSON string of Collection[]

const key = `xivdye:collections:${userId}`;
await env.KV.put(key, JSON.stringify(collections));
```

---

## Data Model

```typescript
interface Collection {
  id: string;           // UUID
  name: string;         // User-provided name
  description?: string; // Optional description
  dyeIds: DyeId[];      // Array of dye IDs
  createdAt: string;    // ISO timestamp
  updatedAt: string;    // ISO timestamp
}
```

---

## API (Discord Bot)

### Commands

| Command | Description |
|---------|-------------|
| `/collection create name:` | Create new collection |
| `/collection list` | List all collections |
| `/collection show name:` | View collection contents |
| `/collection add collection: dye:` | Add dye to collection |
| `/collection remove collection: dye:` | Remove dye from collection |
| `/collection delete name:` | Delete collection |

### Autocomplete

- Collection names autocomplete from user's existing collections
- Dye names autocomplete from dye database

---

## Implementation Notes

### Web App

- Uses StorageService for localStorage abstraction
- Reactive updates via Lit's reactive properties
- Collection selector component reused across tools

### Discord Bot

- KV operations are async
- Commands use deferred responses for consistency
- Autocomplete fetches from KV for collection names

### Sync Considerations

Collections are stored separately on web and Discord:
- **Web**: localStorage (per browser)
- **Discord**: KV (per user)

Future consideration: Server-side sync via presets-api authenticated endpoints.

---

## Related Documentation

- [Web App Overview](../projects/web-app/overview.md)
- [Discord Worker Overview](../projects/discord-worker/overview.md)
- [User Guides: Favorites & Collections](../user-guides/web-app/favorites-collections.md)
