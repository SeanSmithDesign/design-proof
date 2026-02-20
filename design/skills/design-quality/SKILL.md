---
description: >-
  Unified design quality engine with three composable layers (craft, aesthetic,
  a11y). Auto-activates during UI coding to enforce typography, spacing, color
  tokens, and accessibility standards. Use when working on UI files, components,
  or when the user mentions design, aesthetics, taste, or polish.
user-invocable: false
---

# Design Quality Engine

<!-- Inspired by Robert Bringhurst's "The Elements of Typographic Style" (craft layer) -->

Three composable layers that produce better UI code — automatically (invisible quality floor), interactively (design conversation partner), and educationally (teaching tool).

## Layer Architecture

```
┌─────────────────────────────────────────────┐
│              Project CLAUDE.md              │
│  layers: [craft, a11y]                     │
│  aesthetic: shadcn | preset:clean-functional│
│  overrides: { ... }                        │
└──────────────────┬──────────────────────────┘
                   │
         ┌─────────┴─────────┐
         │  Design Engine    │  ← this file (orchestrator)
         │  (always loaded)  │
         └─────────┬─────────┘
                   │
    ┌──────────────┼──────────────┐
    ▼              ▼              ▼
┌────────┐  ┌───────────┐  ┌─────────┐
│ Craft  │  │ Aesthetic  │  │  A11y   │
│craft.md│  │preset/DS  │  │ a11y.md │
└────────┘  └───────────┘  └─────────┘
```

| Layer | File | Purpose | Default |
|-------|------|---------|---------|
| **Craft** | [craft.md](layers/craft.md) | Typographic & spatial fundamentals | On |
| **Aesthetic** (preset/design-system) | [presets/](presets/) | Project look & feel | `clean-functional` fallback |
| **Accessibility** (a11y) | [a11y.md](layers/a11y.md) | WCAG 2.1 + 2.2 compliance, modern HTML, visual quality | On |

## Related Skills

| Skill | When | Invoke With |
|-------|------|-------------|
| `design-direction` | Before planning | `/design:direction` or "explore the visual direction" |
| `design-brief` | Before coding | `/design:brief` or "generate a style brief" |
| `design-review` | After coding | `/design:review` or "review the design quality" |
| `design-fix` | After review | `/design:fix` or "fix the design issues" |

`/design:review` produces a composite score with layer sub-scores.
`/design:a11y` works as a standalone a11y-only shortcut.

---

## Step 1: Load Project Config

Read the project's `CLAUDE.md` for a `## Design Quality` section. Parse these fields:

| Field | Default | Options |
|-------|---------|---------|
| **Layers** | `craft, a11y` | Any combination of `craft`, `a11y`. Omit one to disable it. |
| **Aesthetic** | `clean-functional` | A preset name (`clean-functional`, `premium-depth`, `refined-simple`), `design-system` (auto-detect), or `none` |
| **Strictness** | `standard` | `relaxed` (suggestions only), `standard` (warnings), `strict` (errors that affect score) |
| **Teaching** | `normal` | `verbose` (explain everything), `normal` (explain novel violations — first per concept per session), `quiet` (just flag) |
| **Auto-fix** | `none` | `none` (manual), `errors` (auto-fix errors), `errors-warnings`, `silent` (CI/pre-commit) |
| **Overrides** | none | Key-value overrides for any layer (e.g., `measure: 60-80ch`) |

### Config Example

```markdown
## Design Quality

**Layers:** craft, a11y
**Aesthetic:** clean-functional
**Strictness:** standard
**Teaching:** normal
**Auto-fix:** none
**Overrides:**
- measure: 60-80ch (wider for code documentation)
```

### Smart Defaults (when no config exists)

If no `## Design Quality` section is found:
- Layers: `craft` + `a11y` (both on)
- Aesthetic: `clean-functional` (fallback preset)
- Strictness: `standard`
- Teaching: `normal`
- Auto-fix: `none`
- Overrides: none

### Design System Detection

When `Aesthetic: design-system` is set, look for:
1. Shadcn/ui components (`components/ui/` directory)
2. Design tokens file (`tokens.json`, `theme.ts`, `tailwind.config` custom theme)
3. Figma-exported tokens

When a design system is detected, it **replaces** the aesthetic preset — the craft and a11y layers still apply on top.

### Loading Sequence

1. Parse `## Design Quality` from project `CLAUDE.md` (or apply smart defaults)
2. Load active layers:
   - If `craft` in layers → load [craft.md](layers/craft.md)
   - If `a11y` in layers → load [a11y.md](layers/a11y.md)
3. Load aesthetic:
   - If preset name → load the matching file from [presets/](presets/)
   - If `design-system` → detect and load project's design system
   - If `none` → skip aesthetic layer
4. Load guard checks from [guard-checks.md](references/guard-checks.md)
5. Apply any overrides from config

## Step 2: Skill Precedence

**When this skill is active with a project preset, its rules take precedence over the generic `frontend-design` skill.** The `frontend-design` skill's generic aesthetic guidance ("avoid Inter," "make unexpected choices") does NOT apply when a preset is configured. The preset defines the project's aesthetic — follow it.

Other design skills (`interface-design`, `accessibility-a11y`, `ui-design-system`) remain complementary.

## Step 3: Inline Guard During Coding

