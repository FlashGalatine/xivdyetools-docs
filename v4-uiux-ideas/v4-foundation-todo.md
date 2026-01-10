# XIV Dye Tools v4 Foundation - TODO List

**Branch:** `v4/ui-migration`
**Last Updated:** 2026-01-10
**Status:** Phase 1-4 Complete

---

## Pre-Implementation

- [x] Create `v4/ui-migration` branch from main
- [x] Verify all tests pass on main: `npm run test -- --run`
- [x] Verify build succeeds: `npm run build`

---

## Phase 1: CSS Variables & Theme System ✅ COMPLETE

### 1.1 Type Definitions
- [x] Update `src/shared/types.ts` - Extend ThemePalette interface with v4 optional properties

### 1.2 CSS Variables
- [x] Update `src/styles/themes.css` - Add v4 CSS variables to `:root`
- [x] Create `src/styles/v4-utilities.css` - Glassmorphism utility classes
- [x] Import v4-utilities.css in main stylesheet

### 1.3 Theme Service
- [x] Update `src/services/theme-service.ts` - Add v4 properties to all 11 theme definitions
- [x] Update `applyTheme()` method to set v4 CSS custom properties
- [x] Add `disableBlur: true` to high-contrast themes

### 1.4 Verification
- [x] Run tests: `npm run test -- --run`
- [x] Manual check: All themes load without errors
- [x] Manual check: v4 CSS variables visible in browser dev tools

### 1.5 Commit
- [x] Commit: "feat(v4): Add CSS variable system for v4 themes"

---

## Phase 2: Router Service Updates ✅ COMPLETE

### 2.1 Router Types
- [x] Update `src/services/router-service.ts` - Change ToolId type (8 → 9 tools)
- [x] Update ROUTES array with new paths and titles
- [x] Add LEGACY_ROUTE_REDIRECTS constant
- [x] Update `handleInitialRoute()` to check legacy redirects

### 2.2 Verification (Types)
- [x] Run type-check: `npm run type-check`
- [x] Commit: "feat(v4): Update router service with new tool IDs and redirects"

### 2.3 Component Renames (preserve git history)
- [x] `git mv src/components/matcher-tool.ts src/components/extractor-tool.ts`
- [x] `git mv src/components/mixer-tool.ts src/components/gradient-tool.ts`
- [x] `git mv src/components/character-tool.ts src/components/swatch-tool.ts`
- [x] Commit: "feat(v4): Rename tool components for v4 naming convention"

### 2.4 Update Internal References
- [x] Update class name: `MatcherTool` → `ExtractorTool` in extractor-tool.ts
- [x] Update class name: `MixerTool` → `GradientTool` in gradient-tool.ts
- [x] Update class name: `CharacterTool` → `SwatchTool` in swatch-tool.ts
- [x] Update `src/components/v3-layout.ts` - Import paths and switch cases
- [x] Update component index exports (index.ts)

### 2.5 Create Placeholder Dye Mixer
- [x] Create `src/components/mixer-tool.ts` (NEW placeholder for Dye Mixer)
- [x] Add basic MixerTool class extending BaseComponent
- [x] Add placeholder UI: "Dye Mixer - Coming Soon"
- [x] Commit: "feat(v4): Add Dye Mixer tool placeholder"

### 2.6 Verification (Routes)
- [x] Run type-check: `npm run type-check`
- [X] Manual test: Navigate to `/matcher` → should redirect to `/extractor`
- [X] Manual test: Navigate to `/character` → should redirect to `/swatch`
- [X] Manual test: Navigate to `/mixer` → should load new Dye Mixer placeholder
- [X] Manual test: Navigate to `/gradient` → should load Gradient Builder (old Mixer)
- [X] Manual test: Browser back/forward navigation works

---

## Phase 3: Lit.js Setup ✅ COMPLETE

### 3.1 Dependencies
- [x] **NOTIFY USER:** Need to add `lit@^3.1.0` to package.json
- [x] Add lit to dependencies in package.json
- [x] Run `npm install`

### 3.2 TypeScript Configuration
- [x] Update `tsconfig.json` - Add `experimentalDecorators: true`
- [x] Update `tsconfig.json` - Add `useDefineForClassFields: false`
- [x] Add path alias: `"@v4/*": ["./src/components/v4/*"]`

### 3.3 Vite Configuration
- [x] Update `vite.config.ts` - Add `@v4` path alias

### 3.4 Create v4 Component Infrastructure
- [x] Create directory: `src/components/v4/`
- [x] Create `src/components/v4/base-lit-component.ts` - Base class for v4 Lit components
- [x] Create `src/components/v4/index.ts` - Barrel exports

### 3.5 Verification (Lit Setup)
- [x] Run build: `npm run build`
- [x] Verify Lit imports compile correctly
- [x] Verify v4 base component can be instantiated
- [x] Commit: "feat(v4): Add Lit.js infrastructure for component migration"

