# 🔒 Security Audit Report: xivdyetools-moderation-worker

**Audit Date:** January 5, 2026  
**Project:** XIV Dye Tools Discord Moderation Bot - Cloudflare Workers  
**Auditor:** GitHub Copilot

---

## Executive Summary

This security audit covers the Cloudflare Worker-based Discord moderation bot that handles content moderation for the XIV Dye Tools Preset Palettes feature. The audit identified **16 security findings** across various severity levels.

**Overall Assessment: ⚠️ NEEDS ATTENTION**

| Severity | Count |
|----------|-------|
| 🔴 Critical | 1 |
| 🟠 High | 4 |
| 🟡 Medium | 6 |
| 🔵 Low | 3 |
| ⚪ Informational | 2 |

---

## Detailed Findings

### 🔴 CRITICAL

#### 1. Overly Permissive CORS Configuration
**File:** `src/index.ts`  
**Severity:** Critical

```typescript
// Enable CORS for development
app.use('*', cors());
```

**Description:** The CORS middleware is enabled globally with default settings (`*` wildcard) and the comment says "for development" - but this appears to be in production code. This allows any origin to make requests to the worker, which is inappropriate for a Discord interaction endpoint.

**Risk:** Discord interaction endpoints don't require CORS (Discord makes server-to-server requests). Enabling permissive CORS could allow:
- Cross-origin requests from malicious sites
- Potential exposure of sensitive data in responses
- Abuse of the health check endpoint

**Recommendation:**
```typescript
// Remove CORS entirely for Discord interactions endpoint, or restrict severely
// Discord interactions don't use CORS - they're server-to-server
// If CORS is needed for specific routes, restrict to known origins:
app.use('/health', cors({ origin: 'https://xivdyetools.com' }));
```

---

### 🟠 HIGH SEVERITY

#### 2. Username Injection in Button custom_id Parsing
**File:** `src/handlers/buttons/ban-confirmation.ts`  
**Severity:** High

```typescript
// Parse custom_id: ban_confirm_{discordId}_{username}
const idPart = customId.replace('ban_confirm_', '');
const underscoreIndex = idPart.indexOf('_');

if (underscoreIndex === -1) {
  return ephemeralResponse('Invalid button data.');
}

const targetUserId = idPart.substring(0, underscoreIndex);
const targetUsername = idPart.substring(underscoreIndex + 1);
```

**Description:** The custom_id parsing only uses the first underscore to split Discord ID and username. If a username contains underscores, this could cause issues. More critically, the username is embedded directly in custom_ids without sanitization.

**Risk:** 
- If an attacker can influence the username (e.g., through preset author names), they could potentially manipulate the parsing
- Usernames with underscores would be parsed incorrectly, possibly leading to ban operations on wrong users

**Recommendation:**
```typescript
// Use a safe delimiter that cannot appear in Discord IDs or encode the username
// Discord IDs are numeric only (snowflakes), so use a non-numeric delimiter
const DELIMITER = '|';
// Or use base64 encoding for the username portion
const targetUsername = Buffer.from(idPart.substring(underscoreIndex + 1), 'base64').toString();
```

---

#### 3. Moderator ID List Stored as Plain Text
**File:** `wrangler.toml`, `src/services/preset-api.ts`  
**Severity:** High

```typescript
export function isModerator(env: Env, userId: string): boolean {
  if (!env.MODERATOR_IDS) return false;
  const moderatorIds = env.MODERATOR_IDS.split(',').map((id) => id.trim());
  return moderatorIds.includes(userId);
}
```

**Description:** Moderator IDs are stored in a comma-separated string environment variable. While marked as a secret, the simple string comparison is case-sensitive and the parsing happens on every request.

**Risk:**
- No validation that moderator IDs are valid Discord snowflakes
- If the string is malformed (extra spaces, invalid characters), moderators may be incorrectly blocked or approved
- No fallback if `MODERATOR_IDS` is empty/undefined beyond returning false

