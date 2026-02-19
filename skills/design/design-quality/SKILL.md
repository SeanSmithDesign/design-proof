---
name: design-quality
description: Unified design quality engine. Three composable layers — Craft (bringhurst), Aesthetic (preset/design-system), Accessibility (rams) — activate at the right workflow stage to produce better UI code automatically.
license: MIT
metadata:
  version: 2.0.0
  category: design
  domain: design-quality
  platforms: Web, iOS, macOS, Android, Cross-platform
  techStack: React, Tailwind CSS, Next.js, SwiftUI, UIKit, AppKit, Jetpack Compose, Flutter
keywords:
  - design quality
  - aesthetic
  - taste
  - polish
  - style guide
  - visual consistency
  - UI
  - component
  - design system
  - typography
  - accessibility
  - bringhurst
  - craft
---

# Design Quality Engine

Three composable layers that produce better UI code — automatically (invisible quality floor), interactively (design conversation partner), and educationally (teaching tool).

## Layer Architecture

```
┌─────────────────────────────────────────────┐
│              Project CLAUDE.md              │
│  layers: [bringhurst, rams]                │
│  aesthetic: shadcn | preset:linear-mercury  │
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
│bringhurst│ │preset/DS  │  │  rams   │
└────────┘  └───────────┘  └─────────┘
```

| Layer | File | Purpose | Default |
|-------|------|---------|---------|
| **Craft** (bringhurst) | `layers/bringhurst.md` | Typographic & spatial fundamentals | On |
| **Aesthetic** (preset/design-system) | `presets/<name>.md` | Project look & feel | `linear-mercury` fallback |
| **Accessibility** (rams) | `layers/rams.md` | WCAG compliance & visual bugs | On |

## Related Skills

Two standalone skills handle explicit user-invoked actions:

| Skill | When | Invoke With |
|-------|------|-------------|
| `design-brief` | Before coding | `/design-brief` or "generate a style brief" |
| `design-review` | After coding | `/design-review` or "review the design quality" |

`/design-review` produces a composite score with layer sub-scores.
`/rams` still works as a standalone a11y-only shortcut.

---

## Step 1: Load Project Config

Read the project's `CLAUDE.md` for a `## Design Quality` section. Parse these fields:

| Field | Default | Options |
|-------|---------|---------|
| **Layers** | `bringhurst, rams` | Any combination of `bringhurst`, `rams`. Omit one to disable it. |
| **Aesthetic** | `linear-mercury` | A preset name (`linear-mercury`, `stripe-vercel`, `apple-notion`), `design-system` (auto-detect), or `none` |
| **Strictness** | `standard` | `relaxed` (suggestions only), `standard` (warnings), `strict` (errors that affect score) |
| **Teaching** | `normal` | `verbose` (explain everything), `normal` (explain novel violations — first per concept per session), `quiet` (just flag) |
| **Overrides** | none | Key-value overrides for any layer (e.g., `measure: 60-80ch`) |

### Config Example

```markdown
## Design Quality

**Layers:** bringhurst, rams
**Aesthetic:** linear-mercury
**Strictness:** standard
**Teaching:** normal
**Overrides:**
- measure: 60-80ch (wider for code documentation)
```

### Smart Defaults (when no config exists)

If no `## Design Quality` section is found:
- Layers: `bringhurst` + `rams` (both on)
- Aesthetic: `linear-mercury` (fallback preset)
- Strictness: `standard`
- Teaching: `normal`
- Overrides: none

### Design System Detection

When `Aesthetic: design-system` is set, look for:
1. Shadcn/ui components (`components/ui/` directory)
2. Design tokens file (`tokens.json`, `theme.ts`, `tailwind.config` custom theme)
3. Figma-exported tokens

When a design system is detected, it **replaces** the aesthetic preset — the craft (bringhurst) and a11y (rams) layers still apply on top.

### Loading Sequence

1. Parse `## Design Quality` from project `CLAUDE.md` (or apply smart defaults)
2. Load active layers:
   - If `bringhurst` in layers → load `~/.claude/skills/design/design-quality/layers/bringhurst.md`
   - If `rams` in layers → load `~/.claude/skills/design/design-quality/layers/rams.md`
3. Load aesthetic:
   - If preset name → load `~/.claude/skills/design/design-quality/presets/<name>.md`
   - If `design-system` → detect and load project's design system
   - If `none` → skip aesthetic layer