---

## Phase 4: Layout Components ✅ COMPLETE

### 4.1 Icons Update
- [x] Add `ICON_LOGO` - XIV Dye Tools branding icon
- [x] Add `ICON_GLOBE` - Language selector icon
- [x] Add `ICON_TOOL_DYE_MIXER` - New Dye Mixer tool icon
- [x] Update `TOOL_ICONS` map with v4 tool IDs (extractor, gradient, swatch, mixer)
- [x] Keep legacy aliases for backwards compatibility

### 4.2 V4AppHeader Component
- [x] Create `src/components/v4/v4-app-header.ts`
- [x] 48px header with logo, theme/language/about buttons
- [x] Events: `theme-click`, `language-click`, `about-click`
- [x] ARIA labels and focus-visible styles

### 4.3 ToolBanner Component
- [x] Create `src/components/v4/tool-banner.ts`
- [x] 64px horizontal navigation with 9 tool icons
- [x] Keyboard navigation (Arrow keys, Home, End)
- [x] Active state with gold accent and glow effect
- [x] Event: `tool-select` with `{toolId: ToolId}`

### 4.4 ConfigSidebar Component
- [x] Create `src/components/v4/config-sidebar.ts`
- [x] 320px glassmorphism sidebar
- [x] All-in-one approach: contains all 10 config sections
- [x] Show/hide sections based on `activeTool` property
- [x] Mobile slide-out drawer behavior
- [x] Config controls are visual placeholders (Phase 6 wiring)

### 4.5 V4LayoutShell Component
- [x] Create `src/components/v4/v4-layout-shell.ts`
- [x] Main orchestrator combining all layout pieces
- [x] MediaQueryList for responsive behavior
- [x] Mobile FAB for sidebar toggle
- [x] Slot for tool content

### 4.6 Layout CSS
- [x] Create `src/styles/v4-layout.css`
- [x] Global layout utilities
- [x] Theme-specific adjustments (light, high-contrast)
- [x] Print styles
- [x] Empty/loading state layouts

### 4.7 Exports
- [x] Update `src/components/v4/index.ts` - Export all Phase 4 components

### 4.8 Verification
- [x] Run type-check: `npm run type-check`
- [x] Run build: `npm run build`
- [ ] Manual test: Components render correctly
- [ ] Manual test: Tool banner navigation works
- [ ] Manual test: Sidebar collapse on mobile

---

## Final Verification

- [ ] Run full test suite: `npm run test -- --run`
- [ ] Run E2E tests: `npm run test:e2e`
- [x] Run production build: `npm run build`
- [ ] Check bundle size: `npm run check-bundle-size`
- [ ] Manual testing: All 9 routes work correctly
- [ ] Manual testing: Theme switching works with v4 variables

---

## Commits Made

| Commit | Message |
|--------|---------|
| 1 | feat(v4): Add CSS variable system for v4 themes |
| 2 | feat(v4): Update router with v4 tool IDs and legacy redirects |
| 3 | feat(v4): Rename tool components for v4 naming convention |
| 4 | feat(v4): Add Dye Mixer tool placeholder |
| 5 | feat(v4): Add Lit.js infrastructure for component migration |
| 6 | feat(v4): Add layout icons (ICON_LOGO, ICON_GLOBE, ICON_TOOL_DYE_MIXER) |
| 7 | feat(v4): Add v4 layout components (Header, ToolBanner, Sidebar, Shell) |

---

## Notes

### NPM Updates Completed
- **lit@^3.1.0** - Lit.js framework for v4 components (~5KB gzipped) ✅ Added in Phase 3

### CF Workers
- No changes required for this foundation phase

### Route Mapping Reference
| v3 Route | v4 Route | Tool Name |
|----------|----------|-----------|
| /harmony | /harmony | Harmony Explorer |
| /matcher | /extractor | Palette Extractor |
| /accessibility | /accessibility | Accessibility Checker |
| /comparison | /comparison | Dye Comparison |
| /mixer | /gradient | Gradient Builder |
| (new) | /mixer | Dye Mixer (NEW) |
| /presets | /presets | Community Presets |
| /budget | /budget | Budget Suggestions |
| /character | /swatch | Swatch Matcher |

---

## Future Phases

- ~~Phase 4: Layout Components (V4LayoutShell, V4AppHeader, ToolBanner, ConfigSidebar)~~ ✅ COMPLETE
- Phase 5: Shared Components (ResultCard, GlassPanel, ToggleSwitchV4, RangeSliderV4)
- Phase 6: Tool Migration to Lit.js (wire config controls, connect to services)
- Phase 7: New Dye Mixer Implementation
- Phase 8: Entry Point Integration (connect V4LayoutShell to main.ts)
