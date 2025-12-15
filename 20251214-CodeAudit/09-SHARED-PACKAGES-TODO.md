# Shared Packages Implementation - TODO

**Created:** 2025-12-14
**Status:** Phase 1 Complete (Package Creation)
**Related:** [06-CROSS-CUTTING.md](./06-CROSS-CUTTING.md), [07-REMEDIATION-ROADMAP.md](./07-REMEDIATION-ROADMAP.md)

---

## Overview

This document tracks the implementation progress for the shared packages initiative from the Cross-Cutting Concerns audit.

### Packages Created

| Package | Version | Location | Status |
|---------|---------|----------|--------|
| @xivdyetools/types | 1.0.0 | `xivdyetools-types/` | ✅ Built |
| @xivdyetools/logger | 1.0.0 | `xivdyetools-logger/` | ✅ Built |

---

## Phase 1: Package Creation ✅ COMPLETE

### @xivdyetools/types

- [x] Initialize package (package.json, tsconfig)
- [x] Create color types module (RGB, HSV, branded types)
- [x] Create dye types module (Dye, LocalizedDye, DyeWithDistance, DyeDatabase)
- [x] Create preset types module (16+ types consolidated)
- [x] Create auth types module (Discord, XIVAuth, JWT)
- [x] Create error types module (AppError, ErrorCode enum)
- [x] Create API response types module
- [x] Create localization types module
- [x] Create utility types module (Result<T,E>, isOk/isErr)
- [x] Set up barrel exports and subpath exports
- [x] Build successfully

**Files Created:** 28 source files → 56 compiled files

### @xivdyetools/logger

- [x] Initialize package (package.json, tsconfig)
- [x] Define Logger interfaces (Logger, ExtendedLogger, LogContext)
- [x] Implement BaseLogger abstract class
- [x] Implement ConsoleAdapter (pretty format)
- [x] Implement JsonAdapter (structured JSON)
- [x] Implement NoopAdapter (silent/library use)
- [x] Create browser preset (dev-only logging, error tracking, perf monitoring)
- [x] Create worker preset (JSON structured, request correlation)
- [x] Create library preset (backward compatible NoOpLogger/ConsoleLogger)
- [x] Build successfully

**Files Created:** 12 source files → 24 compiled files

---

## Phase 2: Publishing to npm

### Prerequisites
- [x] Add LICENSE file to both packages
- [x] Add README.md with usage examples to both packages
- [x] Verify package.json metadata (author, repository, keywords)
- [x] Copy .env, .gitignore, .npmrc from xivdyetools-core
- [x] Test package locally with `npm pack`

### @xivdyetools/types
- [x] Run `npm pack` and verify contents (115 files, 24.9 kB packed)
- [ ] Test import in a consuming project
- [ ] Publish to npm: `npm publish --access public`
- [ ] Verify on npmjs.com

### @xivdyetools/logger
- [x] Run `npm pack` and verify contents (51 files, 22.6 kB packed)
- [ ] Test import in a consuming project
- [ ] Publish to npm: `npm publish --access public`
- [ ] Verify on npmjs.com

---

## Phase 3: Migration (per project)

### Migration Order
1. **xivdyetools-core** - Foundation (other projects depend on it)
2. **xivdyetools-presets-api** - Standalone worker
3. **xivdyetools-oauth** - Standalone worker
4. **xivdyetools-discord-worker** - Depends on core
5. **xivdyetools-web-app** - Depends on core

### Per-Project Migration Checklist

#### xivdyetools-core
- [ ] Add `@xivdyetools/types` as dependency
- [ ] Add `@xivdyetools/logger` as dependency
- [ ] Update `src/types/index.ts` to re-export from @xivdyetools/types
- [ ] Update `src/types/logger.ts` to re-export from @xivdyetools/logger/library
- [ ] Add deprecation notices to old exports
- [ ] Run tests
- [ ] Publish new version

