# Design — Claude Code Plugin

Design quality lifecycle for Claude Code. Creative direction, constraint generation, inline enforcement, composite scoring, and auto-correction.

## What's Inside

- **5 skills** — design-quality (orchestrator), design-brief, design-review, design-direction, design-fix
- **5 commands** — `/design:direction`, `/design:brief`, `/design:review`, `/design:fix`, `/design:a11y`
- **2 layers** — craft (typography & spacing), a11y (WCAG 2.1/2.2 + visual quality)
- **3 presets** — clean-functional, premium-depth, refined-simple

## Installation

Add to your Claude Code settings or install via the plugin manager:

```bash
claude plugin add SeanSmithDesign/design-proof --path design
```

## Workflow

```
/design:direction  →  Define visual identity (generates preset)
/design:brief      →  Generate constraints before coding
       ↓
    [code UI]
       ↓
/design:review     →  Composite score (/100) with layer sub-scores
/design:fix        →  Auto-apply corrections with approval
/design:a11y       →  Quick standalone accessibility audit
```

The orchestrator (`design-quality`) runs automatically during UI work, enforcing guard checks inline without needing a slash command.

## Configuration

Add to your project's `CLAUDE.md`:

```markdown
## Design Quality

**Layers:** craft, a11y
**Aesthetic:** clean-functional
**Strictness:** standard
**Teaching:** normal
**Auto-fix:** none
```

| Field | Default | Options |
|-------|---------|---------|
| **Layers** | `craft, a11y` | Any combination |
| **Aesthetic** | `clean-functional` | Preset name, `design-system`, or `none` |
| **Strictness** | `relaxed` / `standard` / `strict` | Controls check severity thresholds |
| **Teaching** | `verbose` / `normal` / `quiet` | Controls educational output |
| **Auto-fix** | `none` / `errors` / `errors-warnings` / `silent` | Controls fix automation |
| **Overrides** | Key-value overrides for any layer | e.g., `measure: 60-80ch` |

## Credits

See [CREDITS.md](CREDITS.md) for inspiration sources and attribution.

## License

MIT
