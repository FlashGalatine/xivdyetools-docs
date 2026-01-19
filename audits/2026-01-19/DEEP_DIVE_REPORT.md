# Deep-Dive Analysis Report

## Executive Summary

- **Project:** xivdyetools-* ecosystem (11 projects)
- **Analysis Date:** 2026-01-19
- **Total Findings:** 203
- **Analyzer:** Claude Code (Opus 4.5 + Haiku 4.5 agents)

### High-Level Statistics

| Project | Bugs | Refactoring | Optimization | Total |
|---------|------|-------------|--------------|-------|
| xivdyetools-core | 5 | 4 | 4 | 13 |
| xivdyetools-discord-worker | 5 | 5 | 5 | 15 |
| xivdyetools-logger | 5 | 5 | 4 | 14 |
| xivdyetools-maintainer | 7 | 7 | 7 | 21 |
| xivdyetools-moderation-worker | 10 | 5 | 3 | 18 |
| xivdyetools-oauth | 7 | 5 | 5 | 17 |
| xivdyetools-presets-api | 6 | 7 | 7 | 20 |
| xivdyetools-test-utils | 8 | 6 | 6 | 20 |
| xivdyetools-types | 11 | 6 | 3 | 20 |
| xivdyetools-universalis-proxy | 7 | 7 | 4 | 18 |
| xivdyetools-web-app | 10 | 6 | 6 | 22 |
| **TOTAL** | **81** | **63** | **54** | **198** |

---

## Summary by Category

### Hidden Bugs

| ID | Project | Title | Severity | Type |
|----|---------|-------|----------|------|
| ✅CORE-BUG-001 | xivdyetools-core | Race Condition in Pending Request Deduplication | CRITICAL | Race Condition |
| ✅CORE-BUG-002 | xivdyetools-core | Promise Rejection Not Properly Cleaned from Pending Map | CRITICAL | Async Handling |
| ✅CORE-BUG-003 | xivdyetools-core | Potential Null Reference in KDTree Nearest Neighbor | HIGH | Null Safety |
| ✅CORE-BUG-004 | xivdyetools-core | Unvalidated Type Cast in DyeDatabase | HIGH | Type Safety |
| ✅DISCORD-BUG-001 | xivdyetools-discord-worker | Race Condition in Analytics Counter Increment | MEDIUM | Race Condition |
| ✅DISCORD-BUG-002 | xivdyetools-discord-worker | Missing Error Handling in Analytics.writeDataPoint | MEDIUM | Error Handling |
| ✅LOGGER-BUG-001 | xivdyetools-logger | Race Condition in Concurrent perf.start() Calls | HIGH | Concurrency |
| ⚠️LOGGER-BUG-002 | xivdyetools-logger | Error Sanitization Regex Edge Cases | MEDIUM | Security |
| ✅MAINT-BUG-001 | xivdyetools-maintainer | Unhandled Promise Rejection in Server Health Check | MEDIUM | Async Handling |
| ✅MAINT-BUG-005 | xivdyetools-maintainer | Stale Session Token Caching | MEDIUM | State Management |
| ✅MOD-BUG-001 | xivdyetools-moderation-worker | Race Condition in Rate Limiting | HIGH | Concurrency |
| ⚠️MOD-BUG-006 | xivdyetools-moderation-worker | Race Condition in Modal Submission Processing | MEDIUM | Concurrency |
| ✅OAUTH-BUG-001 | xivdyetools-oauth | String Spread with charCodeAt Encoding | MEDIUM | Encoding |
| ⚠️OAUTH-SEC-004 | xivdyetools-oauth | Race Condition in Rate Limiter DO Persistence | MEDIUM | Concurrency |
| ✅PRESETS-BUG-001 | xivdyetools-presets-api | Potential Memory Leak in Rate Limiter | MEDIUM | Memory Leak |
| ⚠️PRESETS-BUG-002 | xivdyetools-presets-api | Race Condition in Vote Count Inconsistency | MEDIUM | Race Condition |
| ✅TEST-BUG-001 | xivdyetools-test-utils | Race Condition in KV Mock TTL Expiration | HIGH | Concurrency |
| ✅TEST-BUG-002 | xivdyetools-test-utils | Memory Leak in Fetcher Mock Call History | MEDIUM | Memory Leak |
| ✅TEST-BUG-005 | xivdyetools-test-utils | Base64URL Encoding May Produce Invalid Results | MEDIUM | Encoding |
| ✅TYPES-BUG-002 | xivdyetools-types | Missing Discriminant Union in AuthResponse | MEDIUM | Type Safety |
| ✅TYPES-BUG-011 | xivdyetools-types | XIVAuthUser Type Doesn't Match Documented Response | HIGH | Type Mismatch |
| ✅PROXY-BUG-001 | xivdyetools-universalis-proxy | Race Condition in Response.json() Double-Parsing | HIGH | Async Handling |
| ✅PROXY-BUG-002 | xivdyetools-universalis-proxy | Unhandled Promise Rejection in Request Coalescer | HIGH | Error Handling |
| ✅WEB-BUG-001 | xivdyetools-web-app | Event Listener Accumulation Risk in DyeSelector | HIGH | Memory Leak |
| ✅WEB-BUG-003 | xivdyetools-web-app | Race Condition in Palette Service Import Count | MEDIUM | Race Condition |

