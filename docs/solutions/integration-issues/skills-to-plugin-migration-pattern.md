---
title: "Skill-to-Plugin Migration: Restructuring Loose Claude Code Skills into a Proper Plugin"
date: 2026-02-20
category: integration-issues
tags:
  - claude-code
  - plugin
  - restructure
  - skills
  - migration
  - path-resolution
  - naming-conventions
  - frontmatter
  - severity-sync
component: design-plugin
severity: moderate
symptoms:
  - Absolute paths (~/.claude/skills/) hardcoded across 16 occurrences in 4 files, breaking portability
  - No versioning or packaging — no plugin.json, no changelog, no install mechanism
  - Manual file copying required for installation
  - Brand names in presets creating attribution concerns
  - Layer name "bringhurst" opaque to unfamiliar users
  - YAML frontmatter bug — user_invocable (underscore) instead of user-invocable (hyphen)
  - Three-way severity definition causing drift and mismatches
  - Dual check ID numbering causing collisions
  - Missing lifecycle capabilities — no creative direction or auto-fix workflow
root_cause: >
  The design quality system grew organically as individual skill files installed
  manually to ~/.claude/skills/, accumulating absolute paths, duplicated loading
  logic, inconsistent naming, and no packaging structure. Without a plugin
  manifest (plugin.json), there was no versioning, no portable path resolution,
  and no command namespacing.
status: resolved
resolution: >
  Restructured into a Claude Code plugin under design/ with
  .claude-plugin/plugin.json (v1.0.0, MIT license). Migrated files additive-first
  (copy then delete), converted absolute paths to relative markdown links, renamed
  layers and presets, fixed frontmatter, centralized loading logic, created two
  new skills, and wrote five command entry points.
---

# Skill-to-Plugin Migration Pattern

## Problem Statement

The design quality system started as loose skill files manually installed to `~/.claude/skills/design/`. Over time it accumulated:

- **16 absolute path references** across 4 files — breaks on any other machine
- **No versioning** — no `plugin.json`, no changelog, no way to manage updates
- **Manual installation** — users had to copy files and hand-edit their `CLAUDE.md`
- **Brand-name presets** (Linear, Stripe, Apple) — attribution concerns for distribution
- **YAML frontmatter bug** — `user_invocable` (underscore) silently ignored; correct key is `user-invocable` (hyphen)
- **Three-way severity sync** — check severity defined in layer tables, a11y guard section, AND guard-checks.md simultaneously
- **Dual check ID numbering** — layer-specific IDs (B1-B6) AND sequential numbers (1-37) causing collisions
- **Missing lifecycle steps** — no creative direction skill, no auto-fix workflow

## Solution

### Migration Strategy: Additive-First, Delete-Last

The single most important process decision. Old files stayed in place through all implementation commits. Only the final commit removed the old structure. The system was always functional at every intermediate state.

### 7-Commit Sequence

| # | Commit | What It Did |
|---|--------|-------------|
| 1 | `2404909` | Brainstorm + detailed plan (enhanced by 8 parallel research agents) |
| 2 | `5ef355d` | Scaffold `design/` directory, copy all 11 files with filename renames |
| 3 | `e9cb4c2` | Single-pass content update: absolute→relative paths, layer/preset renames, frontmatter fixes, severity sync |
| 4 | `b4ceedc` | Two new skills: `design-direction` (creative exploration) and `design-fix` (auto-correction) |
| 5 | `a96ad46` | Five command entry points for `/design:*` namespace |
| 6 | `7fb6007` | Metadata: README, CREDITS, CHANGELOG, development guide |
| 7 | `beb1810` + `6e52db0` | Config update + delete old structure |

### Key Structural Decisions

**Command thin-wrapper pattern.** Commands are 10-line entry points that load a skill and pass `$ARGUMENTS`. All logic lives in skills.

```yaml
---
description: Generate design constraints from all active layers before coding UI
argument-hint: "[preset-name]"
---

# /design:brief

Load the [design-brief skill](../skills/design-brief/SKILL.md) and execute with `$ARGUMENTS`.
```

**Orchestrator hub.** The `design-quality` skill (marked `user-invocable: false`) owns all config resolution, layer loading, and inline guard enforcement. Other skills delegate to it rather than duplicating loading logic.

**Invocation control.** Three frontmatter strategies:

| Type | Frontmatter | Example |
|------|-------------|---------|
| Background knowledge | `user-invocable: false` | Orchestrator — auto-loads, hidden from `/` menu |
| Normal skills | (defaults) | Brief, review — can be auto-suggested |
| Side-effect skills | `disable-model-invocation: true` | Direction, fix — user must explicitly invoke |

**Relative paths.** All internal references use relative markdown links (`[craft.md](layers/craft.md)`, `[SKILL.md](../design-quality/SKILL.md)`). No `~/.claude/` or `/Users/` anywhere.

