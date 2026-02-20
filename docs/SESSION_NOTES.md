# Session Notes

## 2026-02-20 — Plugin Restructure & Merge

### Summary

Restructured design-proof from a loose skills/commands folder into a proper Claude Code plugin under `design/`. Brainstormed and planned the restructure, executed it across 7 commits on `refactor/design-plugin`, then merged to `main` and pushed to origin.

### Features Implemented

**Plugin Scaffold**
- Created `design/.claude-plugin/plugin.json` with name, version, author, license, keywords
- Moved all skills from `skills/design/` to `design/skills/`
- Moved commands from `commands/` to `design/commands/`

**New Skills**
- `design-direction` — Define visual identity and creative direction before coding
- `design-fix` — Auto-apply corrections from review findings

**New Commands**
- `/design:direction`, `/design:brief`, `/design:review`, `/design:fix`, `/design:a11y`
- All 5 commands wired as thin entry points to their respective skills

**Renames & Cleanup**
- `bringhurst` layer → `craft` (clearer, less jargon)
- Presets renamed: `apple-notion` → `refined-simple`, `linear-mercury` → `clean-functional`, `stripe-vercel` → `premium-depth`
- Removed old `skills/design/` and `commands/` directories
- Updated all internal paths, frontmatter, and severity vocabulary

**Documentation**
- `design/README.md` — Install instructions, command reference, config guide
- `design/CLAUDE.md` — Development conventions, check ID scheme, pre-commit checklist
- `design/CHANGELOG.md`, `design/CREDITS.md`, `design/LICENSE`
- Brainstorm and plan docs in `docs/brainstorms/` and `docs/plans/`

**Root CLAUDE.md**
- Simplified to reference the plugin instead of inlining config
- Config section: Layers, Aesthetic, Strictness, Teaching, Auto-fix

### New Files Created

| File | Purpose |
|------|---------|
| `design/.claude-plugin/plugin.json` | Plugin manifest |
| `design/README.md` | User-facing install & usage docs |
| `design/CLAUDE.md` | Developer conventions |
| `design/CHANGELOG.md` | Version history |
| `design/CREDITS.md` | Attribution |
| `design/LICENSE` | MIT license |
| `design/commands/direction.md` | `/design:direction` command |
| `design/commands/brief.md` | `/design:brief` command |
| `design/commands/review.md` | `/design:review` command |
| `design/commands/fix.md` | `/design:fix` command |
| `design/skills/design-direction/SKILL.md` | Creative direction skill |
| `design/skills/design-fix/SKILL.md` | Auto-fix skill |
| `design/skills/design-review/SKILL.md` | Review orchestrator skill |

### Key Commands

- `/design:direction` — Define visual identity
- `/design:brief` — Generate constraints before coding
- `/design:review` — Composite score with layer sub-scores
- `/design:fix` — Auto-apply corrections
- `/design:a11y` — Accessibility audit

### Commits

- `2404909` — docs: add brainstorm and plan for plugin restructure
- `5ef355d` — feat(plugin): scaffold design plugin with copied source files
- `e9cb4c2` — refactor(plugin): update content — paths, names, frontmatter, severity sync
- `b4ceedc` — feat(plugin): add design-direction and design-fix skills
- `a96ad46` — feat(plugin): add command files for all 5 slash commands
- `7fb6007` — docs(plugin): add metadata — README, CREDITS, CHANGELOG, dev guide
- `beb1810` — refactor: update CLAUDE.md to lean plugin config
- `6e52db0` — refactor(plugin): remove old pre-plugin file structure

### Next Steps

- Test plugin install: `claude plugin add SeanSmithDesign/design-proof --path design`
- Test `claude --plugin-dir ./design` for local loading
- Verify all 5 commands register and run correctly
- Prepare distribution: official directory submission, skills.sh compatibility, npm package
- See `memory/design-plugin-distribution.md` for full publishing plan

### Repo

- https://github.com/SeanSmithDesign/design-proof

---

## 2026-02-20 — Plugin Installation & Testing Setup

### Summary

Installed the design plugin into Claude Code for local testing. Created a marketplace manifest so the plugin can be discovered by `claude plugin install`, added design-proof as a local marketplace, and verified the plugin is enabled.

### Features Implemented

