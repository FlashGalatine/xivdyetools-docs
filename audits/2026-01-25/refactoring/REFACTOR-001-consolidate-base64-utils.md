# REFACTOR-001: Consolidate Base64URL Encoding/Decoding

## Priority
**HIGH**

## Category
Code Duplication

## Location
- xivdyetools-oauth/src/services/jwt-service.ts
- xivdyetools-oauth/src/utils/state-signing.ts
- xivdyetools-presets-api/src/middleware/auth.ts
- xivdyetools-test-utils/src/utils/crypto.ts

## Current State
4 independent implementations of base64URL encode/decode functions spread across projects.

## Issues
- Maintenance burden (4 places to fix bugs)
- Risk of inconsistent behavior
- ~200 lines of duplicated code
- No single source of truth

## Proposed Refactoring
Create shared `@xivdyetools/crypto` module or extend test-utils:

```typescript
// @xivdyetools/crypto/src/index.ts
export { base64UrlEncode, base64UrlDecode } from './base64';
export { hmacSign, hmacVerify } from './hmac';
```

## Benefits
- Single source of truth for crypto utilities
- Easier to audit security-critical code
- Consistent behavior across all projects
- Reduced code size

## Effort Estimate
**LOW** (2-3 hours)

## Risk Assessment
- Low risk: Functions are well-tested individually
- Breaking change: Projects need to update imports

## Status
**PARTIAL** (2026-01-25)

**Completed:**
- ✅ Exported `base64UrlDecode` from jwt-service.ts (OAUTH-REF-003)
- ✅ Updated state-signing.ts to import from jwt-service instead of duplicating
- ✅ Reduced duplicates from 4 → 3 locations

**Remaining:**
- ⏸️ presets-api/auth.ts still has its own implementation (different worker, no shared dependency)
- ⏸️ Full consolidation requires new `@xivdyetools/crypto` package

**Why Not Fully Consolidated:**
1. `@xivdyetools/test-utils` is a devDependency (can't use in production)
2. oauth and presets-api are separate Cloudflare Workers (no shared runtime)
3. Creating a new production package adds ecosystem complexity

**Recommended Path:**
- If more crypto utilities need sharing, create `@xivdyetools/crypto` package
- For now, the within-project deduplication removes the most significant duplication