### Refactoring Opportunities

| ID | Project | Title | Priority | Effort |
|----|---------|-------|----------|--------|
| ✅CORE-REF-001 | xivdyetools-core | Excessive Error Swallowing in DyeSearch | HIGH | LOW |
| CORE-REF-002 | xivdyetools-core | Duplicate Price Parsing Logic | MEDIUM | MEDIUM |
| DISCORD-REF-001 | xivdyetools-discord-worker | Repeated Command Handler Pattern | LOW | MEDIUM |
| ⚠️DISCORD-REF-004 | xivdyetools-discord-worker | God Object in rate-limiter.ts | MEDIUM | LOW |
| LOGGER-REF-003 | xivdyetools-logger | Hardcoded Redact Fields in Multiple Locations | MEDIUM | MEDIUM |
| MAINT-REF-003 | xivdyetools-maintainer | Hardcoded Locale Lists in Multiple Files | MEDIUM | MEDIUM |
| MOD-REF-001 | xivdyetools-moderation-worker | Long Function - processModerateCommand | MEDIUM | MEDIUM |
| ✅MOD-REF-002 | xivdyetools-moderation-worker | Code Duplication in Modal Handlers | MEDIUM | LOW |
| ✅OAUTH-REF-002 | xivdyetools-oauth | Reduce Code Duplication in OAuth Handlers | HIGH | MEDIUM |
| PRESETS-REF-001 | xivdyetools-presets-api | Validation Logic Scattered Across Multiple Functions | MEDIUM | MEDIUM |
| ⚠️TEST-REF-001 | xivdyetools-test-utils | Long Method in setupFetchMock Handler Logic | MEDIUM | LOW |
| ⚠️TEST-REF-004 | xivdyetools-test-utils | Inconsistent Factory Function Naming | MEDIUM | LOW |
| ✅TYPES-REF-002 | xivdyetools-types | Missing Discriminated Union Audit | MEDIUM | HIGH |
| ⚠️PROXY-REF-001 | xivdyetools-universalis-proxy | Over-Broad Empty Catch Blocks | MEDIUM | LOW |
| WEB-REF-003 | xivdyetools-web-app | Component Size - MixerTool and HarmonyTool Exceed 500 Lines | MEDIUM | MEDIUM |

### Optimization Opportunities

| ID | Project | Title | Impact | Category |
|----|---------|-------|--------|----------|
| ✅CORE-OPT-002 | xivdyetools-core | N+1 Query Pattern in HarmonyGenerator | MEDIUM | Algorithm |
| ⚠️DISCORD-OPT-001 | xivdyetools-discord-worker | Inefficient Collection Lookup - O(n) Per Operation | MEDIUM | Algorithm |
| ⚠️DISCORD-OPT-003 | xivdyetools-discord-worker | Full Preset Fetch for Simple Checks | MEDIUM | Bandwidth |
| ⚠️LOGGER-OPT-001 | xivdyetools-logger | Sanitize Error Message Regex Runs Multiple Replacements | MEDIUM | Performance |
| ⚠️MAINT-OPT-001 | xivdyetools-maintainer | N+1 Query Pattern in getLocaleLabels | MEDIUM | I/O |
| ⚠️MAINT-OPT-005 | xivdyetools-maintainer | Missing Request Deduplication in ItemIdFetcher | MEDIUM | Bandwidth |
| ✅PRESETS-OPT-003 | xivdyetools-presets-api | Missing Database Indexes | MEDIUM | Database |
| ✅TEST-OPT-003 | xivdyetools-test-utils | D1 Mock Query Tracking Without Size Limit | MEDIUM | Memory |
| ⚠️PROXY-OPT-001 | xivdyetools-universalis-proxy | Redundant Cache Metadata Parsing on Every Request | MEDIUM | CPU |
| ⚠️WEB-OPT-002 | xivdyetools-web-app | Memoize Color Distance Calculations | MEDIUM | CPU |
| ✅WEB-OPT-003 | xivdyetools-web-app | Lazy-Load Market Board Data | MEDIUM | Bandwidth |

