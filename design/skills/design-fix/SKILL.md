---
description: >-
  Apply design quality fixes grouped by severity with user approval. Fixes
  violations found by guard checks. Use after design review or standalone.
argument-hint: "[file-path | all]"
disable-model-invocation: true
---

# Design Fix

Apply design quality corrections with user approval. Fixes are grouped by severity and presented for confirmation before applying.

**This skill is a check consumer, not a check author.** It loads checks from [guard-checks.md](../design-quality/references/guard-checks.md) and the layer files. It does not define its own check logic.

## Setup

Ensure the [design-quality engine](../design-quality/SKILL.md) is loaded for layer/preset/config resolution.

## Modes

| Mode | Trigger | Finding Source |
|------|---------|---------------|
| **Post-review** | Same session as `/design:review` | Reads review findings from conversation context |
| **Standalone** | `/design:fix` with no prior review | Runs guard checks internally to collect findings |

In post-review mode, findings are already available — skip re-scanning.

## Target

If a file path is provided, fix that file.
If "all" is specified, fix all UI files in the project.
If no argument and post-review, use the files from the review.
If no argument and standalone, ask the user which files to fix.

## Execution

### Step 1: Collect Findings

**Post-review mode:** Extract findings from the design review in this conversation.

**Standalone mode:** Run the guard checklist from [guard-checks.md](../design-quality/references/guard-checks.md) against target files, respecting active layers and strictness config.

### Step 2: Classify by Confidence

| Tier | Auto-fixable? | Examples |
|------|---------------|---------|
| **High confidence** | Always safe | Hardcoded color → token, missing `aria-label` on icon buttons, touch target < 44px → `min-h-11`, missing focus ring → `focus-visible:ring-2`, missing `lang` attribute, spacing snap to grid, measure → `max-w-prose`, animation without `motion-safe:` |
| **Medium confidence** | Usually safe | Non-keyboard handler → `<button>`, font family replacement, elevation adjustment, weight normalization, `inert` attribute addition |
| **Low confidence** | Never auto-applied | Color-only differentiator, vague link text, visual hierarchy, scale violation, weight/typeface sprawl, DOM vs visual order, image alt text |

**Judgment checks (J1-J5) are never auto-fixed regardless of config.**

### Step 3: Present for Approval

Group findings by severity and present:

```
================================================================
DESIGN FIX: [filename]
================================================================

ERRORS (N issues — all auto-fixable)
────────────────────────────────────
  [aesthetic] Line 12: Hardcoded hex `text-[#333]`
              Fix: Replace with `text-foreground`
  [a11y]     Line 22: Touch target too small (24px)
              Fix: Change `h-6 w-6` to `min-h-11 min-w-11 p-2`
  ...

WARNINGS (N issues)
───────────────────
  [craft]    Line 8: Measure violation (max-w-4xl, ~100ch)
              Fix: Change to `max-w-prose`
  ...

SUGGESTIONS (N issues — require judgment, not auto-applied)
───────────────────────────────────────────────────────────
  [a11y]     Line 45: Custom modal, consider native <dialog>
  ...
================================================================
```

Use **AskUserQuestion** to confirm:

| Option | Behavior |
|--------|----------|
| Fix all errors and warnings (N issues) | Apply high + medium confidence fixes |
| Fix errors only (N issues) | Apply only error-level fixes |
| Don't fix — I'll handle it manually | Exit without changes |
| Select specific issues to skip | Present each issue for individual yes/no |

### Step 4: Apply Fixes

Apply approved fixes to the source files. For each fix:
1. Show the before/after diff
2. Apply the change
3. Verify the fix doesn't introduce new issues

### Step 5: Re-Score

After applying fixes:
- **Post-review mode:** Re-run composite score, show before/after delta
- **Standalone mode:** Run composite score, show absolute result

```
Fixes applied: N of M issues resolved.

Before: 62/100  →  After: 87/100 (+25)

Layer improvements:
  Craft:     18/30 → 26/30 (+8)
  Aesthetic: 22/40 → 35/40 (+13)
  A11y:      22/30 → 26/30 (+4)

Remaining: 2 suggestions (require manual judgment)
```

## Auto-Fix Config

Users can configure auto-fix behavior in their project's `CLAUDE.md`:

```markdown
## Design Quality

**Auto-fix:** none
```

| Value | Behavior |
|-------|----------|
| `none` | Present all findings for manual approval (default) |
| `errors` | Auto-apply error-level fixes; present warnings for approval |
| `errors-warnings` | Auto-apply errors + warnings; present suggestions for info |
| `silent` | Auto-apply high-confidence error-level fixes only, skip everything else, no prompt. For CI/pre-commit. |

## Error Handling

- **Fix introduces new issues:** Flag for human review. Never auto-fix the new issue (prevents loops).
- **File changed since review:** Skip with explanation: "Expected `text-[#333]`, found `text-foreground` — already fixed?"
- **Multi-file:** Failures in one file don't block others. Report all results at the end.
- **No findings:** "No issues found — design quality looks good."
