# Session Notes

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