- Created `.claude-plugin/marketplace.json` at repo root — wraps the `design/` plugin for marketplace discovery
- Added `design-proof` as a local marketplace (`claude plugin marketplace add`)
- Installed `design@design-proof` (v1.0.0, scope: user)
- Verified all 3 marketplaces configured: `claude-plugins-official`, `every-marketplace`, `design-proof`

### New Files Created

| File | Purpose |
|------|---------|
| `.claude-plugin/marketplace.json` | Marketplace manifest for local plugin install |

### Key Commands

```bash
claude plugin validate /path/to/design-proof/design  # Validate plugin structure
claude plugin marketplace add /path/to/design-proof   # Add as local marketplace
claude plugin install design@design-proof              # Install plugin
claude plugin list                                     # Verify installed
```

### Notes

- Plugin system requires a marketplace manifest (`.claude-plugin/marketplace.json`) even for single-plugin repos
- Local directory source means skill file changes are picked up on new sessions without re-installing
- Restart Claude Code (new session) for newly installed plugin skills to appear in skill list
- Plugin validates cleanly — 5 skills, 5 commands, 2 layers, 3 presets all wired correctly

### Next Steps

- Test all 5 commands in a new session: `/design:direction`, `/design:brief`, `/design:review`, `/design:fix`, `/design:a11y`
- Test the orchestrator (`design-quality`) loads as background knowledge
- Test per-project config parsing from `CLAUDE.md`
- Address unstaged change to `design/skills/design-quality/layers/a11y.md`

---

## 2025-02-19 — design-proof

### Summary

Implemented the Unified Design Quality System — a three-layer composable architecture for Claude Code that enforces typographic craft, aesthetic consistency, and accessibility at every workflow stage.

### Features Implemented

**Phase 1: Bringhurst Craft Layer**
- Created `layers/bringhurst.md` with 6 core principles (measure, vertical rhythm, modular scale, weight hierarchy, proportion, restraint)
- 6 guard checks (B1-B6) with severity levels and skip conditions
- Teaching moment templates for educational explanations

**Phase 2: RAMS Accessibility Layer**
- Created `layers/rams.md` with 16 WCAG checks (R1-R16) + 15 visual checks (V1-V15)
- Restructured `/rams` command as thin entry point delegating to layer file
- Preserved standalone output format (RAMS DESIGN REVIEW banner)

**Phase 3: Bug Fixes & Unified Review**
- Fixed guard drift: SKILL.md references guard-checks.md instead of duplicating inline
- Fixed `bg-white` contradiction: preset exception mechanism in guard check #3 + apple-notion preset
- Added anchor code examples to rubric (90+, 70-80, <60) for scoring consistency
- Updated design-review to produce composite scores with layer sub-scores

**Phase 4: Workflow Integration**
- Expanded proactive recommendations for all workflow stages (brainstorm/plan/work/review)
- Updated design-brief to generate constraints from all active layers (craft + aesthetic + a11y)

**Phase 5: Global Config**
- Updated ~/.claude/CLAUDE.md with new layer system, config fields, smart defaults

### New Files Created

| File | Purpose |
|------|---------|
| `skills/design/design-quality/layers/bringhurst.md` | Craft layer (typography & spatial fundamentals) |
| `skills/design/design-quality/layers/rams.md` | A11y layer (WCAG + visual quality) |

### Files Modified

| File | Change |
|------|--------|
| `skills/design/design-quality/SKILL.md` | v1.1 → v2.0: Layer architecture, config parsing, smart defaults |
| `skills/design/design-review/SKILL.md` | v1.1 → v2.0: Composite scoring with layer sub-scores |
| `skills/design/design-brief/SKILL.md` | v1.1 → v2.0: Multi-layer constraint generation |
| `skills/design/design-quality/references/guard-checks.md` | Layer tags, 10 new checks, preset exceptions |
| `skills/design/design-quality/references/review-rubric.md` | Composite format, layer sub-scores, anchor examples |
| `skills/design/design-quality/presets/apple-notion.md` | Explicit bg-white guard exception |
| `commands/rams.md` | Simplified to thin entry point |
| `CLAUDE.md` | Updated Design Quality section |

### Key Commands

- `/design-brief` — Generate constraints from all active layers before coding
- `/design-review` — Composite quality audit after coding (Craft /30 + Aesthetic /40 + A11y /30)
- `/rams` — Standalone a11y-only shortcut

### Commits

- `398b9ce` — Unified design quality system with three composable layers

### Repo

- https://github.com/SeanSmithDesign/design-proof
