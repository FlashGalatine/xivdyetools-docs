# v3.0.0 UI Migration Checklist

> **Last Updated**: 2024-12-12 (Session 1 - Phase 0 & 1 Foundation)

## Pre-Migration Planning

### Phase 0: Preparation ✅

- [x] Review all mockup files in `src/mockups/`
  - *Audited: MockupShell.ts, MobileDrawer.ts, CollapsiblePanel.ts*
  - *See: `06-PHASE0-AUDIT-RESULTS.md` for detailed findings*
- [x] Identify shared components that can be extracted
  - *CollapsiblePanel and MobileDrawer are production-ready*
  - *Common UI patterns documented in 02-COMPONENTS.md*
- [x] Plan feature flag strategy for gradual rollout
  - *Created: `src/services/feature-flag-service.ts`*
  - *Strategy: LocalStorage toggle + URL param override (?ui=v3)*
- [ ] Set up A/B testing infrastructure (optional)
  - *Deferred: Will use simple feature flag for now*
- [ ] Create backup/rollback plan
  - *V2 code will remain until v3 is stable, then deleted entirely*

### Phase 0.5: Infrastructure ✅

- [x] Add CSS custom property definitions to global styles
  - *Verified in `src/styles/themes.css`*
- [x] Verify `--panel-left-width`, `--panel-collapsed-width`, `--drawer-transition` are defined
  - *All present: 280px, 64px, 0.3s ease-out respectively*
- [x] Ensure Tailwind v4 is configured correctly
  - *Confirmed: Tailwind v4 + Vite 7 + TypeScript 5.9*
- [ ] Test responsive breakpoints (768px boundary)
  - *Pending: Manual testing in next session*

---

## Core Layout Migration

### Phase 1: Shell Component

- [ ] Create production `TwoPanelShell` component based on `MockupShell`
- [ ] Implement desktop sidebar with collapse functionality
- [ ] Implement mobile bottom navigation
- [ ] Implement mobile drawer
- [ ] Add tool navigation with icons from `@shared/tool-icons`
- [ ] Wire up StorageService for sidebar collapse persistence
- [ ] Wire up LanguageService for localized tool names
- [ ] Add keyboard shortcuts (if applicable)
- [ ] Test resize behavior (desktop ↔ mobile transition)

*Note: MockupShell is already production-ready per audit. Migration involves:*
1. *Renaming/moving to `src/components/two-panel-shell.ts`*
2. *Integrating with FeatureFlagService*
3. *Connecting to real tool components instead of mockups*

### Phase 1.5: Routing Integration ✅

- [x] Decide on routing strategy (hash-based, history API, or internal)
  - *Decision: History API with Cloudflare Pages `_redirects` for SPA fallback*
- [x] Map tool IDs to routes (`/harmony`, `/matcher`, etc.)
  - *Created: `src/services/router-service.ts`*
  - *Routes: /harmony, /matcher, /accessibility, /comparison, /mixer, /presets, /budget*
- [x] Handle deep linking to specific tools
  - *Implemented: `handleInitialRoute()` parses URL on load*
- [x] Preserve query parameters across tool switches
  - *Implemented: `preservedParams` array in RouterService (dc, dye, ui)*

**Cloudflare Pages Configuration**:
- *`public/_redirects` already exists with `/* /index.html 200` catch-all*

---

## Tool-by-Tool Migration

### Phase 2: Color Harmony Explorer

**Left Panel:**
- [ ] Migrate dye selector component
- [ ] Migrate harmony type selector
- [ ] Migrate companion count slider
- [ ] Add CollapsiblePanel for Dye Filters
- [ ] Add CollapsiblePanel for Market Board
- [ ] Connect to existing dye data service

**Right Panel:**
- [ ] Migrate color wheel SVG visualization
- [ ] Migrate harmony cards grid
- [ ] Wire up export functionality
- [ ] Connect to color calculation logic

**Testing:**
- [ ] Verify all harmony types work correctly
- [ ] Test theme switching
- [ ] Test mobile layout

---

### Phase 3: Color Matcher

**Left Panel:**
- [ ] Migrate image upload (drag-drop, file, camera, paste)
- [ ] Migrate color picker with hex input
- [ ] Migrate eyedropper functionality
- [ ] Migrate sample size slider
- [ ] Migrate palette extraction toggle
- [ ] Add Dye Filters + Market Board panels

**Right Panel:**
- [ ] Migrate image canvas with zoom controls
- [ ] Migrate matched dyes result list
- [ ] Migrate recent colors row
- [ ] Wire up color matching algorithm

**Testing:**
- [ ] Test image upload on desktop browsers
- [ ] Test image upload on mobile (camera)
- [ ] Test clipboard paste
- [ ] Verify color matching accuracy

---

### Phase 4: Accessibility Checker

**Left Panel:**
- [ ] Migrate dye palette selector
- [ ] Migrate CVD simulation type selector
- [ ] Add Dye Filters + Market Board panels

