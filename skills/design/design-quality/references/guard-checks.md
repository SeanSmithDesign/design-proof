# Guard Checks

Quick-reference checklist for Guard mode. Before writing any UI code, self-check against these rules. **Only run checks for active layers.**

**Layer tags:** Each check is tagged with its layer. Skip checks for inactive layers.
- `[aesthetic]` — Requires an active preset or design system
- `[bringhurst]` — Craft layer (typography & spatial fundamentals)
- `[rams]` — Accessibility & visual quality layer
- `[preset:<name>]` — Preset-specific exception applies

**Platform key:** Examples show Web (React/Tailwind) by default. Apply equivalent checks for Swift (`Color()` literals, `.font()` modifiers), Compose (`Color(0xFF...)`, `MaterialTheme`), and Flutter (`Color()`, `ThemeData`).

---

## Static Checks (Can be verified by reading the code)

### 1. No Hardcoded Colors `[aesthetic]`
**Severity:** Error
**Look for:** Hardcoded color values in any platform
**Fix:** Replace with project semantic tokens

| Platform | Bad | Good |
|----------|-----|------|
| Web | `text-[#B87333]` | `text-primary` |
| SwiftUI | `Color(red: 0.72, green: 0.45)` | `Color("primary")` |
| Compose | `Color(0xFFB87333)` | `MaterialTheme.colorScheme.primary` |
| Flutter | `Color(0xFFB87333)` | `Theme.of(context).colorScheme.primary` |

### 2. No Tailwind Palette Colors `[aesthetic]`
**Severity:** Error
**Look for:** `bg-blue-500`, `text-red-400`, `border-green-300`, `text-emerald-*`, `bg-indigo-*`
**Fix:** Use semantic tokens: `bg-primary`, `text-destructive`, `text-success`, `border-border`
```tsx
// Bad:  text-green-500
// Good: text-success
```

### 3. No Hardcoded Light/Dark Values `[aesthetic]`
**Severity:** Error
**Look for:** `bg-white`, `bg-black`, `text-white`, `text-black`, `dark:bg-*` overrides
**Fix:** Use CSS variable-backed tokens that auto-adapt: `bg-background`, `text-foreground`, `bg-card`
**Preset exceptions:**
- `apple-notion`: `bg-white` is acceptable when used as the primary background in a light-first context where the preset explicitly calls for white backgrounds. Still flag `text-white` and `dark:bg-*` overrides.
- `stripe-vercel`: `text-white` on dark backgrounds (`bg-gray-950`, `bg-black`) is the preset's primary pattern. Still flag `bg-white` as primary background (prefer `bg-gray-950`).
```tsx
// Bad:  bg-white dark:bg-gray-900
// Good: bg-background
// Exception (apple-notion): bg-white on root/section background = OK
```

### 4. Font Family Compliance `[aesthetic]`
**Severity:** Error
**Look for:** Unauthorized `font-*` classes or font imports not in preset
**Fix:** Use only fonts specified in the active preset
```tsx
// Bad (if preset says Inter-only):  font-serif on a button label
// Good: font-sans (maps to Inter)
```

### 5. Touch Target Size `[rams]`
**Severity:** Error
**Look for:** Interactive elements (`button`, `a`, clickable `div`) with height < 44px
**Fix:** Add `min-h-11` (44px) or ensure `h-11`+ / `py-3`+ on buttons
```tsx
// Bad:  <button className="h-8 px-2">
// Good: <button className="min-h-11 px-4">
```

### 6. Accessible Names `[rams]`
**Severity:** Error
**Look for:** Icon-only buttons or links without `aria-label`
**Fix:** Add `aria-label="Description"` to any element with only an icon
```tsx
// Bad:  <button><X className="w-4 h-4" /></button>
// Good: <button aria-label="Close"><X className="w-4 h-4" /></button>
```

---

## Pattern Checks (Require matching against preset conventions)

### 7. Spacing Grid Alignment `[aesthetic]`
**Severity:** Warning
**Look for:** Padding/margin/gap values off the 8px grid (e.g., `p-5`, `gap-3.5`, `m-7`)
**Fix:** Snap to nearest 4px/8px increment
```tsx
// Bad:  p-5 (20px), gap-3.5 (14px)
// Good: p-4 (16px) or p-6 (24px), gap-4 (16px)
```
**Note:** `p-3` (12px) is acceptable for fine adjustments on the 4px sub-grid.

