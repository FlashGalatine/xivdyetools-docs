# Stoat (formerly Revolt) Bot Platform Research

**Date:** 2026-02-14
**Priority:** First platform to support
**Verdict:** Most Discord-like experience — strong feature parity, requires hosting change

---

## Platform Overview

Stoat (rebranded from Revolt in late 2025) is an open-source, community-driven chat platform designed as a direct Discord alternative. It features servers, channels, roles, permissions, emoji, and bots — the closest 1:1 mapping to Discord's feature set of any alternative. It's free, ad-free, and tracker-free.

- **Website:** [stoat.chat](https://stoat.chat/)
- **Developer Docs:** [developers.stoat.chat](https://developers.stoat.chat/)
- **GitHub:** [github.com/stoatchat](https://github.com/stoatchat)
- **Self-hostable:** Yes — full self-hosted deployment available

---

## Bot API — Deep Dive

### Bot Creation & Authentication
- Bots are created via the Stoat web interface (server settings → Manage bots).
- Each bot gets a **token** (identical concept to Discord bot tokens).
- The `Bot` class exposes an `interactionsUrl` property — an optional HTTP endpoint for receiving interactions. This is analogous to Discord's HTTP interactions model but its maturity is unclear.
- Bots authenticate with header: `x-bot-token: <token>` (via `revolt-api` client) or `x-session-token` for user accounts.

### REST API Endpoints (from OpenAPI spec)

**Bot Management:**
| Method | Endpoint | Description |
|---|---|---|
| POST | `/bots/create` | Create a new bot |
| GET | `/bots/{bot}` | Get owned bot details |
| PATCH | `/bots/{bot}` | Edit bot (name, public, interactionsUrl, etc.) |
| DELETE | `/bots/{bot}` | Delete bot |
| GET | `/bots/@me` | List all bots you own |
| POST | `/bots/{bot}/invite` | Invite bot to a server/group |

**Messaging:**
| Method | Endpoint | Description |
|---|---|---|
| POST | `/channels/{id}/messages` | Send a message |
| GET | `/channels/{id}/messages` | Fetch messages |
| PATCH | `/channels/{id}/messages/{msg}` | Edit a message |
| DELETE | `/channels/{id}/messages/{msg}` | Delete a message |
| POST | `/channels/{id}/search` | Search messages |

**Reactions:**
| Method | Endpoint | Description |
|---|---|---|
| PUT | `/channels/{id}/messages/{msg}/reactions/{emoji}` | Add reaction |
| DELETE | `/channels/{id}/messages/{msg}/reactions/{emoji}` | Remove reaction |

**Webhooks:**
| Method | Endpoint | Description |
|---|---|---|
| POST | `/channels/{id}/webhooks` | Create a webhook for 3rd-party integration |
| GET | `/channels/{id}/webhooks` | List webhooks for a channel |

### Command Model
- **No native slash commands.** Bots respond to message events, typically using prefix commands (e.g., `!dye search white`).
- The `interactionsUrl` property on the Bot class hints at a future HTTP interactions model, but it is currently underdocumented.
- **Practical approach:** Use prefix-based commands (like Discord bots before slash commands existed) and/or message content parsing.

### SendableEmbed Structure
Stoat embeds are simpler than Discord's:
```typescript
interface SendableEmbed {
  title?: string;        // Heading text
  description?: string;  // Body text (Markdown supported)
  url?: string;          // Hyperlink on title
  icon_url?: string;     // Small icon image
  colour?: string;       // Any valid CSS color (e.g., "#FF5733")
  media?: string;        // File ID from upload — renders as image
}
```

**What's missing vs. Discord embeds:**
- No `fields` array (key-value pairs) — must format as Markdown in `description`
- No `footer` — append to description
- No `author` section — use `icon_url` + title
- No `thumbnail` — only `media` (full-width image)
- No `timestamp`

**Key advantage:** The `media` field accepts a file ID directly in the embed, meaning you can show an image *inside* the embed without a separate attachment reference. This is actually cleaner than Discord's `attachment://` pattern.

### Image / File Upload Pipeline

Files are uploaded to Stoat's **Autumn** CDN service in a two-step process:

```typescript
// Step 1: Upload file to Autumn CDN
// POST to the Autumn upload URL (obtained from API's root / endpoint → features.autumn.url)
const formData = new FormData();
formData.append("file", pngBuffer, { filename: "result.png", contentType: "image/png" });
const uploadResponse = await fetch(`${autumnUrl}/attachments`, {
  method: "POST",
  body: formData
});
const { id: fileId } = await uploadResponse.json();

// Step 2: Send message with attachment
await api.post(`/channels/${channelId}/messages`, {
  content: "Here's your dye result!",
  attachments: [fileId],       // Array of file IDs
  embeds: [{
    title: "Harmony Wheel — Triadic",
    description: "Based on **Pure White**\nHEX: `#FFFFFF`",
    colour: "#FFFFFF",
    media: fileId              // OR embed the image directly in the embed
  }]
});
```

**Using revolt-uploader (convenience library):**
```typescript
import { Uploader } from "revolt-uploader";
const uploader = new Uploader(client);