**Right Panel:**
- [ ] Migrate color preview grid
- [ ] Migrate contrast matrix table
- [ ] Migrate distinguishability matrix with color-coded scores
- [ ] Wire up CVD simulation algorithms

**Testing:**
- [ ] Verify CVD simulations are accurate
- [ ] Test with actual users who have color vision deficiencies (if possible)
- [ ] Verify matrix calculations

---

### Phase 5: Dye Comparison

**Left Panel:**
- [ ] Migrate selected dyes list
- [ ] Migrate "Add Dyes" button/modal
- [ ] Add Market Board panel

**Right Panel:**
- [ ] Migrate Hue-Saturation Plot (SVG)
  - [ ] Ensure axis labels render correctly (0°-360°, 0%-100%)
  - [ ] Verify data points position correctly
- [ ] Migrate Brightness Distribution chart
  - [ ] Ensure bars fill available height
  - [ ] Test flexbox height propagation
- [ ] Migrate Distance Matrix table

**Testing:**
- [ ] Test chart responsiveness
- [ ] Verify brightness bars align to bottom
- [ ] Test with various numbers of dyes (2-8)

---

### Phase 6: Dye Mixer

**Left Panel:**
- [ ] Migrate Start Dye selector
- [ ] Migrate End Dye selector
- [ ] Migrate Steps slider (2-10)
- [ ] Migrate Color Space toggle (RGB/HSV)
- [ ] Add Dye Filters + Market Board panels
- [ ] **Fix**: Apply `!important` for dye name colors

**Right Panel:**
- [ ] Migrate gradient preview bar
- [ ] Migrate step markers with indices
- [ ] Migrate intermediate matches list
- [ ] Migrate export buttons (Copy, Download)
- [ ] Wire up interpolation algorithms (RGB and HSV)

**Testing:**
- [ ] Verify RGB interpolation
- [ ] Verify HSV interpolation
- [ ] Test gradient rendering
- [ ] Test export functionality

---

### Phase 7: Preset Browser

**Left Panel:**
- [ ] Migrate category filters
- [ ] Migrate sort options
- [ ] Migrate authentication section
  - [ ] Discord login integration
  - [ ] User profile display
  - [ ] My Submissions button
  - [ ] Submit Preset button

**Right Panel:**
- [ ] Migrate featured presets section
- [ ] Migrate preset grid
- [ ] Migrate preset detail view/modal
- [ ] Wire up API for fetching presets
- [ ] Wire up voting functionality

**Testing:**
- [ ] Test Discord OAuth flow
- [ ] Test preset loading and pagination
- [ ] Test voting (authenticated users only)
- [ ] Test preset submission flow

---

### Phase 8: Budget Suggestions (New Feature)

**Mockup Status**: ✅ Complete
- *Created: `src/mockups/tools/BudgetMockup.ts`*
- *Added: `ICON_TOOL_BUDGET` to `src/shared/tool-icons.ts`*
- *Updated: MockupShell, MockupNav, mockups/index.ts*

**Left Panel:**
- [x] Create target dye selector with price display *(mockup)*
- [x] Create quick pick buttons for popular expensive dyes *(mockup)*
- [x] Create budget limit slider (logarithmic scale, 0 → 1M gil) *(mockup)*
- [x] Create sort by radio group (Best Match, Lowest Price, Best Value) *(mockup)*
- [x] Add Dye Filters panel *(mockup)*
- [x] Add Data Center selector *(mockup)*

**Right Panel:**
- [x] Create target overview card (swatch, name, hex, price) *(mockup)*
- [x] Create alternatives header with count + sort indicator *(mockup)*
- [x] Create alternatives list with side-by-side swatches *(mockup)*
  - [x] Color distance indicator (Δ value with visual bar) *(mockup)*
  - [x] Price display *(mockup)*
  - [x] Savings badge (green) *(mockup)*
  - [x] Value score (when sorting by value) *(mockup)*
- [x] Create "No results" state with closest affordable suggestion *(mockup)*
- [ ] Wire up to existing `findClosestDyes()` + `PriceService`

**Integration:**
- [ ] Add "Find Cheaper" button to Color Matcher results
- [ ] Add navigation link from Harmony/Comparison tools
- [ ] Implement cross-tool deep linking

**i18n:**
- [ ] Add `tools.budget.title`, `tools.budget.shortName`, `tools.budget.description` to locale files
  - *Currently using fallback strings in MockupNav.ts*

**Testing:**
- [ ] Test budget slider UX (logarithmic feel)
- [ ] Test all sort modes
- [ ] Verify price data accuracy against Universalis
- [ ] Test with 0 results edge case
- [ ] Test mobile layout

---

## Post-Migration

### Phase 9: Polish & Optimization

