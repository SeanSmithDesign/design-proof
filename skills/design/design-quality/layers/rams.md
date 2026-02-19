# Layer: RAMS (Accessibility & Visual Quality)

Expert accessibility and visual design review based on WCAG 2.1 guidelines and visual design best practices. Named after Dieter Rams — "good design is as little design as possible" extends to making interfaces work for everyone.

**Layer ID:** `rams`
**Default:** On (always active unless explicitly disabled)
**Applies to:** All platforms (web-first examples, concepts translate)

---

## Accessibility Checks (WCAG 2.1)

### Critical (Must Fix)

| ID | Check | WCAG | What to Look For |
|----|-------|------|------------------|
| R1 | Images without alt | 1.1.1 | `<img>` without `alt` attribute. Decorative images should use `alt=""`. |
| R2 | Icon-only buttons | 4.1.2 | `<button>` with only SVG/icon, no `aria-label` or visually hidden text |
| R3 | Form inputs without labels | 1.3.1 | `<input>`, `<select>`, `<textarea>` without associated `<label>` or `aria-label` |
| R4 | Non-semantic click handlers | 2.1.1 | `<div onClick>` or `<span onClick>` without `role`, `tabIndex`, `onKeyDown` |
| R5 | Missing link destination | 2.1.1 | `<a>` without `href` using only `onClick` |

### Serious (Should Fix)

| ID | Check | WCAG | What to Look For |
|----|-------|------|------------------|
| R6 | Focus outline removed | 2.4.7 | `outline-none` or `outline: none` without visible `focus-visible:ring-*` replacement |
| R7 | Missing keyboard handlers | 2.1.1 | Interactive elements with `onClick` but no `onKeyDown`/`onKeyUp` |
| R8 | Color-only information | 1.4.1 | Status/error indicated only by color (no icon/text companion) |
| R9 | Touch target too small | 2.5.5 | Clickable elements smaller than 44x44px (min-h-11 min-w-11) |
| R10 | Low contrast text | 1.4.3 | Text contrast below 4.5:1 for normal text, 3:1 for large text (18px+ or 14px+ bold) |

### Moderate (Consider Fixing)

| ID | Check | WCAG | What to Look For |
|----|-------|------|------------------|
| R11 | Heading hierarchy | 1.3.1 | Skipped heading levels (h1 directly to h3) |
| R12 | Positive tabIndex | 2.4.3 | `tabIndex` > 0 (disrupts natural tab order) |
| R13 | Role without required attrs | 4.1.2 | `role="button"` without `tabIndex="0"` and keyboard handler |
| R14 | Missing reduced-motion | 2.3.3 | Animations without `prefers-reduced-motion` media query |
| R15 | Missing aria-live | 4.1.3 | Dynamic content updates (toasts, notifications) without `aria-live="polite"` or `"assertive"` |
| R16 | Missing skip-to-content | 2.4.1 | Pages with navigation but no skip link for keyboard users |

---

## Visual Design Checks

### Layout & Spacing

| ID | Check | Severity | What to Look For |
|----|-------|----------|------------------|
| V1 | Inconsistent spacing | Warning | Mix of spacing values within same component level (e.g., some cards `p-4`, others `p-6`) |
| V2 | Overflow issues | Warning | Content that breaks container bounds without `overflow-hidden` or scroll handling |
| V3 | Alignment problems | Warning | Elements visually misaligned within a flex/grid container |
| V4 | Z-index conflicts | Warning | Stacking context issues where overlays don't layer correctly |

### Typography

| ID | Check | Severity | What to Look For |
|----|-------|----------|------------------|
| V5 | Mixed font families | Warning | Unauthorized font families not in the active preset |
| V6 | Inconsistent weights | Warning | Same heading level using different weights across the page |
| V7 | Line height issues | Warning | Body text with `leading-tight` or `leading-none` — too cramped for readability |
| V8 | Missing font fallbacks | Suggestion | Custom fonts without system font fallback stack |

### Color & Contrast

| ID | Check | Severity | What to Look For |
|----|-------|----------|------------------|
| V9 | Contrast too low | Error | Text/background combinations below WCAG AA ratios |
| V10 | Missing hover/focus states | Warning | Interactive elements without visible state changes |
| V11 | Dark mode inconsistencies | Warning | Components that break or look wrong in dark mode |

### Component States

| ID | Check | Severity | What to Look For |
|----|-------|----------|------------------|
| V12 | Missing button states | Warning | Buttons without disabled, loading, hover, active, or focus styles |
| V13 | Missing form states | Warning | Form fields without error, success, or disabled states |
| V14 | Inconsistent borders/shadows | Suggestion | Mix of border and shadow styles at the same component level |
| V15 | Inconsistent icon sizing | Suggestion | Icons at different sizes within the same context |

---

## Guard Checks (for inline guard during work phase)

When the rams layer is active, these checks run silently during coding:

**Errors (block and explain):**
- R1-R5: Critical a11y violations — always flag before writing code
- R9-R10: Touch targets and contrast — measurable, non-negotiable
- V9: Low contrast — measurable WCAG violation

**Warnings (flag in response):**
- R6-R8: Serious a11y issues
- V1-V7, V10-V13: Visual quality issues

**Suggestions (mention if teaching mode allows):**
- R11-R16: Moderate a11y improvements
- V8, V14-V15: Polish items

---

## Teaching Moments

### Touch Targets
> "This button is `{size}` ({pixels}px) — WCAG 2.5.5 recommends at minimum 44x44px touch targets. Add `min-h-11` to ensure it's accessible on touch devices."

### Accessible Names
> "This icon-only button has no accessible name — screen readers will announce it as just 'button'. Add `aria-label=\"{suggested-label}\"` to describe its action."

### Color-Only Information
> "Status is communicated only through color here. ~4.5% of people have some form of color vision deficiency. Add an icon or text label alongside the color: {suggestion}."

### Semantic Handlers
> "This `<div onClick>` isn't keyboard accessible. Users navigating with Tab can't activate it. Either use `<button>` (preferred) or add `role=\"button\"`, `tabIndex={0}`, and `onKeyDown` handler."

### Contrast
> "The contrast ratio between `{foreground}` and `{background}` is approximately {ratio}:1. WCAG AA requires 4.5:1 for normal text and 3:1 for large text (18px+ or 14px+ bold). Try `{suggestion}` for better contrast."

### Reduced Motion
> "This animation runs unconditionally. Users with vestibular disorders may find motion disorienting. Wrap in `@media (prefers-reduced-motion: no-preference)` or use Tailwind's `motion-safe:` prefix."

---

## Standalone Output Format

When invoked via `/rams` (standalone a11y-only audit), use this format:

```
═══════════════════════════════════════════════════
RAMS DESIGN REVIEW: [filename]
═══════════════════════════════════════════════════

CRITICAL (X issues)
───────────────────
[A11Y] Line NN: Description
  <code snippet>
  Fix: Specific fix
  WCAG: X.X.X

SERIOUS (X issues)
──────────────────
[A11Y] Line NN: Description
  <code snippet>
  Fix: Specific fix
  WCAG: X.X.X

MODERATE (X issues)
───────────────────
...

VISUAL (X issues)
─────────────────
[VISUAL] Line NN: Description
  Fix: Specific fix

═══════════════════════════════════════════════════
SUMMARY: X critical, X serious, X moderate, X visual
A11y Score: XX/100
═══════════════════════════════════════════════════
```

When invoked as part of `/design-review` (composite audit), the output is integrated into the composite score as the **Accessibility** sub-score.
