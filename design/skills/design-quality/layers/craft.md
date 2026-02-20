# Layer: Craft

<!-- Inspired by Robert Bringhurst's "The Elements of Typographic Style" -->

Typographic and spatial fundamentals. This layer enforces the invisible craft that makes interfaces feel "right" — even when users can't articulate why.

**Layer ID:** `craft`
**Default:** On (always active unless explicitly disabled)
**Applies to:** All platforms (web-first examples, concepts translate)

---

## Core Principles

### 1. Measure (Line Length)

The single most impactful readability factor. A line that's too long forces the eye to travel too far; too short, and reading becomes choppy.

- **Body text:** 45-75 characters per line. The sweet spot is 66ch (Bringhurst's ideal).
- **Single-column max:** Never exceed 80ch.
- **Multi-column:** Narrower measures (35-50ch) acceptable when columns are side by side.
- **Headings:** Can exceed body measure, but rarely need to.

**Web equivalents:**
| Tailwind Class | Width | Approx Characters (at 16px) |
|---------------|-------|----------------------------|
| `max-w-prose` | 65ch | 65 (ideal) |
| `max-w-xl` | 576px | ~64ch |
| `max-w-2xl` | 672px | ~75ch |
| `max-w-3xl` | 768px | ~85ch (too wide) |
| `max-w-4xl` | 896px | ~100ch (far too wide) |

### 2. Vertical Rhythm

All vertical spacing should relate to a base unit, creating a consistent "beat" down the page — like music's time signature.

- **Establish a base:** Typically the body line-height (e.g., 24px at 16px/1.5).
- **Derive from the base:** Margins, padding, gaps should be multiples or fractions of this unit.
- **Heading spacing:** Space above a heading should be greater than space below it (the heading "belongs to" what follows).
- **Consistency over precision:** The exact base matters less than using it consistently.

**Common base units (web):**
| Body Size | Line Height | Base Unit | Common Multiples |
|-----------|-------------|-----------|-----------------|
| 14px (text-sm) | 1.5 = 21px | 20px (round) | 10, 20, 40, 60 |
| 16px (text-base) | 1.5 = 24px | 24px | 12, 24, 48, 72 |
| 16px (text-base) | 1.7 = 27px | 28px (round) | 14, 28, 56 |

### 3. Modular Scale

Font sizes should follow a mathematical ratio rather than arbitrary values. This creates natural hierarchy — sizes that "belong together."

- **Minor third (1.2):** Subtle, good for dense UI (14, 16.8, 20.2, 24.2)
- **Major third (1.25):** Balanced, good general purpose (14, 17.5, 21.9, 27.3)
- **Perfect fourth (1.333):** Pronounced, good for content sites (14, 18.7, 24.9, 33.2)
- **Golden ratio (1.618):** Dramatic, good for editorial (14, 22.7, 36.6, 59.2)

**Rule of thumb:** If you can't name the ratio between your font sizes, reconsider the scale.

**Checking a scale:** Take any two adjacent sizes. If `larger / smaller` produces a consistent ratio (within ~0.1), the scale is coherent. If the ratio jumps (1.2 then 1.7 then 1.1), the scale is arbitrary.

### 4. Weight Hierarchy

Meaning through weight — not just through size. A restrained weight palette forces clarity.

- **Maximum 3 weights per component:** e.g., bold (headings), medium (labels/emphasis), regular (body).
- **Consistent assignment:** Every heading level should use the same weight across the interface.
- **Weight carries meaning:** Bold = important. Medium = structural. Regular = content. Don't invert this.
- **Avoid extremes:** `font-thin` (100) and `font-black` (900) rarely belong in UI. Stick to 400-700 range.

### 5. Proportion & Whitespace

Whitespace is structural, not decorative. It defines relationships between elements.

- **Content-to-margin ratio:** Generous margins signal importance and quality. Reference classical proportions (Tschichold's golden canon, Van de Graaf's canon) as intuition guides, not rigid rules.
- **Proximity principle:** Related elements should be closer together than unrelated ones. The gap between a heading and its paragraph should be less than the gap between that paragraph and the next heading.
- **Breathing room:** When in doubt, add space. Dense UI is a last resort for information-heavy tools, not a default.
- **Asymmetric spacing:** Top and bottom padding don't have to be equal. Content often benefits from more space above section headings than below them.

### 6. Restraint

Good typography doesn't call attention to itself. It serves the content.

- **Fewer typefaces, better used:** One typeface family is usually enough. Two maximum (e.g., sans for UI + mono for code). Three is almost never justified.
- **Invisible design:** If someone notices the typography, it's probably too clever.
- **Consistency over novelty:** Every deviation from the established pattern should earn its place. A different font size, weight, or family should signal something meaningful.
- **Earn each variation:** Before adding a new text style, ask: "Can an existing style serve this purpose?"

---

## Guard Checks

These checks are applied during the **work** phase (inline guard) and the **review** phase (explicit audit).

### C1. Measure Violation
**Severity:** Warning
**What to look for:** Body text containers wider than 75ch without multi-column layout or sidebar
**Web signals:** `max-w-3xl`, `max-w-4xl`, `max-w-5xl`, `max-w-6xl`, `max-w-7xl`, `max-w-full`, `w-full` on text-heavy containers without a constraining parent
**Fix:** Use `max-w-prose` (65ch) or `max-w-2xl` (672px, ~75ch) for single-column body text
**Skip when:** Container holds a data table, code block, dashboard grid, or multi-column layout

### C2. Vertical Rhythm Break
**Severity:** Warning
**What to look for:** Spacing values (line-height, margin, padding) within a component that don't share a common divisor
**Web signals:** Mix of values like `mb-3` (12px), `mt-5` (20px), `gap-7` (28px) — these share no common unit
**Fix:** Choose a base unit (typically body line-height) and derive all spacing from it
**Skip when:** Spacing is dictated by a design system's explicit token set

### C3. Orphaned Heading
**Severity:** Warning
**What to look for:** A heading element at the end of a container with less than 2 lines of body content following it
**Web signals:** `<h2>` or `<h3>` as the last or near-last child in a section/div
**Fix:** Ensure headings always have enough content beneath them, or restructure the layout
**Skip when:** Heading is intentionally standalone (e.g., a section title in a sidebar nav)

### C4. Scale Violation
**Severity:** Suggestion
**What to look for:** Font sizes that don't follow a discernible mathematical ratio
**Web signals:** Arbitrary jumps like `text-sm` (14px) to `text-2xl` (24px) with nothing in between — a 1.71x jump that skips the natural intermediate step
**Fix:** Identify a modular scale ratio and add intermediate sizes. For 14→24, a perfect fourth (1.333) suggests 14 → 18.7 → 24.9
**Skip when:** Only 2 sizes are used (body + heading) — a ratio is implicit

### C5. Weight Sprawl
**Severity:** Suggestion
**What to look for:** More than 3 distinct font-weight values within a single component
**Web signals:** `font-normal`, `font-medium`, `font-semibold`, `font-bold` all appearing in one component
**Fix:** Reduce to a palette of 2-3 weights with clear roles (heading, emphasis, body)
**Skip when:** Component is a rich text renderer or documentation page with legitimate weight variety

### C6. Typeface Sprawl
**Severity:** Warning
**What to look for:** More than 2 font families in use across the interface
**Web signals:** `font-sans`, `font-serif`, `font-mono` + a custom `font-display` all in active use (excluding code blocks)
**Fix:** Reduce to 1 primary family + 1 utility family (usually monospace for code)
**Skip when:** The third font is used exclusively in code blocks or a single decorative display element in the preset

---

## Teaching Moments

Templates for educational explanations during work and review. Use the **first time** a concept is violated in a session (teaching mode: `normal`), every time (teaching mode: `verbose`), or never (teaching mode: `quiet`).

### Measure
> "This paragraph container is `{class}` ({width}px) — at {font-size}px, that's ~{chars} characters per line. Bringhurst recommends 45-75ch for comfortable reading. Consider `max-w-prose` (65ch) or `max-w-2xl` (~75ch)."

### Vertical Rhythm
> "Spacing in this component uses {values} — these don't share a common base unit. If your body line-height is {line-height}px, try deriving margins from that: {suggestions}. Consistent rhythm makes the layout feel composed rather than assembled."

### Modular Scale
> "The type scale jumps from {small}px to {large}px — a {ratio}x ratio. This feels {assessment}. A {scale-name} ({scale-ratio}) progression would be {suggested-sizes}, adding an intermediate size for {purpose}."

### Weight Hierarchy
> "This component uses {count} font weights ({weights}). Bringhurst recommends restraint — 2-3 weights that carry clear meaning: bold for headings, medium for emphasis, regular for body."

### Typeface
> "Three font families are in use ({families}). Typography works best with restraint — one family for content, one utility family (mono for code). Can {least-used} be replaced by {primary}?"

### Whitespace
> "The gap between this heading and its content ({below}) is larger than the gap above the heading ({above}). Flip this — headings should be closer to what they introduce, creating a visual bond. Try {suggested-above} above and {suggested-below} below."
