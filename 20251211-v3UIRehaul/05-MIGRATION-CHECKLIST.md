# v3.0.0 UI Migration Checklist

## Pre-Migration Planning

### Phase 0: Preparation

- [ ] Review all mockup files in `src/mockups/`
- [ ] Identify shared components that can be extracted
- [ ] Plan feature flag strategy for gradual rollout
- [ ] Set up A/B testing infrastructure (optional)
- [ ] Create backup/rollback plan

### Phase 0.5: Infrastructure

- [ ] Add CSS custom property definitions to global styles
- [ ] Verify `--panel-left-width`, `--panel-collapsed-width`, `--drawer-transition` are defined
- [ ] Ensure Tailwind v4 is configured correctly
- [ ] Test responsive breakpoints (768px boundary)

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

### Phase 1.5: Routing Integration

- [ ] Decide on routing strategy (hash-based, history API, or internal)
- [ ] Map tool IDs to routes (`/harmony`, `/matcher`, etc.)
- [ ] Handle deep linking to specific tools
- [ ] Preserve query parameters across tool switches

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

### Phase 8: Budget Suggestions

**Left Panel:**
- [ ] Create target dye selector with price display
- [ ] Create quick pick buttons for popular expensive dyes
- [ ] Create budget limit slider (logarithmic scale, 0 → 1M gil)
- [ ] Create sort by radio group (Best Match, Lowest Price, Best Value)
- [ ] Add Dye Filters panel
- [ ] Add Data Center selector

**Right Panel:**
- [ ] Create target overview card (swatch, name, hex, price)
- [ ] Create alternatives header with count + sort indicator
- [ ] Create alternatives list with side-by-side swatches
  - [ ] Color distance indicator (Δ value with visual bar)
  - [ ] Price display
  - [ ] Savings badge (green)
  - [ ] Value score (when sorting by value)
- [ ] Create "No results" state with closest affordable suggestion
- [ ] Wire up to existing `findClosestDyes()` + `PriceService`

**Integration:**
- [ ] Add "Find Cheaper" button to Color Matcher results
- [ ] Add navigation link from Harmony/Comparison tools
- [ ] Implement cross-tool deep linking

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

| Mockup | Source File |
|--------|-------------|
| Shell | `src/mockups/MockupShell.ts` |
| Drawer | `src/mockups/MobileDrawer.ts` |
| Collapsible | `src/mockups/CollapsiblePanel.ts` |
| Harmony | `src/mockups/tools/HarmonyMockup.ts` |
| Matcher | `src/mockups/tools/MatcherMockup.ts` |
| Accessibility | `src/mockups/tools/AccessibilityMockup.ts` |
| Comparison | `src/mockups/tools/ComparisonMockup.ts` |
| Mixer | `src/mockups/tools/MixerMockup.ts` |
| Presets | `src/mockups/tools/PresetsMockup.ts` |
| Budget | `src/mockups/tools/BudgetMockup.ts` |
| Tool Icons | `src/shared/tool-icons.ts` |

---

## Estimated Timeline

| Phase | Effort | Dependencies |
|-------|--------|--------------|
| 0-0.5 Prep | 1-2 days | None |
| 1-1.5 Shell | 3-5 days | Phase 0 |
| 2 Harmony | 2-3 days | Phase 1 |
| 3 Matcher | 3-4 days | Phase 1 |
| 4 Accessibility | 2-3 days | Phase 1 |
| 5 Comparison | 2-3 days | Phase 1 |
| 6 Mixer | 2-3 days | Phase 1 |
| 7 Presets | 3-5 days | Phase 1, Auth |
| 8 Budget | 2-3 days | Phase 1, Price API |
| 9-13 Polish | 5-7 days | All phases |

**Total Estimate**: 4-6 weeks for full migration
