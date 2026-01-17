# i18n Audit: Fallback Pattern Analysis

**Project:** xivdyetools-web-app
**Date:** January 17, 2026
**Focus:** Instances of `LanguageService.t('key') || 'fallback'` pattern

---

## Overview

This document analyzes the use of fallback patterns in the codebase. While defensive coding can be beneficial, excessive fallbacks often indicate:

1. Uncertainty about whether translation keys exist
2. Keys that were added after the component was written
3. Copy-paste from components that genuinely needed fallbacks

**Total Fallback Instances Found: 50+**

---

## Why Fallbacks Are (Usually) Unnecessary

The `LanguageService.t()` method already has a built-in fallback chain:

```typescript
// From LanguageService implementation
static t(key: string): string {
  // 1. Try current locale
  const value = this.getNestedValue(currentTranslations, key);
  if (value) return value;

  // 2. Fall back to English
  const englishValue = this.getNestedValue(englishTranslations, key);
  if (englishValue) return englishValue;

  // 3. Return the key itself as last resort
  console.warn(`Missing translation: ${key}`);
  return key;
}
```

Therefore, `LanguageService.t('budget.targetDye') || 'Target Dye'` is redundant if the key exists.

---

## Fallback Pattern Inventory by File

### budget-tool.ts (40+ instances)

**File:** `src/components/budget-tool.ts`
**Severity:** Medium
**Reason:** Defensive coding, but all keys exist

| Line | Pattern | Key Exists? |
|------|---------|-------------|
| 472 | `LanguageService.t('budget.targetDye') \|\| 'Target Dye'` | Yes |
| 486 | `LanguageService.t('budget.quickPicks') \|\| 'Quick Picks'` | Yes |
| 500 | `LanguageService.t('budget.budgetLimit') \|\| 'Budget Limit'` | Yes |
| 514 | `LanguageService.t('budget.colorDistance') \|\| 'Color Distance'` | Yes |
| 528 | `LanguageService.t('budget.sortBy') \|\| 'Sort By'` | Yes |
| 542 | `LanguageService.t('filters.title') \|\| 'Dye Filters'` | Yes |
| 565 | `LanguageService.t('common.colorFormats') \|\| 'Color Formats'` | **No** |
| 580 | `LanguageService.t('marketBoard.title') \|\| 'Market Board'` | Yes |
| 623 | `LanguageService.t('common.hexCodes') \|\| 'Hex Codes'` | **No** |
| 624 | `LanguageService.t('common.rgbValues') \|\| 'RGB Values'` | **No** |
| 625 | `LanguageService.t('common.hsvValues') \|\| 'HSV Values'` | **No** |
| 626 | `LanguageService.t('common.labValues') \|\| 'LAB Values'` | **No** |
| ... | (30+ more similar patterns) | Varies |

**Analysis:** Most keys in budget-tool.ts DO exist. The pattern appears to be defensive coding rather than missing keys. However, 5 keys under `common.*` are missing and should be added.

---

### changelog-modal.ts (5 instances)

**File:** `src/components/changelog-modal.ts`
**Severity:** Low
**Reason:** All keys exist

| Line | Pattern | Key Exists? |
|------|---------|-------------|
| 99 | `LanguageService.t('changelog.title') \|\| "What's New..."` | Yes |
| 136 | `LanguageService.t('changelog.noChanges') \|\| 'Bug fixes...'` | Yes |
| 154 | `LanguageService.t('changelog.previousUpdates') \|\| 'Previous...'` | Yes |
| 189 | `LanguageService.t('changelog.viewFull') \|\| 'View full...'` | Yes |
| 200 | `LanguageService.t('changelog.gotIt') \|\| 'Got it!'` | Yes |

**Analysis:** All 5 keys exist. Fallbacks can be safely removed.

---

### about-modal.ts (3 instances)

**File:** `src/components/about-modal.ts`
**Severity:** Low
**Reason:** All keys exist

