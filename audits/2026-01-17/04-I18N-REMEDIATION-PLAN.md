# i18n Audit: Remediation Plan

**Project:** xivdyetools-web-app
**Date:** January 17, 2026
**Status:** In Progress (P1 items completed)

---

## Overview

This document provides a prioritized action plan to address the i18n issues identified in this audit. Items are organized by priority level with estimated effort and testing requirements.

---

## Priority Levels

| Priority | Definition | SLA |
|----------|------------|-----|
| P1 | User-facing strings in English | This week |
| P2 | Code quality / maintainability | This sprint |
| P3 | Architecture improvements | Next sprint |
| P4 | Nice to have / technical debt | Backlog |

---

## P1: Critical (This Week)

### P1-001: Internationalize my-submissions-panel.ts ✅ COMPLETE

**Severity:** High
**File:** `src/components/my-submissions-panel.ts`
**Estimated Effort:** 2-3 hours
**Status:** ✅ **COMPLETED** (2026-01-17)

#### Task Description

Update the My Submissions panel to use the i18n system for all user-facing text.

#### Steps

1. **Add new keys to all locale files** (en.json, ja.json, de.json, fr.json, ko.json, zh.json):

```json
{
  "preset": {
    "signInToViewSubmissions": "Sign in to view your submissions",
    "loadingSubmissions": "Loading your submissions...",
    "noSubmissionsYet": "You haven't submitted any presets yet.",
    "submitPresetHint": "Click \"Submit Preset\" above to share your color palettes!",
    "voteCount": "{count} vote(s)"
  },
  "errors": {
    "failedToLoadSubmissions": "Failed to load submissions. Please try again.",
    "failedToDeletePreset": "Failed to delete preset. Please try again."
  },
  "aria": {
    "refreshSubmissions": "Refresh submissions"
  }
}
```

2. **Update component to use existing keys:**
   - Line 71: `preset.mySubmissions` (already exists!)
   - Line 332: `common.dyes` (already exists!)
   - Line 375: `preset.tags` (already exists!)
   - Line 418: `preset.edit` (already exists!)
   - Line 427: `preset.delete` (already exists!)
   - Line 470: `preset.confirmDelete` (already exists!)

3. **Replace native dialogs with i18n-compatible modals:**
   - Replace `confirm()` with `ModalService.confirm()`
   - Replace `alert()` with `ToastService.error()` using i18n keys

#### Testing

- [x] Switch to each of the 6 languages
- [x] Verify all text in My Submissions panel updates
- [x] Test with signed-in and signed-out states
- [x] Test delete confirmation dialog
- [x] Test error states

#### Completion Notes

- 15 hardcoded strings replaced with `LanguageService.t()` calls
- 10 new translation keys added to all 6 locale files
- Vote count implemented with pluralization (`voteCount`/`votesCount`)
- Native `confirm()` retained but uses translated message text

---

### P1-002: Internationalize image-upload-display.ts Error Messages ✅ COMPLETE

**Severity:** High
**File:** `src/components/image-upload-display.ts`
**Estimated Effort:** 1 hour
**Status:** ✅ **COMPLETED** (2026-01-17)

#### Task Description

Update error messages to use the i18n system.

#### Steps

1. **Add new keys to all locale files:**

```json
{
  "errors": {
    "pleaseSelectImageFile": "Please select an image file",
    "failedToReadImage": "Failed to read image",
    "failedToLoadImage": "Failed to load image",
    "failedToReadFile": "Failed to read file"
  }
}
```

2. **Update emit calls:**

```typescript
// Line 324
this.emit('error', { message: LanguageService.t('errors.pleaseSelectImageFile') });

// Line 330 (use existing key with updated message)
this.emit('error', { message: LanguageService.t('errors.imageTooLarge') });

// Line 341
this.emit('error', { message: LanguageService.t('errors.failedToReadImage') });

// Line 362
this.emit('error', { message: LanguageService.t('errors.failedToLoadImage') });

// Line 374
this.emit('error', { message: LanguageService.t('errors.failedToReadFile') });
```

#### Testing

- [x] Upload invalid file type (non-image)
- [x] Upload file larger than 20MB
- [x] Test with corrupted image file
- [x] Verify error messages in all 6 languages

#### Completion Notes

- 5 error messages replaced with `LanguageService.t()` calls
- 4 new error translation keys added to all 6 locale files
- Corrected `imageTooLarge` message from 10MB to 20MB (matching actual code limit)

---

## P2: High (This Sprint)

### P2-001: Fix Namespace Mismatch in budget-tool.ts

**Severity:** Medium
**File:** `src/components/budget-tool.ts`
**Estimated Effort:** 30 minutes
**Assigned To:** TBD

#### Task Description

Fix incorrect namespace references from `common.*` to `config.*`.

#### Steps

1. Find and replace:
   - `common.colorFormats` → `config.colorFormats`
   - `common.hexCodes` → `config.hexCodes`
   - `common.rgbValues` → `config.rgbValues`
   - `common.hsvValues` → `config.hsvValues`
   - `common.labValues` → `config.labValues`

2. Remove fallback patterns after fixing.

#### Testing

- [ ] Verify Budget tool displays correct labels
- [ ] Check all 6 languages

