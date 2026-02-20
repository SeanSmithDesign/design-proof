---
description: >-
  Full design quality audit with composite scoring. Runs guard checks across all
  active layers and produces a quality report with layer sub-scores out of 100.
argument-hint: "[file-path | component-name | all]"
---

# Design Review

Full design quality pipeline: guard + composite score. Runs **all active layers** and produces a unified quality report with layer sub-scores.

## Setup

Ensure the [design-quality engine](../design-quality/SKILL.md) is loaded — it handles layer/preset/config resolution (Step 1: Load Project Config).

Also load:
- [review-rubric.md](../design-quality/references/review-rubric.md) for scoring criteria
- [guard-checks.md](../design-quality/references/guard-checks.md) for the check reference

## Target

If a file path or component name is provided, review those files.
If "all" is specified, review all UI files in the project.
If no argument, ask the user which files to review.

## Execution

For each file in scope, run two phases across **all active layers**:

### Phase 1: Guard (catch violations)

Run through the guard checklist ([guard-checks.md](../design-quality/references/guard-checks.md)). **Only check rules tagged with active layers.**

For each violation found:
- Note the layer it belongs to (`[craft]`, `[aesthetic]`, `[a11y]`)
- Apply strictness setting: `relaxed` downgrades everything to suggestion, `strict` upgrades warnings to errors
- Apply teaching setting for explanation depth

### Phase 2: Score (composite quality rating)

Score using the composite rubric ([review-rubric.md](../design-quality/references/review-rubric.md)):

**When all three layers active (default):**

| Layer | Sub-Score | Categories |
|-------|-----------|------------|
| Craft | /30 | Measure & Readability (10), Vertical Rhythm (10), Type Scale & Weight (10) |
| Aesthetic (preset) | /40 | Hierarchy (10), Color & Tokens (10), Spacing & Grid (10), Elevation & Polish (10) |
| Accessibility (a11y) | /30 | WCAG Compliance (15), Visual Quality (8), Component States (7) |
| **Composite** | **/100** | |

**When a layer is disabled:** Re-weight remaining layers proportionally to 100.

Use the anchor examples in the rubric to calibrate scores:
- 90+: All layers pass, semantic HTML, tokens, rhythm, a11y complete
- 70-80: Mostly correct with some warnings per layer
- <60: Errors in multiple layers, missing fundamentals

After presenting the report, suggest running `/design:fix` to apply corrections.

## Output

Use the composite score report format from [review-rubric.md](../design-quality/references/review-rubric.md):

```markdown
## Design Quality Review

**Composite Score: XX/100** (Preset: [name])

### Layer Sub-Scores
| Layer | Score | Rating |
|-------|-------|--------|
| Craft | XX/30 | Pass / Needs Work / Fail |
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
Run `/design:fix` to apply.
```
