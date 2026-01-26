# REFACTOR-002: Consolidate Rate Limiting Implementations

## Priority
**HIGH**

## Category
Code Duplication / Architecture

## Location
- xivdyetools-oauth/src/durable-objects/rate-limiter.ts
- xivdyetools-presets-api/src/services/rate-limit-service.ts
- xivdyetools-moderation-worker/src/middleware/rate-limit.ts
- xivdyetools-discord-worker/src/services/rate-limiter.ts

## Current State
4 independent rate limiting implementations with similar algorithms but different interfaces:
- Durable Object version (most reliable)
- KV-based versions (prone to race conditions)
- Different configuration formats
- Different response types

## Issues
- Inconsistent rate limit behavior
- Duplicate race condition mitigations
- No shared configuration schema
- Testing burden multiplied

## Proposed Refactoring
Create `@xivdyetools/rate-limiter` abstraction:

```typescript
interface IRateLimiter {
  check(key: string, config: RateLimitConfig): Promise<RateLimitResult>;
  increment(key: string): Promise<void>;
  reset(key: string): Promise<void>;
}

// Two backends
class DurableObjectRateLimiter implements IRateLimiter { }
class KVRateLimiter implements IRateLimiter { }
```

## Benefits
- Consistent rate limiting across ecosystem
- Single place to fix race condition issues
- Easier to swap backends (KV → DO)
- Shared configuration and response types

## Effort Estimate
**MEDIUM** (4-6 hours)

## Risk Assessment
- Medium risk: Rate limiting is security-critical
- Requires coordination across multiple projects
- Good test coverage essential
