# Brainstorm: Design Plugin Restructure

**Date:** 2026-02-20
**Status:** Ready for planning

## What We're Building

Restructure design-proof from a collection of manually-installed skill files into a proper Claude Code plugin called **`design`**. The plugin covers the full design quality lifecycle: creative exploration, constraint generation, inline enforcement, scoring, and auto-correction.

### The Workflow

```
/design:direction  →  Creative exploration (what's the vibe?)
         ↓
    Generates custom preset file + updates CLAUDE.md config
         ↓
/design:brief      →  Lock in constraints from all active layers
         ↓
    Work (guard checks run inline, always-on when layers active)
         ↓
/design:review     →  Composite score (/100) with layer sub-scores
         ↓
/design:fix        →  Present fixes grouped by severity, user approves
         ↓
    Compound        →  Document learnings
```

**Standalone shortcut:** `/design:a11y` runs only the accessibility layer (quick check without full composite scoring).

### Plugin Components

| Type | Count | Components |
|------|-------|------------|
| Skills | 5 | design-direction, design-brief, design-quality (orchestrator), design-review, design-fix |
| Commands | 6 | direction, brief, review, fix, a11y, (quality is always-loaded, not a command) |
| Layers | 2 | craft (bringhurst-inspired), a11y (original WCAG 2.1+2.2) |
| Presets | 3 starter | renamed from brand names to style names |
| References | 2 | guard-checks, review-rubric |

## Key Decisions

### 1. Plugin name: `design`

Commands become `/design:direction`, `/design:brief`, `/design:review`, `/design:fix`, `/design:a11y`. Clean, intuitive namespace.

### 2. Full vision in v1.0

Ship all 5 skills + preset renaming + credits + proper packaging. Don't stage the rollout.

### 3. Architecture: lean config + plugin-loaded skills

- **CLAUDE.md** stays lean (~10 lines) — just declares which layers are active, which preset, strictness level
- **Plugin** provides the skill files (loaded on demand when working on UI code)
- **Guard checks** are always-on when layers are active (the value proposition)
- **Commands** are explicitly on-demand for specific workflow steps

This mirrors `.eslintrc` (lean config) + ESLint (large rule engine, installed as package).

### 4. Direction produces a custom preset file

`/design:direction` explores aesthetics with the user, then writes a custom preset `.md` file in the same format as the starter presets. Updates CLAUDE.md to point to it (`Aesthetic: my-project`). The preset is version-controlled, reusable, and shareable.

### 5. A11y is a subset of review, not a separate step

A11y is always evaluated as part of `/design:review` (scored as part of the /30 accessibility sub-score). `/design:a11y` is a standalone shortcut for quick checks or non-design projects — like running one test file vs. the whole suite.

### 6. Preset renaming: style names with inspiration aliases

Replace brand names with descriptive style names. Keep a comment in each preset noting the inspiration.

| Current | New name (TBD) | Inspiration |
|---------|---------------|-------------|
| `linear-mercury` | e.g., `clean-functional` | Inspired by Linear/Mercury aesthetic |
| `stripe-vercel` | e.g., `premium-depth` | Inspired by Stripe/Vercel aesthetic |
| `apple-notion` | e.g., `refined-simple` | Inspired by Apple/Notion aesthetic |

Final style names to be decided during implementation.

### 7. Fix behavior: approve-first with opt-in auto-fix

- Default: present fixes grouped by severity (Errors, Warnings, Suggestions), user approves each
- Optional CLAUDE.md config: `Auto-fix: errors` to auto-apply Error-level fixes without prompting
- Severity drives the grouping and presentation order, not whether fixes are auto-applied

### 8. License: MIT

Maximum permissiveness. Matches compound-engineering. Simple attribution.

### 9. Community presets (future)

Users can create custom presets via `/design:direction` and share them:
- Drop a `.md` file into a `presets/` folder in their project
- Reference by name in CLAUDE.md config
- Share on GitHub as standalone files
- Future: support loading from URL (`Aesthetic: https://github.com/...`)

