# Code Audit: Remediation Roadmap

**Date:** 2025-12-14
**Total Findings:** 83
**Estimated Total Effort:** ~60 hours
**Last Updated:** 2025-12-14

---

## Session Log

### Session 1: 2025-12-14 — Critical Fixes (P0)

**Completed all 6 CRITICAL findings:**

| Finding | Status | Notes |
|---------|--------|-------|
| PRESETS-SQL-001 | ✅ Already Safe | Code uses switch/case with hardcoded ORDER BY values |
| PRESETS-SEC-001 | ✅ Fixed | Removed legacy mode, `BOT_SIGNING_SECRET` now mandatory |
| OAUTH-SEC-001 | ✅ Fixed | Restricted localhost CORS to ports 3000, 5173, 8787 |
| OAUTH-SEC-004 | ✅ Already Safe | Always uses canonical redirect URI from `WORKER_URL` |
| DISCORD-PERF-001 | ✅ Fixed | Added `InteractionContext` class for deadline tracking |
| CORE-BUG-001 | ✅ Fixed | Added `normalizeHue()` to prevent cache thrashing |

**Commits:**
- `xivdyetools-presets-api`: `fix(auth): require BOT_SIGNING_SECRET for bot authentication`
- `xivdyetools-oauth`: `fix(cors): restrict localhost CORS to specific ports`
- `xivdyetools-discord-worker`: `feat(discord): add deadline tracking for 3-second timeout`
- `xivdyetools-core`: `fix(color): normalize hue before caching to prevent thrashing`

---

## Priority Overview

| Priority | Count | Estimated Effort | Timeline |
|----------|-------|------------------|----------|
| P0 (Critical) | 6 | 4 hours | Day 1 |
| P1 (High) | 23 | 16 hours | This Sprint |
| P2 (Medium) | 44 | 32 hours | Next Sprint |
| P3 (Low) | 10 | 8 hours | Backlog |

---

## Day 1: Critical Security Fixes (P0)

**Goal:** Address all 6 CRITICAL findings that pose immediate security or reliability risks.

### Morning Session (2 hours)

#### 1. PRESETS-SQL-001: SQL Injection via ORDER BY
- **File:** `xivdyetools-presets-api/src/services/preset-service.ts`
- **Effort:** 15 minutes
- **Fix:** Whitelist sort parameter values
```typescript
const VALID_SORT = ['newest', 'popular', 'oldest', 'name'];
if (!VALID_SORT.includes(sort)) sort = 'newest';
```

#### 2. PRESETS-SEC-001: Bot Auth Header Spoofing
- **File:** `xivdyetools-presets-api/src/middleware/auth.ts`
- **Effort:** 30 minutes
- **Fix:** Remove legacy mode, require BOT_SIGNING_SECRET

#### 3. OAUTH-SEC-001: CORS Localhost Permissive
- **File:** `xivdyetools-oauth/src/index.ts`
- **Effort:** 15 minutes
- **Fix:** Whitelist specific ports (3000, 5173, 8787)

#### 4. OAUTH-SEC-004: Redirect URI Dev Bypass
- **File:** `xivdyetools-oauth/src/handlers/callback.ts`
- **Effort:** 10 minutes
- **Fix:** Remove development exception

### Afternoon Session (2 hours)

#### 5. DISCORD-PERF-001: Discord 3-Second Timeout
- **Files:** `xivdyetools-discord-worker/src/index.ts`, `src/utils/discord-api.ts`
- **Effort:** 1.5 hours
- **Fix:** Add deadline tracking and fail-safe response

#### 6. CORE-BUG-001: Floating-Point Cache Mismatch
- **File:** `xivdyetools-core/src/services/color/ColorConverter.ts`
- **Effort:** 30 minutes
- **Fix:** Normalize hue to [0,360) before caching

### Deploy & Verify
- Run all test suites
- Deploy to staging
- Verify fixes in staging
- Deploy to production

---

## This Sprint: High Severity (P1)

**Goal:** Complete all 23 HIGH severity findings.