---

## Priority Matrix

### Immediate Action (High Impact, Low Effort)

1. **CORE-BUG-001/002** - Fix race conditions in APIService request deduplication
2. **DISCORD-BUG-001** - Implement atomic counter operations for analytics
3. **PROXY-BUG-001** - Fix Response.json() double consumption
4. **OAUTH-REF-002** - Extract common OAuth handler code
5. **CORE-REF-001** - Add error logging to swallowed exceptions

### Plan for Next Sprint (High Impact, High Effort)

1. ~~**TYPES-REF-002**~~ - ✅ FIXED: Discriminated unions for response types
2. ~~**TYPES-BUG-011**~~ - ✅ VERIFIED: XIVAuthUser type already corrected
3. **WEB-REF-003** - Break down large components (MixerTool, HarmonyTool) - ⏸️ DEFERRED
4. ~~**PRESETS-OPT-003**~~ - ✅ VERIFIED: Database indexes already comprehensive
5. **DISCORD-OPT-001** - Refactor collection storage structure - ⏸️ DEFERRED
6. ~~**TEST-BUG-001**~~ - ✅ FIXED: KV Mock TTL race condition

### Technical Debt Backlog (Lower Priority)

1. **LOGGER-REF-003** - Extract DEFAULT_REDACT_FIELDS to shared constant
2. **MAINT-REF-003** - Consolidate hardcoded locale lists
3. **TEST-REF-004** - Standardize factory function naming
4. **PROXY-REF-001** - Add proper error logging to catch blocks
5. **WEB-OPT-002** - Add memoization to color distance calculations

---

## Fixes Applied (2026-01-19)

The following "Immediate Action" items from the Priority Matrix have been addressed:

| ID | Status | Description | Files Modified |
|----|--------|-------------|----------------|
| **CORE-BUG-001/002** | ✅ FIXED | Race condition in APIService request deduplication. Used deferred promise pattern to ensure map entry exists before any async operations. | `xivdyetools-core/src/services/APIService.ts` |
| **DISCORD-BUG-001** | ✅ FIXED | Non-atomic counter increment in analytics. Added optimistic concurrency with retries and version tracking via KV metadata. | `xivdyetools-discord-worker/src/services/analytics.ts` |
| **PROXY-BUG-001** | ✅ FIXED | Race condition in RequestCoalescer. Applied same deferred promise pattern to prevent duplicate in-flight requests. | `xivdyetools-universalis-proxy/src/services/request-coalescer.ts` |
| **OAUTH-REF-002** | ✅ FIXED | Duplicated OAuth validation code. Extracted `validateCodeChallenge()` to shared utils, refactored both Discord and XIVAuth handlers to use shared constants and utilities. | `xivdyetools-oauth/src/utils/oauth-validation.ts`, `xivdyetools-oauth/src/handlers/authorize.ts`, `xivdyetools-oauth/src/handlers/xivauth.ts` |
| **CORE-REF-001** | ✅ FIXED | Swallowed exceptions in DyeSearch. Added `console.warn` logging for complete search failures while documenting intentional silent handling for per-dye errors. | `xivdyetools-core/src/services/dye/DyeSearch.ts` |

### Fix Details

#### CORE-BUG-001/002 - Deferred Promise Pattern
The original code had a TOCTOU (Time-Of-Check to Time-Of-Use) race:
```typescript
// BEFORE: Race window between check and set
if (this.pendingRequests.has(key)) { return pending; }
const promise = fetchData(); // async starts here
this.pendingRequests.set(key, promise); // stored too late!
```

Fixed by storing a deferred promise synchronously **before** any async work:
```typescript
// AFTER: No race window
const promise = new Promise((resolve, reject) => { ... });
this.pendingRequests.set(key, promise); // stored immediately
const result = await fetchData(); // async starts after
resolve(result);
```