**Recommendation:**
```typescript
// Validate moderator IDs are valid snowflakes at startup
// Cache the parsed Set for performance
let moderatorSet: Set<string> | null = null;

export function isModerator(env: Env, userId: string): boolean {
  if (!moderatorSet) {
    if (!env.MODERATOR_IDS) return false;
    moderatorSet = new Set(
      env.MODERATOR_IDS.split(',')
        .map((id) => id.trim())
        .filter((id) => /^\d{17,19}$/.test(id)) // Validate snowflake format
    );
  }
  return moderatorSet.has(userId);
}
```

---

#### 4. Insufficient Input Validation on Preset IDs
**File:** `src/handlers/commands/preset.ts`, `src/handlers/buttons/preset-moderation.ts`  
**Severity:** High

```typescript
case 'approve': {
  if (!presetId) {
    // Only null/empty check
  }
  const preset = await presetApi.approvePreset(env, presetId, userId, reason);
}
```

**Description:** Preset IDs are extracted from user input (command options and button custom_ids) and passed directly to API calls without format validation. The code only checks for null/empty values.

**Risk:**
- SQL injection potential if the downstream API doesn't properly sanitize
- NoSQL injection if IDs are used in queries
- Path traversal if IDs are used in filesystem operations
- UUID format not enforced (presets use UUIDs)

**Recommendation:**
```typescript
const UUID_REGEX = /^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$/i;

function validatePresetId(presetId: string): boolean {
  return UUID_REGEX.test(presetId);
}

// Before any API call:
if (!presetId || !validatePresetId(presetId)) {
  return ephemeralResponse('Invalid preset ID format.');
}
```

---

#### 5. Error Message Information Disclosure
**File:** `src/handlers/buttons/preset-moderation.ts`, `src/handlers/modals/ban-reason.ts`  
**Severity:** High

```typescript
await editMessage(env.DISCORD_TOKEN, interaction.channel_id, interaction.message.id, {
  embeds: [
    {
      // ...
      fields: [
        ...(originalEmbed.fields || []),
        { name: 'Error', value: `Failed to approve: ${error}`, inline: false },
      ],
    },
  ],
});
```

**Description:** Raw error objects are interpolated directly into user-visible Discord messages. This can expose internal implementation details, stack traces, and potentially sensitive information.

**Risk:**
- Database error messages could reveal table structure
- Stack traces expose file paths and internal logic
- Error messages might contain sensitive data from failed operations

**Recommendation:**
```typescript
// Create a sanitized error message helper
function sanitizeErrorMessage(error: unknown): string {
  if (error instanceof PresetAPIError) {
    // Only expose user-safe messages
    return error.message;
  }
  // Log full error internally but show generic message to user
  return 'An unexpected error occurred. Please try again later.';
}

// Usage:
{ name: 'Error', value: sanitizeErrorMessage(error), inline: false }
```

---

### 🟡 MEDIUM SEVERITY

#### 6. No Rate Limiting Implementation
**File:** `src/index.ts`  
**Severity:** Medium

**Description:** The worker has no rate limiting implementation. While Discord has its own rate limits, the worker could still be abused through:
- Rapid button clicks
- Autocomplete spam
- Health endpoint polling

**Risk:**
- Resource exhaustion attacks
- Database query flooding through autocomplete
- Potential costs on Cloudflare Workers (though capped)

**Recommendation:** Implement per-user rate limiting using KV storage.

---

#### 7. SQL Query Escaping Inconsistency
**File:** `src/services/ban-service.ts`  
**Severity:** Medium

```typescript
const escapedQuery = query.replace(/[%_\\]/g, '\\$&');
```