4. Load guard checks from `~/.claude/skills/design/design-quality/references/guard-checks.md`
5. Apply any overrides from config

## Step 2: Skill Precedence

**When this skill is active with a project preset, its rules take precedence over the generic `frontend-design` skill.** The `frontend-design` skill's generic aesthetic guidance ("avoid Inter," "make unexpected choices") does NOT apply when a preset is configured. The preset defines the project's aesthetic — follow it.

Other design skills (`interface-design`, `accessibility-a11y`, `ui-design-system`) remain complementary.

## Step 3: Inline Guard During Coding

When writing UI code, silently apply checks from **all active layers**:

1. Before writing a component, check the code against:
   - **Craft checks** (bringhurst) — measure, rhythm, scale, weight, typeface constraints
   - **Aesthetic checks** (preset) — colors, fonts, spacing grid, elevation, motion
   - **A11y checks** (rams) — touch targets, ARIA labels, contrast, keyboard nav
2. If violations found, flag them in your response BEFORE writing the code
3. Write the corrected code (not the violating version)
4. Apply teaching moments per the **Teaching** setting:
   - `verbose`: Explain every violation with context from the layer reference
   - `normal`: Explain the first occurrence of each concept per session
   - `quiet`: Just flag the violation, no explanation

Refer to `~/.claude/skills/design/design-quality/references/guard-checks.md` for the full checklist. Checks are tagged by layer — only run checks for active layers.

This is automatic — no user action needed. Use `/design-review` for the explicit version.

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
| User is brainstorming a UI feature | Craft (bringhurst) | Surface design principles | "This is data-dense — consider a modular scale for the type hierarchy. A minor third (1.2) keeps sizes close for dense UI." |
| User is planning/starting a UI feature | All active layers | `/design-brief` | "Before we start coding, run `/design-brief` to lock in constraints from all active layers — typography craft, [preset] aesthetic, and a11y requirements." |
| User is writing UI code | All active layers | Apply guard silently | Don't interrupt — guard silently and flag violations naturally. Teach on novel violations. |
| User finishes UI changes or is about to commit | All active layers | `/design-review` | "Run `/design-review [path]` to get a composite quality score with craft, aesthetic, and a11y sub-scores." |
| User asks for code review with UI in diff | All active layers | Include design scoring | Run design-review scoring as part of the code review — don't make them ask separately. |
| User's composite score < 70 | All active layers | Offer to fix | "Composite score is XX/100 — want me to auto-fix the N errors and warnings?" |
| Session starts with `## Design Quality` in CLAUDE.md | — | Acknowledge | Brief one-liner: "Design quality active — [layers], [preset] aesthetic." |
| User asks for a11y-only audit | Rams | `/rams` | "Running a11y layer only — use `/design-review` for the full composite audit." |

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
| `linear-mercury` | Clean, functional, minimal | SaaS dashboards, dev tools, productivity apps |
| `stripe-vercel` | Premium, polished, depth | Marketing sites, developer platforms, fintech |
| `apple-notion` | Refined simplicity | Consumer apps, content tools, note-taking |

Presets are at `~/.claude/skills/design/design-quality/presets/`.

### Custom Presets

Create a new `.md` file in `presets/` following the same structure. Reference it in CLAUDE.md:

```markdown
## Design Quality
**Aesthetic:** my-custom-preset
```

### Per-Session Override

> "Use the stripe-vercel preset for this session"

Overrides the project default until the session ends.

## Workflow Integration

| Workflow Stage | Active Layers | What It Does |
|----------------|---------------|--------------|
| **brainstorm** | Craft (bringhurst) | Surfaces relevant design principles for the feature. "This is data-dense — consider a modular scale for the type hierarchy." |
| **plan / brief** | All active layers | Generates unified constraints document: typography (craft), color/elevation (aesthetic), a11y requirements (rams). User can adjust before coding. |
| **work** | All active layers (inline) | Silent guard checks from all layers. Teaching moments on violations per Teaching setting. |
| **review** | All active layers (explicit) | Unified audit → composite score with layer sub-scores. Single `/design-review` runs everything. |
| **rams** (standalone) | A11y layer only | Still works independently for quick a11y-only audits via `/rams`. |