### 8. Elevation Hierarchy `[aesthetic]`
**Severity:** Warning
**Look for:** Shadow usage that doesn't match preset levels. Mixing borders and shadows on cards.
**Fix:** Follow preset's elevation system
**Preset exceptions:**
- `apple-notion`: Borders WITHOUT shadows is the expected pattern (hairline borders, no shadow on cards). Only flag borders + heavy shadows together.
- `stripe-vercel`: Borders + shadows together is expected (layered depth is the preset's signature).
```tsx
// Bad (linear-mercury): border border-gray-200 shadow-lg
// Good (linear-mercury): shadow-xs hover:shadow-sm
```

### 9. Typography Weight Consistency `[aesthetic]` `[bringhurst]`
**Severity:** Warning
**Look for:** More than 3 font weights in a single component. Headings with inconsistent weight.
**Fix:** Stick to the preset's weight hierarchy
```tsx
// Bad:  h2 font-bold, another h2 font-semibold, body font-thin
// Good: h2 always font-semibold, body always font-normal
```

### 10. Transition on Interactive States `[aesthetic]`
**Severity:** Warning
**Look for:** Hover/focus states that change without `transition-*`
**Fix:** Add appropriate transition class
```tsx
// Bad:  hover:shadow-md (no transition)
// Good: hover:shadow-md transition-shadow duration-150
```

---

## Craft Checks (Bringhurst layer — typographic & spatial fundamentals)

### 11. Measure Violation `[bringhurst]`
**Severity:** Warning
**Look for:** Body text containers wider than 75ch without multi-column layout or sidebar
**Web signals:** `max-w-3xl`+, `max-w-full`, `w-full` on text-heavy containers
**Fix:** Use `max-w-prose` (65ch) or `max-w-2xl` (~75ch) for single-column body text
**Skip when:** Container holds a data table, code block, dashboard grid, or multi-column layout

### 12. Vertical Rhythm Break `[bringhurst]`
**Severity:** Warning
**Look for:** Spacing values within a component that don't share a common divisor
**Web signals:** Mix of `mb-3` (12px), `mt-5` (20px), `gap-7` (28px) — no common base
**Fix:** Choose a base unit (typically body line-height) and derive all spacing from it
**Skip when:** Spacing is dictated by a design system's explicit token set

### 13. Orphaned Heading `[bringhurst]`
**Severity:** Warning
**Look for:** Heading at end of container with < 2 lines of body content following
**Fix:** Ensure headings always have content beneath them, or restructure layout
**Skip when:** Heading is intentionally standalone (e.g., sidebar nav title)

### 14. Scale Violation `[bringhurst]`
**Severity:** Suggestion
**Look for:** Font sizes that don't follow a discernible mathematical ratio
**Web signals:** Jump from `text-sm` (14px) to `text-2xl` (24px) — 1.71x with no intermediate
**Fix:** Identify a modular scale ratio and add intermediate sizes
**Skip when:** Only 2 sizes used (body + heading)

### 15. Weight Sprawl `[bringhurst]`
**Severity:** Suggestion
**Look for:** More than 3 distinct font-weight values within a single component
**Fix:** Reduce to 2-3 weights with clear roles (heading, emphasis, body)
**Skip when:** Component is a rich text renderer or documentation page

### 16. Typeface Sprawl `[bringhurst]`
**Severity:** Warning
**Look for:** More than 2 font families in use across the interface
**Fix:** Reduce to 1 primary family + 1 utility (mono for code)
**Skip when:** Third font is exclusively for code blocks or a single preset display element

---

## Accessibility Checks (RAMS layer)

### 17. Focus Indicators `[rams]`
**Severity:** Error
**Look for:** `outline-none` or `focus:outline-none` without `focus-visible:ring-*` replacement
**Fix:** Add visible focus indicator: `focus-visible:ring-2 focus-visible:ring-ring`
```tsx
// Bad:  focus:outline-none
// Good: focus:outline-none focus-visible:ring-2 focus-visible:ring-ring
```

### 18. Color-Only Information `[rams]`
**Severity:** Warning
**Look for:** Status communicated only through color (green dot = active, red = error) without icon or text
**Fix:** Add icon or text label alongside color indicator

### 19. Reduced Motion `[rams]`
**Severity:** Warning
**Look for:** Animations without `prefers-reduced-motion` consideration
**Fix:** Use `motion-safe:` Tailwind prefix or `@media (prefers-reduced-motion: reduce)` query

### 20. Missing aria-live `[rams]`
**Severity:** Warning
**Look for:** Dynamic content (toasts, notifications, live counts) without `aria-live` region
**Fix:** Add `aria-live="polite"` (most cases) or `aria-live="assertive"` (urgent alerts)

---

## Judgment Checks (Require visual/contextual assessment)

### 21. Visual Hierarchy Clarity `[aesthetic]`
**Severity:** Warning
**Look for:** Multiple elements competing for attention at the same visual level
**Fix:** Differentiate via size, weight, or color. One clear focal point per section.

### 22. Color Accent Restraint `[aesthetic]`
**Severity:** Suggestion
**Look for:** More accent-colored elements than the preset recommends
**Fix:** Reduce accent usage. Use neutral colors for secondary elements.

### 23. Hover State Presence `[aesthetic]`
**Severity:** Suggestion
**Look for:** Interactive elements without visible hover feedback
**Fix:** Add hover state (background shift, shadow lift, or color change)

### 24. Empty/Loading/Error States `[rams]`
**Severity:** Suggestion
**Look for:** Components that display data without handling no-data, loading, or error conditions
**Fix:** Add appropriate states (skeleton, spinner, empty message, error message)

### 25. Semantic HTML `[rams]`
**Severity:** Suggestion
**Look for:** `<div>` used where semantic elements would be more appropriate
**Fix:** Use `<section>`, `<nav>`, `<main>`, `<article>`, `<header>`, `<footer>` as appropriate

---

## Quick Decision Tree

```
Writing a component? Check active layers:

[aesthetic] layer active?
├─ Hex color? → ERROR. Use semantic token.
├─ bg-white/text-black? → ERROR (check preset exceptions). Use CSS variable.
├─ Uses Tailwind palette color? → ERROR. Use semantic token.
├─ Spacing off 8px grid? → WARNING. Snap to grid.
└─ No transition on hover? → WARNING. Add transition.

[bringhurst] layer active?
├─ Text container > 75ch? → WARNING. Constrain measure.
├─ Spacing values share no common base? → WARNING. Establish rhythm.
├─ Font sizes skip scale steps? → SUGGESTION. Fill the scale.
└─ > 3 font weights in component? → SUGGESTION. Reduce weight palette.

[rams] layer active?
├─ Button/link < 44px? → ERROR. Add min-h-11.
├─ Icon-only interactive? → ERROR. Add aria-label.
├─ focus:outline-none without ring? → ERROR. Add focus-visible indicator.
├─ Color-only status? → WARNING. Add icon/text.
├─ No reduced-motion handling? → WARNING. Add motion-safe prefix.
└─ Missing hover state? → SUGGESTION.

All clear? → Write the code.
```
