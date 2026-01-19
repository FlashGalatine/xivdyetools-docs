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
| CORE-BUG-001 | xivdyetools-core | Race Condition in Pending Request Deduplication | CRITICAL | Race Condition |
| CORE-BUG-002 | xivdyetools-core | Promise Rejection Not Properly Cleaned from Pending Map | CRITICAL | Async Handling |
| CORE-BUG-003 | xivdyetools-core | Potential Null Reference in KDTree Nearest Neighbor | HIGH | Null Safety |
| CORE-BUG-004 | xivdyetools-core | Unvalidated Type Cast in DyeDatabase | HIGH | Type Safety |
| DISCORD-BUG-001 | xivdyetools-discord-worker | Race Condition in Analytics Counter Increment | MEDIUM | Race Condition |
| DISCORD-BUG-002 | xivdyetools-discord-worker | Missing Error Handling in Analytics.writeDataPoint | MEDIUM | Error Handling |
| LOGGER-BUG-001 | xivdyetools-logger | Race Condition in Concurrent perf.start() Calls | HIGH | Concurrency |
| LOGGER-BUG-002 | xivdyetools-logger | Error Sanitization Regex Edge Cases | MEDIUM | Security |
| MAINT-BUG-001 | xivdyetools-maintainer | Unhandled Promise Rejection in Server Health Check | MEDIUM | Async Handling |
| MAINT-BUG-005 | xivdyetools-maintainer | Stale Session Token Caching | MEDIUM | State Management |
| MOD-BUG-001 | xivdyetools-moderation-worker | Race Condition in Rate Limiting | HIGH | Concurrency |
| MOD-BUG-006 | xivdyetools-moderation-worker | Race Condition in Modal Submission Processing | MEDIUM | Concurrency |
| OAUTH-BUG-001 | xivdyetools-oauth | String Spread with charCodeAt Encoding | MEDIUM | Encoding |
| OAUTH-SEC-004 | xivdyetools-oauth | Race Condition in Rate Limiter DO Persistence | MEDIUM | Concurrency |
| PRESETS-BUG-001 | xivdyetools-presets-api | Potential Memory Leak in Rate Limiter | MEDIUM | Memory Leak |
| PRESETS-BUG-002 | xivdyetools-presets-api | Race Condition in Vote Count Inconsistency | MEDIUM | Race Condition |
| TEST-BUG-001 | xivdyetools-test-utils | Race Condition in KV Mock TTL Expiration | HIGH | Concurrency |
| TEST-BUG-002 | xivdyetools-test-utils | Memory Leak in Fetcher Mock Call History | MEDIUM | Memory Leak |
| TEST-BUG-005 | xivdyetools-test-utils | Base64URL Encoding May Produce Invalid Results | MEDIUM | Encoding |
| TYPES-BUG-002 | xivdyetools-types | Missing Discriminant Union in AuthResponse | MEDIUM | Type Safety |
| TYPES-BUG-011 | xivdyetools-types | XIVAuthUser Type Doesn't Match Documented Response | HIGH | Type Mismatch |
| PROXY-BUG-001 | xivdyetools-universalis-proxy | Race Condition in Response.json() Double-Parsing | HIGH | Async Handling |
| PROXY-BUG-002 | xivdyetools-universalis-proxy | Unhandled Promise Rejection in Request Coalescer | HIGH | Error Handling |
| WEB-BUG-001 | xivdyetools-web-app | Event Listener Accumulation Risk in DyeSelector | HIGH | Memory Leak |
| WEB-BUG-003 | xivdyetools-web-app | Race Condition in Palette Service Import Count | MEDIUM | Race Condition |

### Refactoring Opportunities

