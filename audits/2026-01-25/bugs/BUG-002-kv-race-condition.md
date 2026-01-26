# BUG-002: Race Condition in KV-Based Rate Limiting

## Severity
**HIGH** (Known/Documented)

## Type
Race Condition / Concurrency

## Location
- **File:** xivdyetools-moderation-worker/src/middleware/rate-limit.ts (lines 169-225)
- **File:** xivdyetools-discord-worker/src/services/rate-limiter.ts (lines 88-93)
- **File:** xivdyetools-discord-worker/src/services/analytics.ts (lines 97-103)

## Description
Cloudflare KV doesn't support atomic increments. All three implementations use optimistic concurrency with retries, but this still allows lost increments under high concurrency.

## Reproduction Scenario
1. Process A reads current count (value = 5)
2. Process B also reads (stale value = 5)
3. Process A calculates newCount = 6, writes
4. Process B calculates newCount = 6, overwrites
5. Lost increment: count should be 7, is 6

## Evidence
```typescript
// moderation-worker line 172
// MOD-BUG-001 FIX: KV doesn't support atomic increments

// discord-worker line 88
// DISCORD-BUG-001: KV race condition documented
```

## Suggested Fix
For critical rate limits, migrate to Durable Objects which provide atomic guarantees:

```typescript
// Using Durable Object
export class RateLimiter implements DurableObject {
  private count: number = 0;

  async increment(): Promise<number> {
    this.count++; // Atomic within DO
    return this.count;
  }
}
```

## Why It's Hidden
- Documented but accepted trade-off for availability
- Race window is small (~10ms)
- Impact is minor (allows 2x burst occasionally)
