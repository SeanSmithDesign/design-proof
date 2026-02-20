---
description: >-
  Creative exploration for defining a project's visual direction. Guides through
  structured dialogue covering typography, color, spacing, then generates a custom
  preset file and updates CLAUDE.md.
argument-hint: "[amend | variant | fresh]"
disable-model-invocation: true
---

# Design Direction

Explore and define your project's visual identity through structured dialogue. Generates a custom preset file that integrates with the design quality engine.

## Setup

1. Check if the project already has a custom preset:
   - Look for `.claude/presets/*.md` in the project root
   - Check the project's `CLAUDE.md` for `**Aesthetic:**` configuration
2. If an existing preset is found, ask before proceeding (see Arguments)

## Arguments

| Argument | Behavior |
|----------|----------|
| (none) | Auto-detect: if preset exists, ask what to do. If no preset, start fresh. |
| `amend` | Modify the existing preset — edit specific sections without starting over |
| `variant` | Create a new preset based on the existing one — fork and diverge |
| `fresh` | Start from scratch — ignore any existing preset |

**Partial invocation:** `/design:direction colors` modifies only the color section of an existing preset.

## Execution

### Step 1: Conversation (5 questions max)

Use **AskUserQuestion** for structured exploration. Ask one question at a time.

#### Q1. What kind of interface?

| Option | Description |
|--------|-------------|
| Data-dense tool | Dashboards, analytics, dev tools — high info density, functional |
| Content app | Notes, docs, reading — text-forward, calm |
| Marketing / landing | Showcase, conversion — bold, persuasive |
| Transactional | E-commerce, forms, workflows — clear, trustworthy |

#### Q2. How should it feel?

| Mood | Characteristics |
|------|----------------|
| **Quiet** | Near-monochrome, system fonts, generous whitespace, no stagger animations. The interface disappears. |
| **Confident** | Distinctive display font, dark backgrounds, generous sizing. The interface commands attention. |
| **Warm** | Rounded corners, warm neutrals, friendly typography. The interface feels approachable. |
| **Sharp** | Monospace accents, tight spacing, high contrast. The interface feels technical and precise. |
| **Bold** | Saturated colors, large type, strong gradients. The interface makes a statement. |

#### Q3. Name 1-3 apps you admire visually

Open-ended with suggestions. This is the primary anti-slop mechanism — it grounds the aesthetic in real taste rather than generic defaults.

**If references match a starter preset:** Offer it directly.
- Linear, Mercury, Raycast → "These align closely with the `clean-functional` preset. Want to use it as-is, or customize from there?"
- Stripe, Vercel, Resend → "These match `premium-depth`. Use it or customize?"
- Apple, Notion, Things → "These match `refined-simple`. Use it or customize?"

#### Q4. Light or dark?

| Option | Maps to |
|--------|---------|
| Light-first | White/near-white backgrounds, dark text. Dark mode as secondary. |
| Dark-first | Dark backgrounds, light text. The primary experience. |
| Both equally | True dual-mode with CSS variables. Neither is secondary. |

#### Q5. Accent color? (optional)

| Option | Behavior |
|--------|----------|
| Yes — provide hex or description | Use as primary accent throughout |
| No — auto-pick based on mood | Select from mood-appropriate palette |

### Step 2: Generate Preset

Write a preset file matching the 9-section schema used by built-in presets:

1. **Philosophy** — 2-3 sentences capturing the aesthetic intent. Must contain one unique "signature move" phrase. Bad: "Clean, modern, and professional." Good: "Quiet authority. The interface earns trust through what it omits."
2. **Reference Apps** — The apps from Q3, with specific study notes
3. **Typography** — Font choices, heading hierarchy, body settings, anti-patterns
4. **Color** — Palette philosophy, neutrals, accent, status colors, anti-patterns
5. **Spacing** — Grid, component padding, section spacing, anti-patterns
6. **Elevation** — Shadow/border philosophy, levels, anti-patterns
7. **Motion** — Animation philosophy, timing, key moments, anti-patterns
8. **Component Patterns** — Cards, buttons, navigation, forms
9. **Do / Don't Examples** — Code examples (3-4 categories)

### Anti-Slop Rules

1. **Never converge** — Maintain variety across generations:
   - Rotate neutral families (stone/gray/slate/zinc)
   - Vary accent colors per mood
   - Don't always default to Inter

2. **Commit to an extreme** — Each mood has a strong personality:
   - Quiet should be aggressively minimal
   - Bold should lean hard into saturation
   - The enemy is timid middle-ground

3. **Context-specific** — The Philosophy section must read as written for THIS project, not as a generic template.

### Font Mapping (per mood)

| Mood | Headings | Body |
|------|----------|------|
| Quiet | Inter, SF Pro, system-ui | Same (consistency is the point) |
| Confident | Satoshi, GT Walsheim, Geist | Geist, Inter |
| Warm | Instrument Sans, Plus Jakarta Sans | Same or slightly different weight |
| Sharp | JetBrains Mono (display), Space Grotesk | Geist, Inter |
| Bold | Clash Display, Syne, Cabinet Grotesk | Geist, DM Sans |

### Step 3: Write Files

1. **Write preset:** `<project-root>/.claude/presets/<name>.md`
   - Name derived from the mood + interface type (e.g., `quiet-dashboard.md`, `bold-landing.md`)
   - Follow the exact 9-section schema from built-in presets

2. **Update CLAUDE.md:** Add or update the `## Design Quality` section:
   ```markdown
   ## Design Quality

   **Layers:** craft, a11y
   **Aesthetic:** <preset-name>
   **Strictness:** standard
   **Teaching:** normal
   ```

### Step 4: Confirm

Show a summary of what was generated:

```
Direction set!

Preset: .claude/presets/<name>.md
Mood: [mood]
Fonts: [heading] / [body]
Accent: [color]
Dark mode: [yes/no/both]

CLAUDE.md updated with `Aesthetic: <name>`.

Next: Run `/design:brief` to generate constraints from this preset.
```

## Edge Cases

- **Contradictory answers** (e.g., "Quiet" mood + Bold reference apps): Surface the tension and offer two interpretations as variants
- **Existing preset + amend:** Show current preset summary, ask which sections to change, preserve unchanged sections
- **No opinion on Q5:** Auto-select based on mood (Quiet → desaturated blue, Confident → electric blue, Warm → coral, Sharp → emerald, Bold → vivid purple)

## Preset Resolution Order

When loading a preset, the orchestrator checks:
1. `<project-root>/.claude/presets/<name>.md` (project-local, highest priority)
2. Plugin built-in: [presets/](../design-quality/presets/) (fallback)

This means custom presets from `/design:direction` automatically take priority over built-in ones.