---

### P2-002: Remove Redundant Fallback Patterns

**Severity:** Medium
**Files:** Multiple (see 02-I18N-FALLBACK-PATTERNS.md)
**Estimated Effort:** 2-3 hours
**Assigned To:** TBD

#### Task Description

Remove all `|| 'fallback'` patterns where keys exist.

#### Steps

1. **changelog-modal.ts** (5 instances)
2. **about-modal.ts** (3 instances)
3. **MockupNav.ts** (3 instances)
4. **budget-tool.ts** (~35 instances after namespace fix)

#### Testing

- [ ] No console warnings for missing keys
- [ ] All affected pages render correctly
- [ ] Test with network throttling (ensure lazy loading works)

---

### P2-003: Audit ToastService Calls

**Severity:** Medium
**Files:** Multiple (~50 calls across codebase)
**Estimated Effort:** 4-6 hours
**Assigned To:** TBD

#### Task Description

Review all ToastService calls and ensure messages use i18n keys.

#### Steps

1. Search for `ToastService.success`, `ToastService.error`, `ToastService.warning`, `ToastService.info`
2. For each call, verify the message parameter uses LanguageService
3. Add missing keys to locale files
4. Update calls to use i18n

#### Example Fix

**Before:**
```typescript
ToastService.success('Copied to clipboard!');
```

**After:**
```typescript
ToastService.success(LanguageService.t('success.copiedToClipboard'));
```

#### Testing

- [ ] Trigger each toast type in each tool
- [ ] Verify messages in all 6 languages

---

## P3: Medium (Next Sprint)

### P3-001: Add Build-Time Translation Validation

**Severity:** Low
**Files:** New script + package.json
**Estimated Effort:** 4 hours
**Assigned To:** TBD

#### Task Description

Create a script that validates all `LanguageService.t()` calls against locale files.

#### Steps

1. Create `scripts/validate-i18n.ts`:
   - Parse all TypeScript files for `LanguageService.t('...')` calls
   - Extract key strings
   - Validate each key exists in en.json
   - Report missing keys with file:line references

2. Add npm script:
```json
{
  "scripts": {
    "validate:i18n": "ts-node scripts/validate-i18n.ts"
  }
}
```

3. Add to CI pipeline (optional)

#### Testing

- [ ] Script catches intentionally missing key
- [ ] Script passes with current codebase (after P1/P2 fixes)

---

### P3-002: Document i18n Patterns for Contributors

**Severity:** Low
**Files:** CONTRIBUTING.md or new I18N.md
**Estimated Effort:** 1 hour
**Assigned To:** TBD

#### Task Description

Document the correct patterns for using i18n in new components.

#### Content

1. How to use LanguageService.t()
2. How to add new keys to locale files
3. When to use core library vs. web app translations
4. Examples of good and bad patterns

---

## P4: Low (Backlog)

### P4-001: Add ESLint Rule for Fallback Patterns

**Severity:** Informational
**Estimated Effort:** 2 hours

Add a custom ESLint rule to warn about `LanguageService.t('key') || 'fallback'` patterns.

---

### P4-002: Consider Typed Translation Keys

**Severity:** Informational
**Estimated Effort:** 8+ hours

Explore generating TypeScript types from locale files for compile-time key validation.

---

### P4-003: Evaluate Translation Management Tools

**Severity:** Informational
**Estimated Effort:** Research

Evaluate tools like Crowdin, Lokalise, or Phrase for managing translations at scale.

---

## Implementation Order

```
Week 1:
├── P1-001: my-submissions-panel.ts (2-3h) ✅ DONE
├── P1-002: image-upload-display.ts (1h) ✅ DONE
├── P2-001: budget-tool namespace fix (30m) ✅ DONE
└── P2-002: Remove fallback patterns - priority files (2h) ✅ DONE

Week 2:
├── P2-002: Remove remaining fallback patterns (~399 instances) ⏳ Pending
└── P2-003: Audit ToastService (4-6h)

Sprint 2:
├── P3-001: Build-time validation (4h)
└── P3-002: Documentation (1h)

Backlog:
├── P4-001: ESLint rule
├── P4-002: Typed keys
└── P4-003: Tool evaluation
```

---

## Success Metrics

After completing P1 and P2 items:

| Metric | Before | Target |
|--------|--------|--------|
| Hardcoded user-facing strings | 20+ | 0 |
| Fallback patterns | 50+ | 0 |
| Components with i18n issues | 3 | 0 |
| Console i18n warnings | Unknown | 0 |

---

## Testing Checklist

After all remediations:

- [ ] Test each tool in all 6 languages
- [ ] Verify language switching updates all text
- [ ] Check error states display correctly
- [ ] Verify accessibility labels are translated
- [ ] Test with screen reader (optional)
- [ ] Performance: ensure no lazy loading issues
- [ ] Run `npm run validate:i18n` (when available)

---

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-01-17 | Claude Code | Initial remediation plan |
| 1.1 | 2026-01-17 | Claude Code | Marked P1-001 and P1-002 as complete |
| 1.2 | 2026-01-17 | Claude Code | Marked P2-001 and P2-002 (priority files) as complete |
