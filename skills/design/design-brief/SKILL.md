---
name: design-brief
user_invocable: true
description: Generate a unified style brief from all active design layers before coding UI. Outputs craft constraints (measure, rhythm, scale), aesthetic rules (typography, color, spacing, elevation, motion), and a11y requirements (touch targets, contrast, ARIA patterns).
license: MIT
metadata:
  version: 2.0.0
  category: design
  domain: design-quality
keywords:
  - design brief
  - style brief
  - style constraints
  - before coding
  - design tokens
  - preset
  - bringhurst
  - accessibility
---

# Design Brief

Generate a unified style brief that constrains upcoming UI work using **all active layers**. Run this before writing any UI code.

## Setup

1. Load the design engine: `~/.claude/skills/design/design-quality/SKILL.md`
2. Follow the engine's **Step 1: Load Project Config** to determine active layers, aesthetic, strictness, and teaching mode
3. Load layer references for each active layer:
   - If bringhurst active → `~/.claude/skills/design/design-quality/layers/bringhurst.md`
   - If rams active → `~/.claude/skills/design/design-quality/layers/rams.md`
4. Load the aesthetic preset from `~/.claude/skills/design/design-quality/presets/<name>.md`

## Arguments

If an argument is provided (e.g., `design-brief stripe-vercel`), use that preset instead of the project default. Layer configuration still comes from the project's `CLAUDE.md`.

## Execution

Generate a unified brief with sections from **each active layer**:

### From Craft Layer (bringhurst) — if active

1. **Measure** — Maximum line length for body text, recommended Tailwind classes
2. **Vertical Rhythm** — Base unit derived from body line-height, spacing multiples
3. **Modular Scale** — Recommended type scale ratio and concrete sizes for this project
4. **Weight Palette** — Which 2-3 font weights to use and their roles
5. **Restraint Rules** — Max typefaces, earn-each-variation principle

### From Aesthetic Layer (preset) — if active

1. **Typography** — Allowed fonts, weight hierarchy, tracking, line-height
2. **Color** — Palette boundaries, semantic token usage, accent rules
3. **Spacing** — Grid system, component padding, section spacing
4. **Elevation** — Shadow hierarchy, border usage rules
5. **Motion** — Animation approach, timing, easing
6. **Component Patterns** — Card, button, navigation, form treatments
7. **Do/Don't** — Code examples from the preset

### From A11y Layer (rams) — if active

1. **Touch Targets** — Minimum sizes, recommended classes
2. **Contrast** — WCAG AA ratios, how to verify
3. **Focus** — Required focus indicator pattern
4. **ARIA** — Common patterns needed for the planned components
5. **Reduced Motion** — How to handle animations
6. **States** — Required component states (loading, empty, error, disabled)

## Output

```markdown
## Style Brief
**Active Layers:** [bringhurst, aesthetic, rams]
**Aesthetic:** [preset name or design-system]
**Strictness:** [relaxed / standard / strict]

---

### Craft Constraints (Bringhurst)

**Measure:** Body text max 65-75ch. Use `max-w-prose` or `max-w-2xl`.
**Rhythm Base:** [X]px (from body line-height [size]px × [ratio]).
  Spacing multiples: [X/2], [X], [2X], [3X] → Tailwind: [classes]
**Type Scale:** [ratio name] ([ratio]) → [concrete sizes]
**Weight Palette:** [weight1] (headings), [weight2] (emphasis), [weight3] (body)
**Restraint:** Max 2 font families. Max 3 weights per component.

---

### Aesthetic Rules ([preset name])

#### Typography
[Specific rules from preset]

#### Color
[Specific rules from preset]

#### Spacing
[Specific rules from preset]

#### Elevation
[Specific rules from preset]

#### Motion
[Specific rules from preset]

#### Component Patterns
[Specific rules from preset]

#### Do / Don't
[Code examples from preset]

---

### A11y Requirements (RAMS)

**Touch Targets:** `min-h-11` (44px) on all interactive elements.
**Contrast:** 4.5:1 normal text, 3:1 large text (18px+ or 14px bold+).
**Focus:** `focus:outline-none focus-visible:ring-2 focus-visible:ring-ring` on all interactive elements.
**ARIA Patterns:**
- Icon-only buttons → `aria-label="[action]"`
- Dynamic content → `aria-live="polite"`
- Lists → `role="list"` when styled
**Reduced Motion:** Use `motion-safe:` prefix on animations.
**Required States:** loading, empty, error for data-dependent components.
```

After generating, ask if the user wants to proceed with coding or adjust any constraints.

## Layer-Specific Notes

- If **only bringhurst** is active (no aesthetic), the brief focuses on craft fundamentals without preset-specific rules. Generic best practices replace preset sections.
- If **only rams** is active (no bringhurst), skip the craft constraints section.
- If **aesthetic is `none`**, skip the aesthetic section entirely. Craft and a11y constraints still apply.
- If **aesthetic is `design-system`**, analyze the project's design system tokens and generate the aesthetic section from those instead of a preset file.