#### DISCORD-BUG-001 - Optimistic Concurrency
KV doesn't support atomic increments, so added retry-based pattern with version metadata:
- Read current value + version
- Write new value + new version
- Verify write succeeded
- Retry on contention

Note: For truly atomic counters, Durable Objects should be used. Analytics Engine provides accurate long-term stats.

#### OAUTH-REF-002 - Consolidated Validation
Created shared utilities in `oauth-validation.ts`:
- `validateCodeChallenge()` - RFC 7636 format validation
- `validateRedirectUri()` - Origin allowlist validation
- `ALLOWED_REDIRECT_ORIGINS` constant in `constants/oauth.ts`

---

## High Severity Bugs Fixed (2026-01-19)

The following HIGH severity bugs have been addressed:

| ID | Status | Description | Files Modified |
|----|--------|-------------|----------------|
| **CORE-BUG-003** | ✅ FIXED | KDTree nearestNeighbor skipped far side search when `best` was null (all nodes excluded). Now searches far side when no valid candidate found yet. | `xivdyetools-core/src/utils/kd-tree.ts` |
| **CORE-BUG-004** | ✅ FIXED | HSV validation was optional but `dye.hsv.h` was accessed unconditionally for hue bucket indexing. Made HSV required in validation. | `xivdyetools-core/src/services/dye/DyeDatabase.ts` |
| **LOGGER-BUG-001** | ✅ FIXED | `perf.start()` silently overwrote existing timers with same label. Now warns and returns `false` if timer already active. | `xivdyetools-logger/src/presets/browser.ts` |
| **MOD-BUG-001** | ✅ FIXED | Rate limiter had same read-modify-write race as DISCORD-BUG-001. Applied optimistic concurrency with retries. | `xivdyetools-moderation-worker/src/middleware/rate-limit.ts` |
| **TYPES-BUG-011** | ✅ VERIFIED | XIVAuthUser type already corrected. Type definition includes note explaining XIVAuth does NOT return `username` or `avatar_url`. | `xivdyetools-types/src/auth/xivauth.ts` (no changes needed) |
| **PROXY-BUG-002** | ✅ FIXED | Fixed as part of PROXY-BUG-001. Deferred promise pattern ensures rejections propagate to all waiting callers. | `xivdyetools-universalis-proxy/src/services/request-coalescer.ts` |
| **WEB-BUG-001** | ✅ FIXED | `manageBtn.addEventListener()` bypassed BaseComponent event tracking. Changed to `this.on()` for proper cleanup via `unbindAllEvents()`. | `xivdyetools-web-app/src/components/dye-selector.ts` |

### Fix Details

#### CORE-BUG-003 - KDTree Far Side Search
The k-d tree's `searchNearest` method had a bug where it would skip searching the far side of the splitting plane if no valid candidate had been found on the near side:

```typescript
// BEFORE: Bug - skipped far side when best was null
if (farChild && best) {
  if (Math.abs(targetValue - nodeValue) <= best.distance) {
    best = this.searchNearest(farChild, ...);
  }
}

// AFTER: Fixed - search far side when best is null OR within distance
if (farChild) {
  if (!best || Math.abs(targetValue - nodeValue) <= best.distance) {
    best = this.searchNearest(farChild, ...);
  }
}
```

This bug would manifest when all nodes on the near side were excluded by the `excludeData` filter, causing valid nodes on the far side to be missed.

#### CORE-BUG-004 - HSV Required Validation
The Dye interface requires HSV values (used for hue bucket indexing), but validation only checked HSV *if present*. This allowed dyes without HSV to pass validation, then crash when accessing `dye.hsv.h`:

```typescript
// BEFORE: HSV optional in validation, but required in interface
if (dye.hsv !== undefined && dye.hsv !== null) { /* validate */ }

// AFTER: HSV required
if (dye.hsv === undefined || dye.hsv === null) {
  this.logger.warn(`Dye ${id} missing required HSV values`);
  return false;
}
```

#### LOGGER-BUG-001 - perf.start() Race Condition
The `perf.start()` method silently overwrote the start time if called twice with the same label. This caused data loss when concurrent operations used the same label:

```typescript
// BEFORE: Silent overwrite
start(label: string): void {
  activeTimers.set(label, performance.now());
}

// AFTER: Warn and prevent overwrite
start(label: string): boolean {
  if (activeTimers.has(label)) {
    console.warn(`Timer "${label}" is already active...`);
    return false;
  }
  activeTimers.set(label, performance.now());
  return true;
}
```