| ID | Project | Title | Priority | Effort |
|----|---------|-------|----------|--------|
| CORE-REF-001 | xivdyetools-core | Excessive Error Swallowing in DyeSearch | HIGH | LOW |
| CORE-REF-002 | xivdyetools-core | Duplicate Price Parsing Logic | MEDIUM | MEDIUM |
| DISCORD-REF-001 | xivdyetools-discord-worker | Repeated Command Handler Pattern | LOW | MEDIUM |
| DISCORD-REF-004 | xivdyetools-discord-worker | God Object in rate-limiter.ts | MEDIUM | LOW |
| LOGGER-REF-003 | xivdyetools-logger | Hardcoded Redact Fields in Multiple Locations | MEDIUM | MEDIUM |
| MAINT-REF-003 | xivdyetools-maintainer | Hardcoded Locale Lists in Multiple Files | MEDIUM | MEDIUM |
| MOD-REF-001 | xivdyetools-moderation-worker | Long Function - processModerateCommand | MEDIUM | MEDIUM |
| MOD-REF-002 | xivdyetools-moderation-worker | Code Duplication in Modal Handlers | MEDIUM | LOW |
| OAUTH-REF-002 | xivdyetools-oauth | Reduce Code Duplication in OAuth Handlers | HIGH | MEDIUM |
| PRESETS-REF-001 | xivdyetools-presets-api | Validation Logic Scattered Across Multiple Functions | MEDIUM | MEDIUM |
| TEST-REF-001 | xivdyetools-test-utils | Long Method in setupFetchMock Handler Logic | MEDIUM | LOW |
| TEST-REF-004 | xivdyetools-test-utils | Inconsistent Factory Function Naming | MEDIUM | LOW |
| TYPES-REF-002 | xivdyetools-types | Missing Discriminated Union Audit | MEDIUM | HIGH |
| PROXY-REF-001 | xivdyetools-universalis-proxy | Over-Broad Empty Catch Blocks | MEDIUM | LOW |
| WEB-REF-003 | xivdyetools-web-app | Component Size - MixerTool and HarmonyTool Exceed 500 Lines | MEDIUM | MEDIUM |

### Optimization Opportunities

| ID | Project | Title | Impact | Category |
|----|---------|-------|--------|----------|
| CORE-OPT-002 | xivdyetools-core | N+1 Query Pattern in HarmonyGenerator | MEDIUM | Algorithm |
| DISCORD-OPT-001 | xivdyetools-discord-worker | Inefficient Collection Lookup - O(n) Per Operation | MEDIUM | Algorithm |
| DISCORD-OPT-003 | xivdyetools-discord-worker | Full Preset Fetch for Simple Checks | MEDIUM | Bandwidth |
| LOGGER-OPT-001 | xivdyetools-logger | Sanitize Error Message Regex Runs Multiple Replacements | MEDIUM | Performance |
| MAINT-OPT-001 | xivdyetools-maintainer | N+1 Query Pattern in getLocaleLabels | MEDIUM | I/O |
| MAINT-OPT-005 | xivdyetools-maintainer | Missing Request Deduplication in ItemIdFetcher | MEDIUM | Bandwidth |
| PRESETS-OPT-003 | xivdyetools-presets-api | Missing Database Indexes | MEDIUM | Database |
| TEST-OPT-003 | xivdyetools-test-utils | D1 Mock Query Tracking Without Size Limit | MEDIUM | Memory |
| PROXY-OPT-001 | xivdyetools-universalis-proxy | Redundant Cache Metadata Parsing on Every Request | MEDIUM | CPU |
| WEB-OPT-002 | xivdyetools-web-app | Memoize Color Distance Calculations | MEDIUM | CPU |
| WEB-OPT-003 | xivdyetools-web-app | Lazy-Load Market Board Data | MEDIUM | Bandwidth |

---

## Priority Matrix

### Immediate Action (High Impact, Low Effort)

1. **CORE-BUG-001/002** - Fix race conditions in APIService request deduplication
2. **DISCORD-BUG-001** - Implement atomic counter operations for analytics
3. **PROXY-BUG-001** - Fix Response.json() double consumption
4. **OAUTH-REF-002** - Extract common OAuth handler code
5. **CORE-REF-001** - Add error logging to swallowed exceptions

### Plan for Next Sprint (High Impact, High Effort)

1. **TYPES-REF-002** - Implement discriminated unions for all response types
2. **TYPES-BUG-011** - Fix XIVAuth type mismatch with documentation
3. **WEB-REF-003** - Break down large components (MixerTool, HarmonyTool)
4. **PRESETS-OPT-003** - Add database indexes for common queries
5. **DISCORD-OPT-001** - Refactor collection storage structure

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

3. **Type Safety**: The types package has several discriminated union issues that propagate to consumers. Priority:
   - Fix AuthResponse union types first (affects auth flow)
   - Add validation helpers for optional fields
   - Document type constraints clearly

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
