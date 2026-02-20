# Design Plugin — Development Guide

Instructions for contributing to this plugin.

## Conventions

### Check IDs
- Prefixed scheme: `C` for craft, `A`/`M`/`V` for a11y, `E` for aesthetic, `J` for judgment
- Example: C1-C6 (craft), A1-A24 (WCAG), M1-M4 (modern HTML), V1-V18 (visual)
- Never reuse a retired ID

### Severity Vocabulary
- Always use: **Error** / **Warning** / **Suggestion**
- Never use: critical, serious, moderate, minor, info

### Severity Authority
- Layer files (a11y.md, craft.md) define check severity in their tables
- guard-checks.md derives from layer tables — keep them in sync
- If they conflict, the layer file wins

### Reference Links
- Use relative markdown links: `[filename.md](./path/filename.md)`
- Never use absolute paths or backtick-only references

### Command Naming
- Frontmatter uses `description` field (auto-namespaced by plugin name)
- In prose, always use full `/design:command` format
- Never reference bare `/command` — it won't resolve

### Frontmatter Rules
- Orchestrator: `user-invocable: false` (background knowledge, always loaded)
- Side-effect skills: `disable-model-invocation: true` (design-direction, design-fix)
- Commands: `description` + `argument-hint` only

## Pre-Commit Checklist

- [ ] Version bumped in `.claude-plugin/plugin.json` if shipping
- [ ] CHANGELOG.md updated
- [ ] No absolute paths (`~/.claude/`, `/Users/`)
- [ ] Check IDs follow prefixed scheme
- [ ] Severity vocabulary consistent

## Adding a New Layer

1. Create `skills/design-quality/layers/<name>.md`
2. Add check table with ID prefix and severity
3. Update guard-checks.md with corresponding entries
4. Update review-rubric.md scoring weights
5. Update orchestrator layer table

## Adding a New Preset

1. Create `skills/design-quality/presets/<name>.md`
2. Follow the 8-section schema (Philosophy through Do/Don't Examples)
3. Add `<!-- Inspired by ... -->` comment after title
4. Test with `/design:brief` to verify constraint generation