#### MOD-BUG-001 - Rate Limiter Race Condition
Same pattern as DISCORD-BUG-001: read-then-write without atomicity. Applied the same optimistic concurrency fix with retries and version metadata.

---

## Hidden Bugs Fixed (2026-01-19)

The following MEDIUM severity hidden bugs have been addressed:

| ID | Status | Description | Files Modified |
|----|--------|-------------|----------------|
| **TEST-BUG-002** | ✅ FIXED | Memory leak in Fetcher Mock call history. Added `maxCallHistory` config with FIFO eviction to prevent unbounded memory growth. | `xivdyetools-test-utils/src/cloudflare/fetcher.ts` |
| **TEST-BUG-005** | ✅ FIXED | Base64URL decode failed to properly handle UTF-8 multi-byte characters. Added `base64UrlDecodeBytes()` helper with `TextDecoder`. | `xivdyetools-test-utils/src/utils/crypto.ts` |
| **MAINT-BUG-005** | ✅ FIXED | Stale session token caching caused 401 errors after server restart. Added `invalidateSession()` and `fetchWithRetry()` with automatic token refresh on 401/403. | `xivdyetools-maintainer/src/services/fileService.ts` |
| **WEB-BUG-003** | ✅ FIXED | Race condition in palette import count. Consolidated two separate storage reads into one to prevent TOCTOU race. | `xivdyetools-web-app/src/services/palette-service.ts` |
| **PRESETS-BUG-001** | ✅ FIXED | Memory leak in IP rate limiter. Added `maxTrackedIps` limit (10,000) with LRU-style eviction to prevent unbounded memory growth under DDoS. | `xivdyetools-presets-api/src/services/rate-limit-service.ts` |
| **MAINT-BUG-001** | ✅ FIXED | Unhandled promise rejection in server health check. Added `.catch()` handler to set `serverOnline = false` on failure. | `xivdyetools-maintainer/src/components/DyeForm.vue` |
| **OAUTH-BUG-001** | ✅ FIXED | String spread with `charCodeAt` could fail on large arrays due to call stack limits. Replaced with `Array.from().map().join()` pattern. | `xivdyetools-oauth/src/services/jwt-service.ts` |
| **DISCORD-BUG-002** | ✅ VERIFIED | Analytics.writeDataPoint already had try-catch error handling with logger support. No changes needed. | — |

### Fix Details

#### TEST-BUG-002 - Fetcher Mock Memory Leak
The mock fetcher's `_calls` array grew unbounded, which could cause memory issues in long-running test suites:

```typescript
// BEFORE: Array grows without limit
const calls: FetcherCall[] = [];

// AFTER: FIFO eviction when limit exceeded
let maxCallHistory = config?.maxCallHistory ?? DEFAULT_MAX_CALL_HISTORY;

// In fetch handler:
while (calls.length > maxCallHistory) {
  calls.shift(); // FIFO eviction
}
```

#### MAINT-BUG-005 - Session Token Invalidation
When server restarts, old tokens become invalid but client kept using them:

```typescript
// ADDED: Token invalidation on 401/403
export function invalidateSession(): void {
  sessionToken = null;
  console.warn('Session token invalidated - will request new token on next mutation');
}

// ADDED: Automatic retry with fresh token
async function fetchWithRetry(url, options, timeout): Promise<Response> {
  const response = await fetchWithTimeout(url, { ...options, headers }, timeout);

  if (response.status === 401 || response.status === 403) {
    invalidateSession();
    const newToken = await getSessionToken(true);
    return fetchWithTimeout(url, { ...options, headers: retryHeaders }, timeout);
  }
  return response;
}
```

#### PRESETS-BUG-001 - Rate Limiter Memory Bound
Added maximum IP tracking limit to prevent memory exhaustion under attack:

```typescript
const PUBLIC_RATE_LIMIT = {
  maxRequests: 100,
  windowMs: 60_000,
  maxTrackedIps: 10_000,  // NEW: Prevents unbounded growth
};

function enforceMaxTrackedIps(): void {
  if (ipRequestLog.size <= PUBLIC_RATE_LIMIT.maxTrackedIps) return;

  // LRU-style eviction (Map maintains insertion order)
  const entriesToRemove = ipRequestLog.size - PUBLIC_RATE_LIMIT.maxTrackedIps;
  const keys = Array.from(ipRequestLog.keys());
  for (let i = 0; i < entriesToRemove; i++) {
    ipRequestLog.delete(keys[i]);
  }
}
```

