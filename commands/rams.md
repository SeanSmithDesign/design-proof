---
description: Run accessibility and visual design review (a11y layer only)
---

# RAMS — Standalone A11y Audit

Quick accessibility and visual design review. This is a shortcut that runs just the **rams (a11y) layer** from the design quality engine.

For a full composite audit (craft + aesthetic + a11y), use `/design-review` instead.

## Setup

1. Load the rams layer reference from `~/.claude/skills/design/design-quality/layers/rams.md`
2. If a `## Design Quality` section exists in the project's `CLAUDE.md`, respect **Strictness** and **Teaching** settings
3. Otherwise, use defaults: strictness `standard`, teaching `normal`

## Target

If `$ARGUMENTS` is provided, analyze that specific file or path.
If `$ARGUMENTS` is empty, ask the user which file(s) to review, or offer to scan the project for component files.

## Execution

Run the accessibility and visual design checks defined in `layers/rams.md`:

1. **Critical a11y checks (R1-R5):** Must fix — images without alt, icon-only buttons, unlabeled inputs, non-semantic handlers, missing href
2. **Serious a11y checks (R6-R10):** Should fix — focus outlines, keyboard handlers, color-only info, touch targets, contrast
3. **Moderate a11y checks (R11-R16):** Consider fixing — heading hierarchy, tabIndex, roles, reduced-motion, aria-live, skip links
4. **Visual checks (V1-V15):** Layout, typography, color, component states

Be specific with line numbers and code snippets. Provide fixes, not just problems. Prioritize critical accessibility issues first.

## Output

Use the standalone output format defined in `layers/rams.md` — the RAMS DESIGN REVIEW banner format with severity sections and summary score.

If asked, offer to fix the issues directly.