### Week 1: Security Hardening

#### Day 2: Auth & Signatures (4 hours)

| Finding | Project | Effort |
|---------|---------|--------|
| DISCORD-SEC-001: /stats auth | discord-worker | 15 min |
| DISCORD-SEC-003: Timing-safe compare | discord-worker | 30 min |
| OAUTH-SEC-002: PKCE verification | oauth | 1 hour |
| OAUTH-SEC-003: State expiration | oauth | 30 min |
| OAUTH-BUG-002: Rate limit headers | oauth | 15 min |
| OAUTH-SEC-005: Scope validation | oauth | 20 min |
| PRESETS-SEC-002: Signature replay | presets-api | 1 hour |
| PRESETS-SEC-003: Moderator parsing | presets-api | 10 min |

#### Day 3: Input Validation (3 hours)

| Finding | Project | Effort |
|---------|---------|--------|
| DISCORD-SEC-002: SSRF validation | discord-worker | 1 hour |
| DISCORD-SEC-004: Preset API signature | discord-worker | 1 hour |
| DISCORD-BUG-003: Ephemeral flag | discord-worker | 15 min |
| DISCORD-BUG-001: Rate limiter boundary | discord-worker | 45 min |

### Week 2: Bug Fixes & Performance

#### Day 4: Worker Reliability (3 hours)

| Finding | Project | Effort |
|---------|---------|--------|
| DISCORD-BUG-002: KV error handling | discord-worker | 1 hour |
| OAUTH-BUG-001: JWT replay protection | oauth | 2 hours |

#### Day 5: Database & API (3 hours)

| Finding | Project | Effort |
|---------|---------|--------|
| PRESETS-BUG-001: Duplicate detection | presets-api | 1.5 hours |
| PRESETS-BUG-002: Moderation reset | presets-api | 10 min |
| PRESETS-BUG-003: Vote deduplication | presets-api | 30 min |
| OAUTH-BUG-003: User service race | oauth | 1 hour |

#### Day 6: Web App (3 hours)

| Finding | Project | Effort |
|---------|---------|--------|
| WEB-PERF-001: Modal console.log | web-app | 15 min |
| WEB-PERF-002: Modal incremental render | web-app | 1.5 hours |
| WEB-BUG-001: KeyboardService init | web-app | 15 min |
| WEB-REF-001: Error boundaries | web-app | 1 hour |

#### Day 7: Core Library (3 hours)

| Finding | Project | Effort |
|---------|---------|--------|
| CORE-PERF-001: LRU cache atomicity | core | 30 min |
| CORE-PERF-002: K-d tree optimization | core | 2 hours |
| CORE-PERF-003: K-means optimization | core | 30 min |

---

## Next Sprint: Medium Severity (P2)

**Goal:** Address 44 MEDIUM findings in priority order.

### Performance Optimizations (18 findings, ~12 hours)

| Finding | Project | Effort |
|---------|---------|--------|
| WEB-PERF-003: Event listener key | web-app | 10 min |
| WEB-PERF-004: Dye grid DOM | web-app | 1 hour |
| WEB-PERF-005: Translation cache | web-app | 30 min |
| WEB-PERF-006: Toast re-render | web-app | 30 min |
| DISCORD-PERF-003: Photon cold start | discord-worker | 30 min |
| DISCORD-PERF-004: SVG caching | discord-worker | 30 min |
| DISCORD-PERF-005: i18n redundancy | discord-worker | 10 min |
| PRESETS-PERF-002: Rate limit cache | presets-api | 30 min |
| PRESETS-PERF-003: Pagination limit | presets-api | 10 min |
| OAUTH-PERF-001: Rate limiter memory | oauth | 1 hour |
| CORE-BUG-002: K-d tree boundary | core | 15 min |
| CORE-BUG-004: HSV precision | core | 15 min |
| CORE-PERF-004: Pixel sampling | core | 10 min |
| WEB-REF-004: DOM references | web-app | 1.5 hours |
| WEB-REF-007: Bundle splitting | web-app | 4 hours |