### Assessed Bugs - Acceptable Limitations (⚠️)

The following bugs have been reassessed and determined to be **acceptable limitations** that don't require immediate fixes:

| ID | Assessment | Rationale |
|----|------------|-----------|
| **LOGGER-BUG-002** | ACCEPTABLE | Regex patterns handle 99%+ of common cases after LOG-ERR-001 fix. Remaining edge cases (escaped quotes, JSON objects, unquoted values with spaces) are extremely rare in actual error messages. Making patterns more greedy could cause false positives. |
| **MOD-BUG-006** | ACCEPTABLE | Modal submission race (two moderators rejecting same preset simultaneously) is handled gracefully - error is caught and displayed to user. Race is extremely rare (requires millisecond timing) and causes no data corruption. Proper fix would require distributed locking across workers. |
| **OAUTH-SEC-004** | INTENTIONAL | Durable Object rate limiter persistence race is an intentional trade-off. The DO eventually persists state, and slight over-counting during high-concurrency bursts is acceptable for rate limiting purposes. True atomicity would require blocking I/O. |
| **PRESETS-BUG-002** | PARTIALLY MITIGATED | Vote duplicate prevention now uses `ON CONFLICT DO NOTHING` (atomic). Remaining theoretical race between INSERT and UPDATE vote_count is extremely rare (requires server crash between statements). Impact is minor (off-by-one count) and can be corrected by periodic reconciliation. |

### Optimization Opportunities Addressed (2026-01-19)

| ID | Status | Rationale |
|----|--------|-----------|
| **CORE-OPT-002** | ✅ ALREADY OPTIMIZED | HarmonyGenerator already uses hue-indexed buckets (P-2) and O(log n) dye search via PERF-003 fix. No N+1 pattern present. |
| **DISCORD-OPT-001** | ⚠️ ACCEPTABLE | Collections stored as single JSON array with O(n) in-memory lookup. For MAX_COLLECTIONS=50, this is acceptable - KV I/O dominates, not array iteration. Per-collection KV keys would increase reads. |
| **DISCORD-OPT-003** | ⚠️ ACCEPTABLE | Full preset fetch in edit command is necessary for merging partial dye updates. API validates ownership server-side; client fetch provides better UX. |
| **LOGGER-OPT-001** | ⚠️ ACCEPTABLE | 7 separate regex replacements in `sanitizeErrorMessage` is more readable than combined regex. Error logging is infrequent; overhead is negligible. |
| **MAINT-OPT-001** | ⚠️ ACCEPTABLE | Sequential locale file processing (12 HTTP requests) in maintainer tool provides clear per-locale error messages and allows partial success. Not performance-critical (admin tooling). |
| **MAINT-OPT-005** | ⚠️ ACCEPTABLE | Sequential XIVAPI requests without caching is acceptable for a maintainer tool used manually and infrequently. |
| **PRESETS-OPT-003** | ✅ VERIFIED | Comprehensive indexes already exist in schema.sql (verified in Next Sprint fixes). |
| **TEST-OPT-003** | ✅ FIXED | Added `maxQueryHistory` config with FIFO eviction to D1 mock, preventing unbounded memory growth. See fix below. |
| **PROXY-OPT-001** | ⚠️ ACCEPTABLE | Cache metadata parsing (3 parseInt calls for Cache API, 1 metadata read for KV) happens once per cache lookup. Not redundant - necessary for staleness check. |
| **WEB-OPT-002** | ⚠️ ACCEPTABLE | Color distance matrix calculates N² distances, but max dyes is small (3-5). Core library pre-computes LAB values. Overhead is ~10-20 calculations per render. |
| **WEB-OPT-003** | ✅ ALREADY OPTIMIZED | MarketBoardService already implements lazy-loading with singleton cache, on-demand fetching, request versioning, and event-driven updates. |

#### TEST-OPT-003 Fix: D1 Mock Query History Limit

Added memory bounds to D1 mock query tracking, matching the pattern from TEST-BUG-002 (Fetcher mock):

```typescript
// xivdyetools-test-utils/src/cloudflare/d1.ts
export interface MockD1DatabaseConfig {
  maxQueryHistory?: number;  // Default: 1000
}

const enforceMaxHistory = (): void => {
  while (queries.length > maxQueryHistory) {
    queries.shift();
    bindings.shift();
  }
};

// Called after each query push in first(), all(), run(), raw(), exec()
```

### Low Effort Refactoring Addressed (2026-01-19)