#### xivdyetools-presets-api
- [ ] Add `@xivdyetools/types` as dependency
- [ ] Add `@xivdyetools/logger` as dependency
- [ ] Replace `src/types.ts` imports with @xivdyetools/types
- [ ] Add request logger middleware using @xivdyetools/logger/worker
- [ ] Run tests
- [ ] Deploy

#### xivdyetools-oauth
- [ ] Add `@xivdyetools/types` as dependency
- [ ] Add `@xivdyetools/logger` as dependency
- [ ] Replace `src/types.ts` imports with @xivdyetools/types
- [ ] Add request logger middleware using @xivdyetools/logger/worker
- [ ] Run tests
- [ ] Deploy

#### xivdyetools-discord-worker
- [ ] Add `@xivdyetools/types` as dependency
- [ ] Add `@xivdyetools/logger` as dependency
- [ ] Replace `src/types/preset.ts` imports with @xivdyetools/types
- [ ] Replace console.log/error with structured logger
- [ ] Run tests
- [ ] Deploy

#### xivdyetools-web-app
- [ ] Add `@xivdyetools/types` as dependency
- [ ] Add `@xivdyetools/logger` as dependency
- [ ] Replace `src/shared/types.ts` imports with @xivdyetools/types
- [ ] Replace `src/shared/logger.ts` with @xivdyetools/logger/browser
- [ ] Run tests
- [ ] Deploy

---

## Phase 4: Cleanup

### Deprecation Period
- [ ] Add JSDoc `@deprecated` tags to old type exports
- [ ] Document migration guide in CHANGELOG
- [ ] Keep deprecated exports for 2 minor versions
- [ ] Remove deprecated exports in next major version

### Old Files to Eventually Remove

| Project | File | Replacement |
|---------|------|-------------|
| core | `src/types/index.ts` (type definitions) | `@xivdyetools/types` |
| core | `src/types/logger.ts` | `@xivdyetools/logger/library` |
| web-app | `src/shared/types.ts` | `@xivdyetools/types` |
| web-app | `src/shared/logger.ts` | `@xivdyetools/logger/browser` |
| discord-worker | `src/types/preset.ts` | `@xivdyetools/types/preset` |
| presets-api | `src/types.ts` | `@xivdyetools/types` |
| oauth | `src/types.ts` | `@xivdyetools/types` |

---

## Phase 5: Testing Infrastructure

### @xivdyetools/test-utils (Future)
- [ ] Create shared test utilities package
- [ ] Mock factories: `mockDyeService()`, `mockKVNamespace()`, `mockD1Database()`
- [ ] Test data factories: `createTestPreset()`, `createTestDye()`
- [ ] Cross-service integration test helpers

---

## Quick Reference

### Import Examples After Migration

```typescript
// Types
import { Dye, RGB, HexColor, createHexColor } from '@xivdyetools/types';
import { CommunityPreset, PresetFilters } from '@xivdyetools/types/preset';
import { AuthResponse, JWTPayload } from '@xivdyetools/types/auth';
import { AppError, ErrorCode } from '@xivdyetools/types/error';

// Logger - Browser
import { createBrowserLogger, perf } from '@xivdyetools/logger/browser';

// Logger - Worker
import { createWorkerLogger, getRequestId } from '@xivdyetools/logger/worker';

// Logger - Library
import { NoOpLogger, ConsoleLogger } from '@xivdyetools/logger/library';
import type { Logger } from '@xivdyetools/logger';
```

### Build Commands

```bash
# Build types package
cd xivdyetools-types && npm run build

# Build logger package
cd xivdyetools-logger && npm run build

# Pack for local testing
npm pack
```

---

## Notes

- Worker-specific `Env` interfaces remain in each worker (not shared)
- Types package has no runtime dependencies (pure TypeScript)
- Logger package has no runtime dependencies (pure TypeScript)
- Both packages target ES2020 and ESM format