## Sources of Inspiration & Credits

| Source | What it inspired | Author | License |
|--------|-----------------|--------|---------|
| **Frontend Design Skill** | Creative direction, anti-AI-slop aesthetics | Anthropic (Prithvi Rajasekaran, Alexander Bricken) | Apache 2.0 |
| **RAMS** | Accessibility layer scope and structure | Brian Lovin | Unknown |
| **bringhurst-typography** | Encoding Bringhurst's typographic rules as an AI skill | agrimsingh | Not specified |
| **Compound Engineering** | Workflow loop (plan, work, review, compound) | Kieran Klaassen / Every, Inc. | MIT |
| **The Elements of Typographic Style** | Typographic principles (measure, rhythm, scale, weight, restraint) | Robert Bringhurst (book) | N/A |

**Crediting approach:** All content in the plugin is original prose. Sources are credited as inspirations in CREDITS.md, not as code dependencies. No verbatim code from any source is included.

## Plugin Directory Structure

```
design/
├── .claude-plugin/
│   └── plugin.json                    # name: "design", version, MIT license
├── CREDITS.md                         # Sources of inspiration
├── CHANGELOG.md                       # Keep a Changelog format
├── README.md                          # Component counts, installation, usage
├── LICENSE                            # MIT
├── CLAUDE.md                          # Plugin dev instructions, pre-commit checklist
│
├── skills/
│   ├── design-direction/              # Creative exploration
│   │   └── SKILL.md
│   ├── design-brief/                  # Constraint generation from layers
│   │   └── SKILL.md
│   ├── design-quality/                # Orchestrator + layers + presets
│   │   ├── SKILL.md
│   │   ├── layers/
│   │   │   ├── craft.md               # Renamed from bringhurst.md
│   │   │   └── a11y.md
│   │   ├── presets/
│   │   │   ├── [style-name-1].md      # Renamed from linear-mercury
│   │   │   ├── [style-name-2].md      # Renamed from stripe-vercel
│   │   │   └── [style-name-3].md      # Renamed from apple-notion
│   │   └── references/
│   │       ├── guard-checks.md
│   │       └── review-rubric.md
│   ├── design-review/                 # Composite scoring + findings
│   │   └── SKILL.md
│   └── design-fix/                    # Auto-correction with approval
│       └── SKILL.md
│
├── commands/
│   ├── direction.md                   # /design:direction
│   ├── brief.md                       # /design:brief
│   ├── review.md                      # /design:review
│   ├── fix.md                         # /design:fix
│   └── a11y.md                        # /design:a11y (standalone shortcut)
│
└── docs/
    ├── CREDITS_AND_WORKFLOW.md
    ├── brainstorms/
    └── solutions/
```

## Open Questions

1. **Final style names for presets** — What descriptive names replace linear-mercury, stripe-vercel, apple-notion?
2. **Layer rename** — Should `bringhurst.md` become `craft.md` for clarity, or keep `bringhurst` since it's descriptive of the methodology?
3. **Plugin distribution** — Own marketplace, Anthropic's official marketplace, or GitHub-only for now?
4. **Agents** — Should the plugin include specialized agents (e.g., a design-critic agent for review, an a11y-auditor agent)? Or keep it skills-only for v1.0?
5. **Cross-reference paths** — All `~/.claude/skills/` absolute paths in skill files need to become relative. Need to verify how the plugin system resolves relative paths.
6. **Three-way sync** — The severity-in-three-places issue (a11y.md tables, a11y.md guard section, guard-checks.md). Address in this restructure or defer?
7. **Community preset format** — Is the current preset `.md` structure documented enough for community contributions, or does it need a schema/template?

## What's NOT in Scope

- Building a showcase/demo project (future)
- Custom marketplace infrastructure (future)
- MCP server integration (future, if needed)
- Mobile-specific layers or checks (future)