- [ ] Performance audit (Lighthouse)
- [ ] Bundle size analysis
- [ ] Lazy-load tool components
- [ ] Add loading states/skeletons
- [ ] Add error boundaries
- [ ] Add analytics tracking

### Phase 10: Accessibility Audit

- [ ] Screen reader testing
- [ ] Keyboard navigation testing
- [ ] Focus management verification
- [ ] Color contrast verification (WCAG AA)
- [ ] Motion/animation preferences

### Phase 11: Cross-Browser Testing

- [ ] Chrome (latest)
- [ ] Firefox (latest)
- [ ] Safari (latest)
- [ ] Edge (latest)
- [ ] Mobile Safari (iOS)
- [ ] Chrome (Android)

### Phase 12: Documentation

- [ ] Update user documentation
- [ ] Update developer README
- [ ] Create changelog entry for v3.0.0
- [ ] Update screenshots in docs

### Phase 13: Release

- [ ] Feature flag rollout (10% → 50% → 100%)
- [ ] Monitor error rates
- [ ] Gather user feedback
- [ ] Address critical issues
- [ ] Full release

---

## Known Issues to Address

### CSS Specificity

| Issue | Location | Solution |
|-------|----------|----------|
| `<p>` color override | MixerMockup dye names | Add `!important` |
| Global text color | Various | Check DevTools, add `!important` if needed |

### Flexbox Height Issues

| Issue | Location | Solution |
|-------|----------|----------|
| Bars not filling height | Brightness chart | Use `flex-1 h-full` chain from parent to child |
| Charts not expanding | Grid cells | Use `flex flex-col` on card, `flex-1` on content |

### SVG Text Positioning

| Issue | Location | Solution |
|-------|----------|----------|
| Overlapping labels | Hue-Sat plot | Expand viewBox, adjust coordinates |
| Rotation centering | Saturation label | Use `text-anchor="middle"` + rotation transform |

---

## Quick Reference: File Locations

| Component | Source File | Status |
|-----------|-------------|--------|
| Shell | `src/mockups/MockupShell.ts` | ✅ Production-ready |
| Drawer | `src/mockups/MobileDrawer.ts` | ✅ Production-ready |
| Collapsible | `src/mockups/CollapsiblePanel.ts` | ✅ Production-ready |
| Harmony | `src/mockups/tools/HarmonyMockup.ts` | ✅ Mockup complete |
| Matcher | `src/mockups/tools/MatcherMockup.ts` | ✅ Mockup complete |
| Accessibility | `src/mockups/tools/AccessibilityMockup.ts` | ✅ Mockup complete |
| Comparison | `src/mockups/tools/ComparisonMockup.ts` | ✅ Mockup complete |
| Mixer | `src/mockups/tools/MixerMockup.ts` | ✅ Mockup complete |
| Presets | `src/mockups/tools/PresetsMockup.ts` | ✅ Mockup complete |
| Budget | `src/mockups/tools/BudgetMockup.ts` | ✅ Mockup complete |
| Tool Icons | `src/shared/tool-icons.ts` | ✅ All 7 icons |
| Feature Flag | `src/services/feature-flag-service.ts` | ✅ Created |
| Router | `src/services/router-service.ts` | ✅ Created |

---

## Session Log

### Session 1 (2024-12-12): Phase 0 & 1 Foundation

**Commits:**
1. `d4b9603` - feat(services): Add FeatureFlagService for v2/v3 UI toggle
2. `725c59c` - feat(services): Add RouterService for History API navigation
3. `9e38a76` - feat(mockups): Add Budget Suggestions tool mockup

**Completed:**
- Phase 0: Preparation ✅
- Phase 0.5: Infrastructure ✅
- Phase 1.5: Routing Integration ✅
- Budget mockup created ✅

**Next Session:**
- Phase 1: Create production TwoPanelShell component
- Manual testing of responsive breakpoints
- Begin Phase 2: Harmony Explorer migration

---

## Estimated Timeline

| Phase | Effort | Dependencies | Status |
|-------|--------|--------------|--------|
| 0-0.5 Prep | 1-2 days | None | ✅ Complete |
| 1-1.5 Shell | 3-5 days | Phase 0 | 🔄 Routing done, Shell pending |
| 2 Harmony | 2-3 days | Phase 1 | ⏳ Pending |
| 3 Matcher | 3-4 days | Phase 1 | ⏳ Pending |
| 4 Accessibility | 2-3 days | Phase 1 | ⏳ Pending |
| 5 Comparison | 2-3 days | Phase 1 | ⏳ Pending |
| 6 Mixer | 2-3 days | Phase 1 | ⏳ Pending |
| 7 Presets | 3-5 days | Phase 1, Auth | ⏳ Pending |
| 8 Budget | 2-3 days | Phase 1, Price API | 🔄 Mockup done |
| 9-13 Polish | 5-7 days | All phases | ⏳ Pending |

**Total Estimate**: 4-6 weeks for full migration