### Plugin Manifest

```json
{
  "name": "design",
  "version": "1.0.0",
  "description": "Design quality lifecycle — creative direction, constraint generation, inline enforcement, composite scoring, and auto-correction.",
  "author": { "name": "Sean Smith", "url": "https://github.com/SeanSmithDesign" },
  "license": "MIT",
  "repository": "https://github.com/SeanSmithDesign/design-proof"
}
```

Automatic namespacing: plugin name `design` + command file `brief.md` = `/design:brief`.

## What Went Right

- **8 parallel research agents** during planning caught bugs before they were written: the `user_invocable` frontmatter bug, the dual check-ID anti-pattern, and the phase-ordering risk (don't delete old files before updating config).
- **Single-pass content updates** touched each file exactly once, avoiding partial-rename states.
- **Relative path strategy worked** without needing the `${CLAUDE_PLUGIN_ROOT}` fallback.
- **Lean project config** reduced per-project `CLAUDE.md` from verbose manual instructions to ~7 lines.

## What Was Tricky

- **Severity sync** was the deepest content problem. Mapping a11y's Critical/Serious/Moderate to the plugin's Error/Warning/Suggestion required careful cross-referencing across three files.
- **Check ID unification** from dual numbering (B1-B6 + sequential 1-37) to prefixed scheme (C1-C6, A1-A24, E1-E10, J1-J5) was conceptually simple but tedious across all reference files.
- **The a11y command asymmetry** — 4 commands are pure thin wrappers, but `/design:a11y` is a 40-line self-contained command that loads a layer directly, bypassing the orchestrator.
- **Command namespacing gotcha** — subagents cannot resolve short command names (Claude Code issue #11328). All prose must use `/design:review`, never `/review`.

## Prevention Checklist

Use this when migrating skills to a plugin:

### Structure
- [ ] Create `.claude-plugin/plugin.json` with `name`, `version`, `description`, `author`, `license`
- [ ] Commands in `commands/` (thin wrappers)
- [ ] Skills in `skills/<skill-name>/SKILL.md`
- [ ] Reference data in `skills/<domain>/references/`
- [ ] Composable sub-units in `skills/<domain>/layers/` and `presets/`

### Content Migration
- [ ] Convert all absolute paths to relative markdown links
- [ ] Convert YAML frontmatter fields to kebab-case (not snake_case)
- [ ] All command references use full `/plugin:command` format
- [ ] Check IDs follow a prefixed scheme per layer
- [ ] Single severity vocabulary enforced everywhere

### Consistency Audit
- [ ] Every check ID in a layer file has a corresponding entry in guard-checks
- [ ] Severities match across layer tables and guard-checks
- [ ] Scoring weights reference only existing check IDs

### Pre-Ship
- [ ] Version bumped in `plugin.json`
- [ ] `CHANGELOG.md` updated
- [ ] `grep -r` for absolute paths returns zero results
- [ ] `grep -r` for snake_case frontmatter fields returns zero results
- [ ] Manual test: install and run every command

## Testing Strategies

### Structural
1. **Manifest validation** — `plugin.json` parses as valid JSON with required `name` field
2. **Frontmatter parsing** — all `.md` files use kebab-case field names
3. **Link integrity** — every relative markdown link resolves to an existing file

### Command Registration
1. All `commands/*.md` files produce `/design:<name>` entries
2. Skills marked `user-invocable: false` do not appear in command list
3. Skills marked `disable-model-invocation: true` cannot be auto-triggered

### Functional
1. `/design:review` on a clean component — score near 100, no false positives
2. `/design:review` on a known-bad component — specific check IDs with correct severity terms
3. `/design:brief` with a preset — output references that preset's constraints
4. `/design:a11y` — only a11y-layer checks (A/M/V prefixes), no craft (C) checks

### Regression Markers
- `user_invocable` (snake_case) must not appear
- Retired check IDs (B1-B6) must not appear in active tables
- Retired preset names must not appear outside CHANGELOG/CREDITS
- Absolute path patterns must not appear in any skill or command

## Cross-References

- [Brainstorm](../../brainstorms/2026-02-20-design-plugin-restructure-brainstorm.md)
- [Plan](../../plans/2026-02-20-refactor-design-plugin-restructure-plan.md)
- [A11y Layer Solution](../code-quality/a11y-layer-licensing-and-coverage-upgrade.md)
- [Session Notes](../../SESSION_NOTES.md)
- [Plugin README](../../../design/README.md)
- [Plugin CHANGELOG](../../../design/CHANGELOG.md)
- [Claude Code Plugins Reference](https://code.claude.com/docs/en/plugins-reference)
- [anthropics/claude-code #11328](https://github.com/anthropics/claude-code/issues/11328) — command namespacing bug
