# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is the documentation hub for the XIV Dye Tools monorepo. It contains feature specifications, roadmaps, and archived development documentation. There is no code, build system, or tests in this repository.

See the root monorepo `CLAUDE.md` for the complete monorepo overview and inter-project dependencies.

## Document Types

| Location | Purpose | Status |
|----------|---------|--------|
| Root level (`*.md`) | Active feature specs and roadmaps | Living documents |
| `YYYYMMDD-*/` folders | In-progress initiatives | Active until archived |
| `historical/` | Archived documentation | Read-only |

## Key Documents

- **FEATURE_ROADMAP.md** - Master feature list with implementation status for all platforms
- **COLLECTIONS_SPEC.md** - Dye favorites/collections feature (complete)
- **COMMUNITY_PRESETS_SPEC.md** - Community preset sharing feature (complete)
- **BUDGET_AWARE_SUGGESTIONS.md** - Budget-filtered dye matching (planned)

## Conventions

### Folder Naming
Active initiatives use date-prefixed folders: `YYYYMMDD-InitiativeName/`

Example: `20251207-DiscordBotMigration/`

### Document Lifecycle
1. Create specification at root level or in dated folder
2. Update `FEATURE_ROADMAP.md` with status
3. When complete, move to `historical/` with date prefix

### Editing Rules
- **Do not modify** files in `historical/` except for typos or broken links
- Mark completed features with "✅ Done" in roadmap tables
- Link related specs to each other in "Related Documents" sections