### Bug Fixes (15 findings, ~8 hours)

| Finding | Project | Effort |
|---------|---------|--------|
| WEB-BUG-002: Modal listener race | web-app | 15 min |
| WEB-BUG-003: Theme double init | web-app | 10 min |
| WEB-BUG-004: Storage quota error | web-app | 15 min |
| WEB-BUG-005: Focus trap inert | web-app | 20 min |
| WEB-BUG-006: Tooltip memory leak | web-app | 10 min |
| WEB-BUG-007: Footer selectors | web-app | 15 min |
| WEB-BUG-008: V3 layout cleanup | web-app | 15 min |
| DISCORD-BUG-004: Analytics errors | discord-worker | 5 min |
| DISCORD-BUG-005: User storage keys | discord-worker | 1 hour |
| PRESETS-BUG-004: WHERE clause guard | presets-api | 5 min |
| CORE-BUG-003: Harmony 10-dye limit | core | 15 min |
| CORE-BUG-005: Facewear consistency | core | 15 min |
| CORE-SEC-001: Cache key delimiter | core | 15 min |

### Refactoring (11 findings, ~12 hours)

| Finding | Project | Effort |
|---------|---------|--------|
| WEB-REF-002: Event handler patterns | web-app | 2 hours |
| WEB-REF-003: Subscription utility | web-app | 2 hours |
| WEB-REF-005: Error throwing | web-app | 1 hour |
| WEB-REF-006: A11y announcer | web-app | 30 min |
| WEB-REF-008: Cleanup pattern | web-app | 10 min |
| DISCORD-REF-001: Command handlers | discord-worker | 4 hours |
| DISCORD-REF-002: Response wrappers | discord-worker | 20 min |
| PRESETS-SEC-004: Content-Type | presets-api | 15 min |
| PRESETS-SEC-005: Profanity ReDoS | presets-api | 30 min |
| PRESETS-REF-001: Validation merge | presets-api | 30 min |

---

## Backlog: Low Severity (P3)

**Goal:** Address opportunistically when working in related code.

| Finding | Project | Effort |
|---------|---------|--------|
| WEB-A11Y-001: ARIA labels | web-app | 20 min |
| WEB-A11Y-002: Focus management | web-app | 15 min |
| WEB-TYPE-001: Modal ID typing | web-app | 15 min |
| WEB-TYPE-002: Response wrapper | web-app | 15 min |
| WEB-PERF-007: Router handler | web-app | N/A |
| DISCORD-REF-003: i18n separation | discord-worker | 30 min |
| OAUTH-REF-002: JWT duplication | oauth | 30 min |
| PRESETS-REF-002: Notification errors | presets-api | 15 min |
| PRESETS-PERF-001: D1 batch | presets-api | 30 min |
| CORE-PERF-006: getAllDyes copy | core | 10 min |

---

## Cross-Cutting Improvements (Separate Initiative)

These improvements span multiple projects and should be scheduled as a dedicated initiative:

### Phase 1: Shared Infrastructure (8 hours)
1. Create `@xivdyetools/types` package
2. Create `@xivdyetools/logger` package
3. Standardize error handling
4. Add request correlation IDs

### Phase 2: Testing (8 hours)
1. Create `@xivdyetools/test-utils` package
2. Add cross-service integration tests
3. Add rate limiting boundary tests
4. Add concurrent request tests

### Phase 3: Documentation (4 hours)
1. Document all environment variables
2. Generate OpenAPI specs
3. Write architecture decision records
4. Update CLAUDE.md with findings

---

## Deployment Strategy

### For Critical Fixes (Day 1)
1. Create hotfix branch
2. Apply fixes with tests
3. Run full test suite
4. Deploy to staging
5. Smoke test in staging
6. Deploy to production
7. Monitor for errors

### For Sprint Work
1. Create feature branch per finding group
2. Implement fixes with tests
3. PR review
4. Merge to main
5. Deploy to staging at end of week
6. Production deployment Monday