// Upload from buffer, file path, or URL
const attachmentId = await uploader.uploadFile("/path/to/image.png", "result.png");
const attachmentId = await uploader.uploadUrl("https://example.com/image.png", "result.png");
const attachmentId = await uploader.upload(bufferOrStream, "result.png");

// Send with message
channel.sendMessage({
  content: "Result:",
  attachments: [attachmentId]
});
```

**Note:** The Autumn CDN URL recently changed. Bots should dynamically fetch it from the API root endpoint's `features.autumn.url` field rather than hardcoding it.

### Message Sending — Full Parameter Reference
```typescript
// POST /channels/{id}/messages
{
  content?: string;                    // Text content
  attachments?: string[];              // Array of Autumn file IDs
  embed?: SendableEmbed;               // Single embed
  embeds?: SendableEmbed[];            // Multiple embeds
  replies?: MessageReply[];            // Reply references
  masquerade?: {                       // Override display name/avatar
    name?: string;
    avatar?: string;
  };
  interactions?: {                     // Preset reactions
    reactions?: string[];              // Emoji to add as reactions
    restrict_reactions?: boolean;      // Only allow preset reactions
  };
}
```

### Masquerade Feature
Stoat has a unique **masquerade** feature that lets bots change their display name and avatar per-message. This could be useful for visual variety (e.g., showing a dye color as the avatar).

---

## Architecture & Hosting

### Connection Model: WebSocket Required
The primary bot model uses **WebSocket** to connect to Stoat's real-time gateway. revolt.js (the official library) maintains a persistent WebSocket connection:

```typescript
import { Client } from "revolt.js";

const client = new Client();

client.on("ready", () => {
  console.log(`Logged in as ${client.user.username}`);
});

client.on("messageCreate", async (message) => {
  if (message.content === "!ping") {
    await message.channel.sendMessage("Pong!");
  }
});

client.loginBot("your-bot-token");
```

**This means Cloudflare Workers alone won't work** — Workers can't maintain persistent WebSocket connections.

### Hosting Options

| Option | Pros | Cons | Cost |
|---|---|---|---|
| **Fly.io** | Easy deploy, auto-sleep, scales to zero | Cold starts on wake | Free tier available, ~$3-5/mo |
| **Railway** | Git-push deploy, simple | No sleep-to-zero on free tier | $5/mo hobby plan |
| **VPS (Hetzner/OVH)** | Full control, always-on | Manual setup, maintenance | ~$4-5/mo |
| **Home server / Raspberry Pi** | Free, full control | Uptime depends on you | $0 (hardware cost) |
| **Hybrid (VPS + CF Worker)** | Keeps heavy WASM on CF | More complexity | $4-5 + CF free tier |

### Recommended Architecture

**Option A: All-in-one Node.js process (Simplest)**
```
revolt.js WebSocket → Node.js process (Fly.io / Railway / VPS)
  → @xivdyetools/core (dye data, color math)
  → @resvg/resvg-js (Node.js native, NOT wasm)
  → Upload PNG to Autumn CDN
  → Send message via REST API
```
- Use `@resvg/resvg-js` (native Node.js binding) instead of `@resvg/resvg-wasm` — faster, no WASM constraints.
- Import `@xivdyetools/core` directly as an npm dependency.
- All logic in one process — simpler deployment and debugging.

**Option B: Hybrid (if keeping CF Worker infrastructure)**
```
revolt.js WebSocket → Thin listener (Fly.io, ~128MB RAM)
  → POST command to CF Worker endpoint
  → CF Worker processes with WASM pipeline
  → Returns PNG buffer
  → Listener uploads to Autumn CDN → sends message
