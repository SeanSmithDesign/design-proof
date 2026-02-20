# User Preferences

- Uses Ghostty as terminal emulator

## Design Quality (Global)

Always load `~/.claude/skills/design/design-quality/SKILL.md` when working on UI files or when the user mentions design, aesthetics, taste, or polish.

**Unified engine with three composable layers:**

| Layer | File | Purpose | Default |
|-------|------|---------|---------|
| **Craft** (bringhurst) | `layers/bringhurst.md` | Typographic & spatial fundamentals (measure, rhythm, scale, weight restraint) | On |
| **Aesthetic** (preset) | `presets/<name>.md` | Project look & feel (colors, spacing grid, elevation, motion) | `linear-mercury` |
| **Accessibility** (a11y) | `layers/a11y.md` | WCAG 2.1 + 2.2 compliance, modern HTML, visual quality | On |

**Available presets:** `linear-mercury`, `stripe-vercel`, `apple-notion`

**Skills:**
- `~/.claude/skills/design/design-quality/SKILL.md` — Core engine (orchestrator, layer loading, inline guard, recommendations)
- `~/.claude/skills/design/design-brief/SKILL.md` — `/design-brief` before coding (generates constraints from all active layers)
- `~/.claude/skills/design/design-review/SKILL.md` — `/design-review` after coding (composite score with layer sub-scores + auto-fix)
- `/a11y` — Standalone accessibility audit (runs just the a11y layer)

**Per-project config** via `## Design Quality` section in each project's `CLAUDE.md`:

```markdown
## Design Quality

**Layers:** bringhurst, a11y
**Aesthetic:** linear-mercury
**Strictness:** standard
**Teaching:** normal
**Overrides:**
- measure: 60-80ch
```

| Field | Default | Options |
|-------|---------|---------|
| **Layers** | `bringhurst, a11y` | Any combination. Omit one to disable it. |
| **Aesthetic** | `linear-mercury` | Preset name, `design-system` (auto-detect), or `none` |
| **Strictness** | `standard` | `relaxed` / `standard` / `strict` |
| **Teaching** | `normal` | `verbose` / `normal` / `quiet` |
| **Overrides** | none | Key-value overrides for any layer |

**Smart defaults** when no config exists: bringhurst + a11y on, `linear-mercury` aesthetic, standard strictness, normal teaching.

## Agent Context System

For any project with multiple domains, scaffold specialist agent context files in `.claude/projects/<path>/memory/`. This gives domain-expert agents deep knowledge without loading the full codebase.

**Guide:** `~/.claude/skills/workflow/agent-context/SKILL.md`
**Pattern:** `MEMORY.md` (always loaded) + `general-agent-context.md` + `agent-<domain>.md` per specialist
**Convention:** Agents update their files as they work (see "Agent Knowledge Updates" in each project's MEMORY.md)

**On first session in any project:** If `.claude/projects/<path>/memory/MEMORY.md` doesn't list agent context files, suggest scaffolding them.

## Session Notes Workflow

Before compacting any thread or ending a session:

1. Ensure all changes are committed and pushed
2. Generate session notes to `docs/SESSION_NOTES.md`
3. Commit and push the session notes
4. Then compact

**Skill location**: `~/.claude/skills/workflow/session-notes/SKILL.md`

Session notes should include:
- Date and project name
- Features implemented
- New files created
- Key commands
- Commit hashes