| Line | Pattern | Key Exists? |
|------|---------|-------------|
| 65 | `LanguageService.t('about.title') \|\| 'About...'` | Yes |
| 148 | `LanguageService.t('about.connect') \|\| 'Connect'` | Yes |
| 227 | `LanguageService.t('common.close') \|\| 'Close'` | Yes |

**Analysis:** All 3 keys exist. Fallbacks can be safely removed.

---

### MockupNav.ts (3 instances)

**File:** `src/mockups/MockupNav.ts`
**Severity:** Low (mockup code)
**Reason:** Keys exist

| Line | Pattern | Key Exists? |
|------|---------|-------------|
| 85 | `LanguageService.t('tools.budget.title') \|\| 'Budget'` | Yes |
| 86 | `LanguageService.t('tools.budget.shortName') \|\| 'Budget'` | Yes |
| 89 | `LanguageService.t('tools.budget.description') \|\| '...'` | Yes |

---

## Missing Keys Analysis

Based on the fallback pattern audit, the following keys are **actually missing** and need to be added:

### Keys to Add to en.json

```json
{
  "common": {
    "colorFormats": "Color Formats",
    "hexCodes": "Hex Codes",
    "rgbValues": "RGB Values",
    "hsvValues": "HSV Values",
    "labValues": "LAB Values"
  }
}
```

**Note:** These keys should be added to the `config` namespace where similar keys already exist:
- `config.colorFormats` (line 181 in en.json)
- `config.hexCodes` (line 182)
- `config.rgbValues` (line 183)
- `config.hsvValues` (line 184)
- `config.labValues` (line 185)

**Root Cause:** The budget-tool.ts is using `common.*` namespace but the keys are in `config.*` namespace. This is a **namespace mismatch**, not missing keys.

---

## Recommendations

### 1. Remove Redundant Fallbacks

For all cases where keys exist, remove the fallback:

**Before:**
```typescript
title: LanguageService.t('budget.targetDye') || 'Target Dye',
```

**After:**
```typescript
title: LanguageService.t('budget.targetDye'),
```

### 2. Fix Namespace Mismatches

In `budget-tool.ts`, change `common.*` to `config.*`:

**Before:**
```typescript
{ key: 'showHex' as const, label: LanguageService.t('common.hexCodes') || 'Hex Codes' },
```

**After:**
```typescript
{ key: 'showHex' as const, label: LanguageService.t('config.hexCodes') },
```

### 3. Consider a Lint Rule

Add an ESLint rule to warn about fallback patterns:

```javascript
// .eslintrc.js
{
  rules: {
    'no-restricted-syntax': [
      'warn',
      {
        selector: 'BinaryExpression[operator="||"][left.callee.property.name="t"]',
        message: 'Avoid fallback patterns with LanguageService.t(). If the key exists, remove the fallback.'
      }
    ]
  }
}
```

### 4. Add Key Validation Script

Create a script that validates all `LanguageService.t()` calls against en.json:

```bash
# Example: scripts/validate-i18n-keys.ts
# Extracts all t('key') calls and verifies they exist in locale files
```

---

## Fallback Categories Summary

| Category | Count | Action |
|----------|-------|--------|
| Key exists, fallback redundant | ~45 | Remove fallback |
| Key in wrong namespace | 5 | Fix namespace |
| Key genuinely missing | 0 | N/A |
| Intentional fallback (dynamic) | 0 | N/A |

---

## Verification Checklist

After implementing fixes:

- [ ] Remove fallbacks from `changelog-modal.ts` (5 instances)
- [ ] Remove fallbacks from `about-modal.ts` (3 instances)
- [ ] Remove fallbacks from `MockupNav.ts` (3 instances)
- [ ] Fix namespace in `budget-tool.ts` for `common.*` → `config.*`
- [ ] Remove remaining fallbacks from `budget-tool.ts` (~35 instances)
- [ ] Verify no console warnings for missing keys
- [ ] Test all 6 languages on affected pages