### Rollback Plan
- Keep previous version tagged
- Use Cloudflare's instant rollback for workers
- Vercel's deployment history for web-app
- npm unpublish for core library (within 72h)

---

## Success Metrics

### After Day 1
- [ ] All CRITICAL findings resolved
- [ ] No security vulnerabilities in production
- [ ] All tests passing

### After This Sprint
- [ ] All HIGH findings resolved
- [ ] Security audit score improved
- [ ] No P0/P1 issues in backlog

### After Next Sprint
- [ ] All MEDIUM findings resolved
- [ ] Performance baseline established
- [ ] Test coverage improved

---

## Risk Mitigation

### High-Risk Changes
1. **K-d tree optimization** - Heavy testing required, potential for color matching regressions
2. **Modal incremental rendering** - UI behavior changes, test all modal scenarios
3. **PKCE verification** - Could break existing OAuth flows, test thoroughly
4. **Rate limiter changes** - Could cause false positives, monitor closely

### Mitigation Steps
- Feature flags for risky changes
- A/B testing where possible
- Gradual rollout (10% → 50% → 100%)
- Monitoring dashboards for key metrics
- On-call during initial deployment

---

## Progress Tracking

Use this checklist to track remediation progress:

### Day 1 (Critical) — ✅ COMPLETED 2025-12-14
- [x] PRESETS-SQL-001 — Already secure (uses switch/case with hardcoded values)
- [x] PRESETS-SEC-001 — Fixed: Removed legacy bot auth mode, BOT_SIGNING_SECRET now mandatory
- [x] OAUTH-SEC-001 — Fixed: Restricted localhost CORS to specific ports (3000, 5173, 8787)
- [x] OAUTH-SEC-004 — Already secure (always uses canonical redirect URI from WORKER_URL)
- [x] DISCORD-PERF-001 — Fixed: Added InteractionContext deadline tracking infrastructure
- [x] CORE-BUG-001 — Fixed: Added normalizeHue() to prevent cache key mismatches

### Week 1 (High Security)
- [ ] DISCORD-SEC-001
- [ ] DISCORD-SEC-003
- [ ] OAUTH-SEC-002
- [ ] OAUTH-SEC-003
- [ ] OAUTH-BUG-002
- [ ] OAUTH-SEC-005
- [ ] PRESETS-SEC-002
- [ ] PRESETS-SEC-003
- [ ] DISCORD-SEC-002
- [ ] DISCORD-SEC-004
- [ ] DISCORD-BUG-003
- [ ] DISCORD-BUG-001

### Week 2 (High Bugs/Perf)
- [ ] DISCORD-BUG-002
- [ ] OAUTH-BUG-001
- [ ] PRESETS-BUG-001
- [ ] PRESETS-BUG-002
- [ ] PRESETS-BUG-003
- [ ] OAUTH-BUG-003
- [ ] WEB-PERF-001
- [ ] WEB-PERF-002
- [ ] WEB-BUG-001
- [ ] WEB-REF-001
- [ ] CORE-PERF-001
- [ ] CORE-PERF-002
- [ ] CORE-PERF-003

---

## Appendix: Quick Reference

### Files Most Frequently Modified
1. `xivdyetools-presets-api/src/middleware/auth.ts` - 4 findings
2. `xivdyetools-presets-api/src/handlers/presets.ts` - 6 findings
3. `xivdyetools-oauth/src/handlers/callback.ts` - 4 findings
4. `xivdyetools-discord-worker/src/index.ts` - 3 findings
5. `xivdyetools-web-app/src/services/modal-service.ts` - 2 findings
6. `xivdyetools-core/src/services/color/ColorConverter.ts` - 3 findings

### Command Reference
```bash
# Run tests
cd xivdyetools-core && npm test
cd xivdyetools-web-app && npm test
cd xivdyetools-discord-worker && npm test

# Deploy workers
cd xivdyetools-oauth && npm run deploy
cd xivdyetools-presets-api && npm run deploy
cd xivdyetools-discord-worker && npm run deploy

# Deploy web app
cd xivdyetools-web-app && npm run build && npm run deploy
```