```
- Keeps the existing WASM rendering pipeline on CF Workers.
- Thin WebSocket listener only handles event routing.
- More complex but reuses existing infrastructure.

**Recommendation:** Start with **Option A**. It's simpler, and the Node.js resvg binding is actually faster than the WASM version. We can always extract shared logic later if we also build a Telegram worker.

---

## Migration Plan

### What Can Be Reused Directly (85%+)
- All `@xivdyetools/core` logic (dye database, color math, matching algorithms)
- All SVG generator templates (`harmony-wheel.ts`, `gradient.ts`, `palette-grid.ts`, etc.)
- Universalis client (direct HTTP, or keep the proxy worker)
- Preset API client
- Font files and subsetting

### What Changes
| Component | Effort | Details |
|---|---|---|
| **Bot framework** | Medium | revolt.js WebSocket client replaces Discord HTTP interactions |
| **Command parser** | Medium | Prefix commands (`!harmony`, `!dye search`) instead of slash commands |
| **Response formatter** | Medium | Discord embeds → Stoat SendableEmbed (simpler structure) |
| **File upload** | Low-Medium | FormData to Autumn CDN + attachment ID, or revolt-uploader |
| **PNG rendering** | Low | Swap `@resvg/resvg-wasm` → `@resvg/resvg-js` (Node.js native) |
| **Image processing** | Low | Swap `@cf-wasm/photon` → `@aspect-build/photon` or sharp |
| **Storage** | Medium | CF KV → SQLite (better-sqlite3) or Redis for preferences |
| **Hosting/deployment** | Medium | wrangler → Dockerfile + Fly.io/Railway |
| **Autocomplete** | N/A | Not available — users type full command arguments |
| **Ephemeral messages** | Low | Not available — use DMs or timed self-deleting messages |
| **Button components** | Low | Not available — provide values inline in the embed description |

### Estimated Effort: **3-4 weeks**

### Suggested Implementation Order
1. Scaffold project, set up revolt.js with bot token, connect to test server
2. Implement `!ping` → verify message send/receive pipeline
3. Port `!dye info` — tests embed formatting + image upload pipeline end-to-end
4. Port remaining image commands (harmony, gradient, comparison, etc.)
5. Port text-only commands (mixer, extractor color, swatch)
6. Port budget commands (Universalis integration)
7. Port preferences system (new storage backend)
8. Port preset commands (Preset API integration)

---

## Available Libraries

### Official
| Package | Type | Description |
|---|---|---|
| `revolt.js` | npm | Official JS/TS library — WebSocket client, full API coverage |
| `revolt-api` | npm | Low-level typed REST API client + OpenAPI types |

### Community
| Package | Type | Description |
|---|---|---|
| `revolt-uploader` | npm | File upload utility (fills gap in revolt.js) |
| `revoltx` | npm | Framework with @sapphire/framework-style argument parsing |
| `revkit` | npm | Class-oriented library with voice + command handler |

### Recommendation
- Use **`revolt.js`** for the WebSocket connection and event handling.
- Use **`revolt-uploader`** or direct Autumn CDN calls for file uploads.
- Use **`revolt-api`** types for type safety on REST calls.

---

## Pros & Cons

### Pros
- **Most Discord-like experience** — servers, channels, roles, embeds, bots — users feel at home
- **Open source** — no vendor lock-in, full code transparency
- **Self-hostable** — can run your own instance if needed
- **No ID verification or tracking** — privacy-first, no face scans
- **Growing community** — Discord exodus is driving rapid growth (Feb 2026)
- **Gaming-friendly culture** — unlike enterprise tools (Slack, Rocket.Chat)
- **Embeds with inline media** — `media` field in embeds is cleaner than Discord's attachment pattern
- **Masquerade feature** — unique ability to change bot appearance per-message
- **Active development** — monorepo approach, regular updates
- **TypeScript-first** — official library and API client are TypeScript
- **Multiple embeds per message** — can show several dye results in one response

### Cons
- **Smaller user base** — growing but still much smaller than Discord/Telegram
- **WebSocket required** — can't use Cloudflare Workers alone, need a persistent host
- **No slash commands** — must use prefix commands (like pre-2021 Discord bots)
- **No autocomplete** — users must type dye names without suggestions (biggest UX gap)
- **Simpler embeds** — no fields, footer, author, or timestamp
- **No ephemeral messages** — can't send user-only responses
- **No button components** — can't replicate copy-value buttons
- **Evolving API surface** — CDN migration, rebrand suggest ongoing changes
- **Smaller ecosystem** — fewer libraries, examples, and community resources
- **revolt-uploader needed** — revolt.js doesn't handle file uploads natively

---

## Key Resources

### Documentation
- [Stoat Developer Documentation](https://developers.stoat.chat/)
- [Stoat API Reference](https://developers.stoat.chat/developers/api/reference.html/) (redirects to OpenAPI viewer)
- [Stoat File Uploads](https://developers.stoat.chat/developers/api/uploading-files.html/)
- [Protocol Reference](https://developers.stoat.chat/developers/events/protocol.html)

### Libraries
- [revolt.js — Official TypeScript library](https://revolt.js.org/) | [GitHub](https://github.com/revoltchat/revolt.js) | [npm](https://www.npmjs.com/package/revolt.js)
- [revolt-api — Typed REST client + OpenAPI types](https://www.npmjs.com/package/revolt-api) | [GitHub](https://github.com/stoatchat/javascript-client-api)
- [revolt-uploader — File upload utility](https://github.com/ShadowLp174/revolt-uploader) | [npm](https://www.npmjs.com/package/revolt-uploader)
- [awesome-stoat — Community libraries, bots, tools](https://github.com/stoatchat/awesome-stoat)
- [revoltx — Sapphire-style framework](https://github.com/kaname-png/revoltx)

### Examples & Guides
- [How to Make a Bot Using Revolt.js](https://github.com/BoQsc/How-to-Make-a-bot-using-Revolt.js/)
- [Revolt.py API Reference](https://revoltpy.readthedocs.io/en/stable/api.html) (Python, but shows API shape clearly)
- [Stoat Self-Hosted Deployment](https://github.com/stoatchat/self-hosted)
- [OpenAPI Spec (JSON)](https://github.com/revoltchat/api/blob/main/OpenAPI.json)