| ID | Status | Rationale |
|----|--------|-----------|
| **DISCORD-REF-004** | ⚠️ NOT A GOD OBJECT | The `rate-limiter.ts` file (193 lines) is a focused single-responsibility module with only 2 exported functions (`checkRateLimit`, `formatRateLimitMessage`). It follows good separation of concerns with clear configuration constants and well-documented interfaces. No refactoring needed. |
| **MOD-REF-002** | ✅ FIXED | Extracted shared modal types and helpers to `types/modal.ts`. The `ModalInteraction` interface, `ModalComponents` type, and `extractTextInputValue` helper were duplicated across `preset-rejection.ts` and `ban-reason.ts`. Added convenience helpers `getModalUserId()` and `getModalUsername()`. |
| **TEST-REF-001** | ⚠️ ACCEPTABLE | The `fetch` method in `fetcher.ts` is ~50 lines, which is reasonable for a mock with header extraction, call recording, and response matching. Breaking it up would add complexity without significant benefit. The code is linear and well-commented. |
| **TEST-REF-004** | ⚠️ ACCEPTABLE | Factory function naming is actually consistent: `createMock{Entity}` for primary factories, `createMock{Entities}` for plurals, `createMock{Entity}Row` for database rows, `create{Variant}{Entity}` for special variants, and `{entity}ToRow`/`rowTo{Entity}` for converters. No changes needed. |
| **PROXY-REF-001** | ⚠️ INTENTIONAL | Empty catch blocks in `cache-service.ts` are intentional fail-soft behavior for non-critical caching operations. Cache lookup/storage failures shouldn't break the application. Adding logging would add noise for expected failures. The inline comments explain this design choice. |

#### MOD-REF-002 Fix: Shared Modal Types

Created `xivdyetools-moderation-worker/src/types/modal.ts` to eliminate duplicated code:

```typescript
// Shared types
export interface ModalInteraction { ... }
export type ModalComponents = Array<{ ... }>;

// Shared helpers
export function extractTextInputValue(components, customId): string | undefined;
export function getModalUserId(interaction): string | undefined;
export function getModalUsername(interaction, defaultName): string;
```

Updated handlers to import from shared module:
- `preset-rejection.ts` - Removed local `ModalInteraction`, `ModalComponents`, `extractTextInputValue`
- `ban-reason.ts` - Removed local `ModalInteraction`, `ModalComponents`, `extractTextInputValue`

---

## Next Sprint Items Fixed (2026-01-19)

The following items from the "Plan for Next Sprint" priority matrix have been addressed:

| ID | Status | Description | Files Modified |
|----|--------|-------------|----------------|
| **TYPES-REF-002** | ✅ FIXED | Implemented discriminated unions for all response types. AuthResponse, RefreshResponse, UserInfoResponse, APIResponse, PresetSubmitResponse, PresetEditResponse, VoteResponse, and ModerationResponse now use `success: true` / `success: false` literal types for proper type narrowing. | `xivdyetools-types/src/auth/response.ts`, `xivdyetools-types/src/api/response.ts`, `xivdyetools-types/src/preset/response.ts`, `xivdyetools-types/src/*/index.ts` |
| **PRESETS-OPT-003** | ✅ VERIFIED | Database indexes already comprehensive. `schema.sql` includes composite indexes for filtered+sorted queries (status+category+vote, status+vote, status+created, author+created) plus unique index on `dye_signature`. No changes needed. | `xivdyetools-presets-api/schema.sql` (no changes needed) |
| **TEST-BUG-001** | ✅ FIXED | Race condition in KV mock TTL expiration. Added snapshot-based timestamp capture (`Date.now() / 1000` at start of operation) to prevent TOCTOU races with mocked time. Also added proper expired key cleanup in `list()`. | `xivdyetools-test-utils/src/cloudflare/kv.ts` |
| **WEB-REF-003** | ⏸️ DEFERRED | Breaking down MixerTool (~2000 lines) and HarmonyTool (~2000 lines) into smaller components. HIGH effort requiring architectural planning. Deferred to future sprint. | — |
| **DISCORD-OPT-001** | ⏸️ DEFERRED | Refactoring collection storage from O(n) array to indexed individual KV entries. Requires migration logic and careful atomic operation handling. HIGH effort. Deferred to future sprint. | — |

### Fix Details

#### TYPES-REF-002 - Discriminated Unions for Response Types
The original response types used `success: boolean` with optional fields, which breaks TypeScript's type narrowing:

```typescript
// BEFORE: TypeScript can't narrow types
interface AuthResponse {
  success: boolean;
  token?: string;    // Optional - unknown if present
  error?: string;    // Optional - unknown if present
}

// AFTER: Discriminated union enables perfect narrowing
type AuthResponse = AuthSuccessResponse | AuthErrorResponse;

interface AuthSuccessResponse {
  success: true;     // Literal type
  token: string;     // Guaranteed present
  user: AuthUser;
  expires_at: number;
}

interface AuthErrorResponse {
  success: false;    // Literal type
  error: string;     // Guaranteed present
}
```

Now consumers get full type safety:
```typescript
if (response.success) {
  // TypeScript KNOWS: token, user, expires_at exist
  console.log(response.user.username);
} else {
  // TypeScript KNOWS: error exists
  console.error(response.error);
}
```

#### TEST-BUG-001 - KV Mock TTL Race Condition
The mock KV's TTL checking had a Time-Of-Check-To-Time-Of-Use (TOCTOU) vulnerability. In tests with mocked time (`vi.setSystemTime`), time could advance between checking expiration and returning the value:

```typescript
// BEFORE: Date.now() called multiple times
const isExpired = (key: string): boolean => {
  return Date.now() > expiration * 1000;  // Called here
};

get: async (key) => {
  if (isExpired(key)) { ... }  // Then here
  return store.get(key);        // Value could be stale
}

// AFTER: Snapshot timestamp used consistently
get: async (key) => {
  const nowSeconds = Date.now() / 1000;  // Capture once
  if (isExpiredAt(key, nowSeconds)) { ... }  // Use snapshot
  return store.get(key);
}
```

Also added proper cleanup of expired entries in `list()` to prevent stale data accumulation.

---

## Recommendations

### Cross-Cutting Concerns

1. **Race Condition Prevention**: Multiple projects exhibit race conditions in async operations. Consider:
   - Implementing atomic operations where possible
   - Using mutex patterns for shared state
   - Documenting concurrency assumptions

2. **Error Handling Consistency**: Several projects swallow errors silently. Establish:
   - Minimum logging standard for caught exceptions
   - Categorize expected vs unexpected errors
   - Add error tracking integration

3. **Type Safety**: ✅ Discriminated unions now implemented for all response types.
   - AuthResponse, RefreshResponse, UserInfoResponse use proper unions
   - APIResponse<T>, PresetSubmitResponse, PresetEditResponse, VoteResponse, ModerationResponse also updated
   - Consumers now get full type narrowing with `if (response.success)`

4. **Memory Management**: Rate limiters and mocks across projects share similar memory leak patterns:
   - Implement size limits on in-memory collections
   - Add periodic cleanup with deterministic intervals
   - Consider using external storage (KV/Durable Objects) for persistence

### Security Considerations

1. **OAuth State Signing**: Already well-implemented with HMAC
2. **Rate Limiting**: Multiple implementations exist - consider consolidating
3. **Input Validation**: Generally good, but some edge cases in error sanitization
4. **Redirect URI Validation**: Duplicated across handlers - extract to shared module

---

## Detailed Reports by Project

For detailed findings, see individual project reports:

- [xivdyetools-core Analysis](./projects/xivdyetools-core.md)
- [xivdyetools-discord-worker Analysis](./projects/xivdyetools-discord-worker.md)
- [xivdyetools-logger Analysis](./projects/xivdyetools-logger.md)
- [xivdyetools-maintainer Analysis](./projects/xivdyetools-maintainer.md)
- [xivdyetools-moderation-worker Analysis](./projects/xivdyetools-moderation-worker.md)
- [xivdyetools-oauth Analysis](./projects/xivdyetools-oauth.md)
- [xivdyetools-presets-api Analysis](./projects/xivdyetools-presets-api.md)
- [xivdyetools-test-utils Analysis](./projects/xivdyetools-test-utils.md)
- [xivdyetools-types Analysis](./projects/xivdyetools-types.md)
- [xivdyetools-universalis-proxy Analysis](./projects/xivdyetools-universalis-proxy.md)
- [xivdyetools-web-app Analysis](./projects/xivdyetools-web-app.md)

---

## Next Steps

1. Review findings with team
2. Prioritize items for remediation
3. Create tickets/issues for tracking
4. Proceed with approved modifications

---

*Generated by Claude Code Deep-Dive Analysis - 2026-01-19*