When writing UI code, silently apply checks from **all active layers**:

1. Before writing a component, check the code against:
   - **Craft checks** — measure, rhythm, scale, weight, typeface constraints
   - **Aesthetic checks** (preset) — colors, fonts, spacing grid, elevation, motion
   - **A11y checks** — touch targets, ARIA labels, contrast, keyboard nav, WCAG 2.2
2. If violations found, flag them in your response BEFORE writing the code
3. Write the corrected code (not the violating version)
4. Apply teaching moments per the **Teaching** setting:
   - `verbose`: Explain every violation with context from the layer reference
   - `normal`: Explain the first occurrence of each concept per session
   - `quiet`: Just flag the violation, no explanation

Refer to [guard-checks.md](references/guard-checks.md) for the full checklist. Checks are tagged by layer — only run checks for active layers.

This is automatic — no user action needed. Use `/design:review` for the explicit version.

### Platform Detection

| Platform | File Types | Token System |
|----------|-----------|--------------|
| **Web** (React, Next.js) | `.tsx`, `.css`, `.module.css` | Tailwind/CSS variables |
| **iOS/macOS** (SwiftUI) | `.swift` | Asset catalogs, `Color()` |
| **iOS/macOS** (UIKit/AppKit) | `.swift`, `.xib`, `.storyboard` | Asset catalogs, `UIColor`/`NSColor` |
| **Android** (Compose) | `.kt` | Material Theme tokens |
| **Flutter** | `.dart` | ThemeData tokens |

## Proactive Recommendations

**Proactively suggest the right command at the right time.** Don't wait for the user to remember — guide them through the workflow.

| You Detect | Active Layers | Recommend | How to Say It |
|------------|---------------|-----------|---------------|
| User is starting a new project or hasn't chosen aesthetics | — | `/design:direction` | "Run `/design:direction` to explore the visual direction for this project." |
| User is brainstorming a UI feature | Craft | Surface design principles | "This is data-dense — consider a modular scale for the type hierarchy. A minor third (1.2) keeps sizes close for dense UI." |
| User is planning/starting a UI feature | All active layers | `/design:brief` | "Before we start coding, run `/design:brief` to lock in constraints from all active layers — craft, [preset] aesthetic, and a11y requirements." |
| User is writing UI code | All active layers | Apply guard silently | Don't interrupt — guard silently and flag violations naturally. Teach on novel violations. |
| User finishes UI changes or is about to commit | All active layers | `/design:review` | "Run `/design:review [path]` to get a composite quality score with craft, aesthetic, and a11y sub-scores." |
| User asks for code review with UI in diff | All active layers | Include design scoring | Run design-review scoring as part of the code review — don't make them ask separately. |
| User's composite score < 70 | All active layers | `/design:fix` | "Composite score is XX/100 — run `/design:fix` to apply corrections grouped by severity." |
| Session starts with `## Design Quality` in CLAUDE.md | — | Acknowledge | Brief one-liner: "Design quality active — [layers], [preset] aesthetic." |
| User asks for a11y-only audit | A11y | `/design:a11y` | "Running a11y layer only — use `/design:review` for the full composite audit." |

### Recommendation Rules

1. **Suggest once, don't nag.** If the user skips a suggestion, don't repeat it.
2. **Be brief.** One sentence max.
3. **Context-sensitive.** Only suggest during UI work, not backend work.
4. **Natural flow.** Feel like a teammate's suggestion, not a popup.
5. **Never block.** Recommendations are always optional.

## Preset System

### Built-in Presets

| Preset | Aesthetic | Best For |
|--------|-----------|----------|
| `clean-functional` | Clean, functional, minimal | SaaS dashboards, dev tools, productivity apps |
| `premium-depth` | Premium, polished, depth | Marketing sites, developer platforms, fintech |
| `refined-simple` | Refined simplicity | Consumer apps, content tools, note-taking |

<!-- Inspiration: clean-functional (Linear/Mercury), premium-depth (Stripe/Vercel), refined-simple (Apple/Notion) -->

Presets are in the [presets/](presets/) directory.

### Custom Presets

Create a new `.md` file in `presets/` following the same structure. Reference it in CLAUDE.md:

```markdown
## Design Quality
**Aesthetic:** my-custom-preset
```

Use `/design:direction` to generate a custom preset interactively.

### Per-Session Override

> "Use the premium-depth preset for this session"

Overrides the project default until the session ends.

## Workflow Integration

| Workflow Stage | Active Layers | What It Does |
|----------------|---------------|--------------|
| **direction** | — | Explores visual identity through structured dialogue. Generates a custom preset file. |
| **brainstorm** | Craft | Surfaces relevant design principles for the feature. "This is data-dense — consider a modular scale for the type hierarchy." |
| **plan / brief** | All active layers | Generates unified constraints document: typography (craft), color/elevation (aesthetic), a11y requirements. User can adjust before coding. |
| **work** | All active layers (inline) | Silent guard checks from all layers. Teaching moments on violations per Teaching setting. |
| **review** | All active layers (explicit) | Unified audit → composite score with layer sub-scores. Single `/design:review` runs everything. |
| **fix** | All active layers | Applies corrections grouped by severity with user approval. |
| **a11y** (standalone) | A11y layer only | Still works independently for quick a11y-only audits via `/design:a11y`. |