**Description:** The SQL LIKE clause escaping only handles `%`, `_`, and `\` characters. While this is correct for LIKE patterns, verify LIKE ESCAPE is properly supported with D1.

---

#### 8. Channel Restriction Bypass Potential
**File:** `src/handlers/commands/preset.ts`  
**Severity:** Medium

```typescript
function isInModerationChannel(interaction: DiscordInteraction, env: Env): boolean {
  if (!env.MODERATION_CHANNEL_ID) {
    return false;  // If not configured, all channels are denied
  }
  return interaction.channel_id === env.MODERATION_CHANNEL_ID;
}
```

**Description:** The channel restriction returns `false` when `MODERATION_CHANNEL_ID` is not set. This is safe, but the `moderate` subcommand doesn't check channel restriction at all - only `ban_user` and `unban_user` do.

**Recommendation:** Apply channel restriction to ALL moderation subcommands.

---

#### 9. Missing Request Body Size Validation on JSON Parse
**File:** `src/index.ts`  
**Severity:** Medium

**Description:** While `src/utils/verify.ts` implements a 100KB body size limit, the JSON parsing doesn't have additional protections against deeply nested objects or prototype pollution attacks.

**Recommendation:** Use a safer JSON parser with prototype pollution protection.

---

#### 10. Timestamp Validation Missing in HMAC Signatures
**File:** `src/services/preset-api.ts`  
**Severity:** Medium

**Description:** The code generates HMAC signatures with timestamps but there's no indication the receiving API validates the timestamp is recent. If the receiving end doesn't validate, signed requests could be replayed.

**Recommendation:** Ensure the receiving API validates timestamp is within 5 minutes.

---

#### 11. Webhook Token Exposure in URLs
**File:** `src/utils/discord-api.ts`  
**Severity:** Medium

**Description:** Discord interaction tokens are included in URLs and could appear in logs and error messages.

**Recommendation:** Add URL sanitization in logging middleware.

---

### 🔵 LOW SEVERITY

#### 12. Health Endpoint Information Disclosure
**File:** `src/index.ts`  
**Severity:** Low

**Description:** The health endpoint exposes the service name, confirming the technology stack to potential attackers.

**Recommendation:** Return minimal health response: `{ status: 'ok' }`

---

#### 13. Missing Security Headers
**File:** `src/index.ts`  
**Severity:** Low

**Description:** Good security headers are set, but some additional headers could be beneficial:
- `Cache-Control: no-store`
- `Content-Security-Policy: default-src 'none'`
- `Referrer-Policy: no-referrer`

---

#### 14. Async Error Handling in waitUntil
**File:** `src/handlers/commands/preset.ts`  
**Severity:** Low

**Description:** Errors in `waitUntil` promises don't propagate to the user. Unhandled rejections could occur.

**Recommendation:** Add `.catch()` handler to `waitUntil` calls.

---

### ⚪ INFORMATIONAL

#### 15. Database Credentials in wrangler.toml
**File:** `wrangler.toml`  
**Severity:** Informational

Database IDs are committed to version control. While these are not direct credentials, they provide reconnaissance value.

---

#### 16. Unused Optional Environment Variables
**File:** `src/types/env.ts`  
**Severity:** Informational

`BOT_API_SECRET` and `BOT_SIGNING_SECRET` are marked as optional (`?`), meaning the worker can run without them configured.

**Recommendation:** Make security-critical secrets required or validate at startup.

---

## Summary of Recommendations

### Immediate Actions (Critical/High):
1. ✅ Remove or restrict CORS configuration
2. ✅ Fix username parsing in button custom_ids using safe delimiters
3. ✅ Add UUID validation for all preset IDs
4. ✅ Sanitize error messages before showing to users
5. ✅ Validate moderator ID format

### Short-term Actions (Medium):
1. Implement rate limiting
2. Apply channel restrictions to all moderation commands
3. Add JSON parsing safety measures
4. Verify HMAC timestamp validation on API side
5. Audit SQL escaping consistency

### Long-term Actions (Low/Informational):
1. Minimize health endpoint response
2. Add additional security headers
3. Improve error handling in waitUntil
4. Consider making security secrets required

---

## Resolutions

**Resolution Date:** January 05, 2026
**Resolved By:** Claude Code (Automated Security Fixes)

The following CRITICAL and HIGH severity issues have been resolved:

### ✅ Issue #1: Overly Permissive CORS Configuration (CRITICAL)
**Status:** RESOLVED
**Fix Applied:** Removed CORS middleware entirely from src/index.ts (lines 16, 47)
**Files Modified:**
- src/index.ts - Removed `import { cors } from 'hono/cors'` and `app.use('*', cors())`

**Verification:** Discord interactions work correctly without CORS headers (server-to-server)

**Rationale:** Discord's server-to-server POST requests don't require CORS headers. The worker doesn't serve any browser-based clients, so CORS is unnecessary and posed a security risk.

---

### ✅ Issue #2: Username Injection in Button custom_id Parsing (HIGH)
**Status:** RESOLVED
**Fix Applied:** Implemented base64url encoding/decoding for usernames in custom_ids
**Files Modified:**
- src/utils/response.ts - Added `encodeBase64Url()` and `decodeBase64Url()` utility functions
- src/handlers/commands/preset.ts - Encode username in ban confirmation button creation
- src/handlers/buttons/ban-confirmation.ts - Decode username when parsing button data and re-encode for modal
- src/handlers/modals/ban-reason.ts - Decode username when parsing modal submission

**Verification:** Usernames with underscores and special characters now handled correctly

**Technical Details:** Base64url encoding ensures usernames containing underscores don't break the parsing logic that uses underscores as delimiters. The URL-safe variant prevents issues with Discord's custom_id constraints.

---

### ✅ Issue #3: Moderator ID List Stored as Plain Text (HIGH)
**Status:** RESOLVED
**Fix Applied:** Implemented cached Set with Discord snowflake validation
**Files Modified:**
- src/services/preset-api.ts - Refactored `isModerator()` function with validation and module-level caching

**Verification:** Invalid moderator IDs are logged and rejected, O(1) lookup performance achieved

**Technical Details:**
- Added `isValidDiscordSnowflake()` to validate 17-19 digit format
- Added `getModerators()` with module-level cache for performance
- Invalid IDs generate console warnings but don't break functionality
- Cache is naturally refreshed when Workers restart

---

### ✅ Issue #4: Insufficient Input Validation on Preset IDs (HIGH)
**Status:** RESOLVED
**Fix Applied:** Added UUID v4 format validation for all preset IDs
**Files Modified:**
- src/utils/response.ts - Added `isValidUuid()` utility function with UUID v4 regex
- src/handlers/commands/preset.ts - Validate preset IDs in approve and reject command flows
- src/handlers/buttons/preset-moderation.ts - Validate preset IDs in all three button handlers (approve, reject, revert)

**Verification:** Non-UUID inputs are rejected before API calls with user-friendly error messages

**Technical Details:** UUID v4 regex validates format: `xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx` where `y` is one of [89ab]. This prevents SQL injection, NoSQL injection, and path traversal attacks.

---

### ✅ Issue #5: Error Message Information Disclosure (HIGH)
**Status:** RESOLVED
**Fix Applied:** Created error sanitization helper to filter unsafe error messages
**Files Modified:**
- src/utils/response.ts - Added `sanitizeErrorMessage()` utility function
- src/handlers/buttons/preset-moderation.ts - Sanitize approval errors
- src/handlers/modals/preset-rejection.ts - Sanitize rejection and revert errors
- src/handlers/modals/ban-reason.ts - Sanitize ban processing errors

**Verification:** Internal details (stack traces, file paths, SQL queries) no longer exposed to users

**Technical Details:** The sanitizer detects unsafe patterns (stack traces, file paths, SQL keywords, environment variables) and returns generic fallback messages. PresetAPIError 4xx messages are allowed as they're user-safe, but 5xx errors are sanitized.

---

### Remaining Issues
Medium, Low, and Informational severity issues will be addressed in subsequent security improvement sessions.

---

## Positive Security Practices Noted

1. ✅ **Ed25519 signature verification** - Proper Discord request verification using `discord-interactions` library
2. ✅ **Request body size limits** - 100KB limit prevents large payload attacks
3. ✅ **Timing-safe comparison** - `timingSafeEqual` function available for constant-time comparisons
4. ✅ **HMAC request signing** - Requests to internal APIs are signed
5. ✅ **Moderator permission checks** - All sensitive actions verify moderator status
6. ✅ **Structured logging** - Request correlation IDs for audit trails
7. ✅ **Security headers** - HSTS, X-Frame-Options, and X-Content-Type-Options present
8. ✅ **Service bindings** - Worker-to-worker communication doesn't expose internal APIs
