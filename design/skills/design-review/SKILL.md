---
name: design-review
user_invocable: true
description: Full design quality pipeline — guard checks + composite scoring with layer sub-scores + auto-fix. Runs all active layers (craft, aesthetic, a11y) and produces a unified quality report. Run after coding UI.
license: MIT
metadata:
  version: 2.0.0
  category: design
  domain: design-quality
keywords:
  - design review
  - design guard
  - design check
  - UI quality
  - score
  - guard check
  - design violations
  - polish
  - composite score
  - bringhurst
  - accessibility
---

# Design Review

Full design quality pipeline: guard + composite score + fix. Runs **all active layers** and produces a unified quality report with layer sub-scores.

## Setup

1. Load the design engine: `~/.claude/skills/design/design-quality/SKILL.md`
2. Follow the engine's **Step 1: Load Project Config** to determine active layers, aesthetic, strictness, and teaching mode
3. Load layer references for each active layer:
   - If bringhurst active → `~/.claude/skills/design/design-quality/layers/bringhurst.md`
   - If a11y active → `~/.claude/skills/design/design-quality/layers/a11y.md`
4. Load the aesthetic preset from `~/.claude/skills/design/design-quality/presets/<name>.md`
5. Load the review rubric from `~/.claude/skills/design/design-quality/references/review-rubric.md`
6. Load the guard checklist from `~/.claude/skills/design/design-quality/references/guard-checks.md`

## Target

If a file path or component name is provided, review those files.
If "all" is specified, review all UI files in the project.
If no argument, ask the user which files to review.

## Execution

For each file in scope, run three phases across **all active layers**:

### Phase 1: Guard (catch violations)

Run through the guard checklist (`references/guard-checks.md`). **Only check rules tagged with active layers.**

For each violation found:
- Note the layer it belongs to (`[bringhurst]`, `[aesthetic]`, `[a11y]`)
- Apply strictness setting: `relaxed` downgrades everything to suggestion, `strict` upgrades warnings to errors
- Apply teaching setting for explanation depth

### Phase 2: Score (composite quality rating)

Score using the composite rubric (`references/review-rubric.md`):

**When all three layers active (default):**

| Layer | Sub-Score | Categories |
|-------|-----------|------------|
| Craft (bringhurst) | /30 | Measure & Readability (10), Vertical Rhythm (10), Type Scale & Weight (10) |
| Aesthetic (preset) | /40 | Hierarchy (10), Color & Tokens (10), Spacing & Grid (10), Elevation & Polish (10) |
| Accessibility (a11y) | /30 | WCAG Compliance (15), Visual Quality (8), Component States (7) |
| **Composite** | **/100** | |

**When a layer is disabled:** Re-weight remaining layers proportionally to 100.

Use the anchor examples in the rubric to calibrate scores:
- 90+: All layers pass, semantic HTML, tokens, rhythm, a11y complete
- 70-80: Mostly correct with some warnings per layer
- <60: Errors in multiple layers, missing fundamentals

### Phase 3: Fix (apply corrections)

After presenting the report, offer to auto-fix:

1. **Auto-fix errors** — Replace hardcoded colors with tokens, fix font families, add ARIA labels, constrain measure
2. **Auto-fix warnings** — Align spacing to grid, add transitions, fix elevation order, establish rhythm
3. **Skip suggestions** — These require judgment; flag but don't auto-apply

Use **AskUserQuestion** to confirm:
- "Fix all errors and warnings (N issues across M layers)?"
- "Fix errors only (N issues)?"
- "Don't fix — I'll handle it"

After fixing, re-score and show the before/after comparison.

## Output

Use the composite score report format from `references/review-rubric.md`:

```markdown
## Design Quality Review

**Composite Score: XX/100** (Preset: [name])

### Layer Sub-Scores
| Layer | Score | Rating |
|-------|-------|--------|
| Craft (bringhurst) | XX/30 | Pass / Needs Work / Fail |
| Aesthetic ([preset]) | XX/40 | Pass / Needs Work / Fail |
| Accessibility (a11y) | XX/30 | Pass / Needs Work / Fail |

### Category Breakdown
| Layer | Category | Score | Key Finding |
|-------|----------|-------|-------------|
| Craft | Measure & Readability | X/10 | ... |
| Craft | Vertical Rhythm | X/10 | ... |
| Craft | Type Scale & Weight | X/10 | ... |
| Aesthetic | Hierarchy | X/10 | ... |
| Aesthetic | Color & Tokens | X/10 | ... |
| Aesthetic | Spacing & Grid | X/10 | ... |
| Aesthetic | Elevation & Polish | X/10 | ... |
| A11y | WCAG Compliance | X/15 | ... |
| A11y | Visual Quality | X/8 | ... |
| A11y | Component States | X/7 | ... |

### Strengths
- [What's working well — reference specific layer]

### Issues Found (N)
- **[Layer]** Error `file:line` — Issue → Fix
- **[Layer]** Warning `file:line` — Issue → Fix
- **[Layer]** Suggestion `file:line` — Opportunity → Improvement

### Auto-Fixable (N of M issues)
Errors and warnings that can be automatically corrected.
```

After fixing, re-run the composite score and show improvement per layer.
