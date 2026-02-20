---
title: "Restructure Design Proof into Claude Code Plugin"
type: refactor
date: 2026-02-20
deepened: 2026-02-20
---

# Restructure Design Proof into Claude Code Plugin

## Enhancement Summary

**Deepened on:** 2026-02-20
**Research agents used:** 8 (skill-authoring, architecture-strategist, pattern-recognition, code-simplicity, best-practices-researcher, design-direction-specialist, design-fix-specialist, learnings-researcher)

### Key Improvements
1. **Path resolution strategy confirmed** — relative paths within plugins work; `../` within plugin root should work; `${CLAUDE_PLUGIN_ROOT}` available as fallback. Test early.
2. **Phase reordering for safety** — old file deletion moved from Phase 1 to Phase 10 (additive-first, delete-last)
3. **Three-way severity sync solution** — remove guard section from a11y.md; guard-checks.md becomes canonical severity source
4. **Check ID unification** — replace dual numbering with prefixed IDs everywhere; B1-B6 → C1-C6
5. **Frontmatter corrections** — fix `user_invocable` → `user-invocable` (bug); add `disable-model-invocation: true` for side-effect skills
6. **Scope decision documented** — full vision (5 skills) vs MVP (3 skills) with tradeoffs for each
7. **Detailed specs for new skills** — complete design-direction conversation flow and design-fix approval UX
8. **Institutional learnings applied** — 12 specific safeguards from the a11y layer replacement

### Scope Decision: Full Vision vs MVP

The simplicity reviewer made a strong case for shipping a minimal plugin first:

| | **MVP (3 skills, 3 commands)** | **Full Vision (5 skills, 5 commands)** |
|---|---|---|
| **Skills** | design-quality, design-brief, design-review | + design-direction, design-fix |
| **Commands** | brief, review, a11y | + direction, fix |
| **Phases** | 4 | 7 (consolidated from 10) |
| **New files** | ~8 (plugin scaffold + commands) | ~12 (+ 2 new SKILL.md + 2 commands) |
| **Preset renames** | None (keep current names) | 3 renames + aliases |
| **Ceremony files** | LICENSE only | + CREDITS.md, CHANGELOG.md, README.md |
| **Risk** | Lower — pure restructure, no new behavior | Higher — new skills need design and testing |
| **Value** | Plugin packaging + portable paths + guard checks | + creative direction + structured fix workflow |

**Arguments for MVP:** Single user, no external audience, existing design-review Phase 3 already does basic fixing, direction is nice-to-have. Ship the restructure, iterate later.

**Arguments for Full Vision:** The brainstorm explicitly chose "full vision in v1.0" and the lifecycle gap (no direction step, no structured fix) is the core motivation beyond packaging.

**Recommendation:** Start with the restructure phases (1-4 below), which are required regardless. Then decide whether to proceed with new skills (phases 5-6) or ship.

---

## Overview

Restructure `design-proof` from a collection of manually-installed skill files into a proper Claude Code plugin called **`design`**. The plugin covers the full design quality lifecycle: creative exploration, constraint generation, inline enforcement, scoring, and auto-correction.

**Brainstorm:** `docs/brainstorms/2026-02-20-design-plugin-restructure-brainstorm.md`

## Problem Statement / Motivation

Design Proof currently lives as loose files under `~/.claude/skills/design/` with absolute paths wired throughout. This creates several problems:

1. **Installation friction** — users must manually copy files to `~/.claude/skills/` and add config to their global CLAUDE.md
2. **Absolute path fragility** — 16 occurrences of `~/.claude/skills/` across 4 files break on any install location change
3. **No versioning** — no plugin.json, no CHANGELOG, no way to know which version is installed
4. **Missing capabilities** — no creative direction step (gap vs Anthropic's frontend-design skill) and no auto-fix workflow
5. **Attribution concerns** — preset names use brand names (Linear, Stripe, Apple), layer name "bringhurst" references a book author

## Proposed Solution

Ship a Claude Code plugin with the namespace `design`, providing 5 skills and 5 commands covering the full design lifecycle:

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
```

**Standalone shortcut:** `/design:a11y` runs only the accessibility layer.

---

## Technical Approach

### Target Plugin Directory Structure

```
design/
├── .claude-plugin/
│   └── plugin.json                    # name: "design", version: "1.0.0", license: "MIT"
├── CREDITS.md                         # Sources of inspiration
├── CHANGELOG.md                       # Keep a Changelog format
├── README.md                          # Installation, usage, component counts
├── LICENSE                            # MIT
├── CLAUDE.md                          # Plugin dev instructions, pre-commit checklist
│
├── skills/
│   ├── design-direction/              # NEW: Creative exploration
│   │   └── SKILL.md
│   ├── design-brief/                  # EXISTING: Constraint generation
│   │   └── SKILL.md
│   ├── design-quality/                # EXISTING: Orchestrator + layers + presets
│   │   ├── SKILL.md
│   │   ├── layers/
│   │   │   ├── craft.md               # RENAME from bringhurst.md
│   │   │   └── a11y.md               # EXISTING (already replaced RAMS)
│   │   ├── presets/
│   │   │   ├── clean-functional.md    # RENAME from linear-mercury.md
│   │   │   ├── premium-depth.md       # RENAME from stripe-vercel.md
│   │   │   └── refined-simple.md      # RENAME from apple-notion.md
│   │   └── references/
│   │       ├── guard-checks.md        # EXISTING
│   │       └── review-rubric.md       # EXISTING
│   ├── design-review/                 # EXISTING: Composite scoring
│   │   └── SKILL.md
│   └── design-fix/                    # NEW: Auto-correction with approval
│       └── SKILL.md
│
├── commands/
│   ├── direction.md                   # /design:direction
│   ├── brief.md                       # /design:brief
│   ├── review.md                      # /design:review
│   ├── fix.md                         # /design:fix
│   └── a11y.md                        # /design:a11y
│
└── docs/
    ├── brainstorms/
    └── solutions/
```

### Component Inventory

| Type | Count | Components |
|------|-------|------------|
| Skills | 5 | design-direction (NEW), design-brief, design-quality (orchestrator), design-review, design-fix (NEW) |
| Commands | 5 | direction, brief, review, fix, a11y |
| Layers | 2 | craft (renamed from bringhurst), a11y |
| Presets | 3 starter | clean-functional, premium-depth, refined-simple |
| References | 2 | guard-checks, review-rubric |

### Research Insights: Plugin System

**Path resolution confirmed (best-practices-researcher):**
- Simple relative paths (`layers/craft.md`) from within a skill directory: **works** (official docs + real examples)
- `../` paths across skill directories within the same plugin: **should work** since both files are inside plugin root, but no official confirmation — test early
- `${CLAUDE_PLUGIN_ROOT}` environment variable: available in hooks, MCP, scripts, and markdown files — use as fallback
- Paths outside plugin root: **blocked after installation** (files not copied to cache)

**Plugin.json (best-practices-researcher):**
- Only `name` is required. All other fields optional.
- Version field drives cache + update detection — must bump version for users to see changes
- Custom directory paths supplement defaults, don't replace them
- `author` should be an object `{name, email, url}` not a string

**Command namespacing (best-practices-researcher):**
- Automatic: plugin name `design` + file `commands/brief.md` = `/design:brief`
- Known bug (GitHub #11328, closed NOT_PLANNED): subagents cannot resolve short command names — always use fully-qualified `/design:review` not `/review` in skill files
- No built-in alias mechanism for old command names

**Guard check pattern (best-practices-researcher):**
- `user-invocable: false` on the orchestrator skill is the standard pattern for always-on background knowledge
- Claude auto-loads based on description keywords matching the conversation context

---

## Implementation Phases

### Phase 1: Plugin Scaffolding & File Migration (Additive Only)

Create the plugin structure and copy existing files into it. **No deletions in this phase** — old files stay until Phase 7 verification.

#### 1.1 Create plugin manifest

**File:** `design/.claude-plugin/plugin.json`

```json
{
  "name": "design",
  "version": "1.0.0",
  "description": "Design quality lifecycle — creative direction, constraint generation, inline enforcement, composite scoring, and auto-correction. 5 skills, 5 commands, 2 layers, 3 presets.",
  "author": {
    "name": "Sean Smith",
    "url": "https://github.com/SeanSmithDesign"
  },
  "license": "MIT",
  "keywords": ["design", "accessibility", "typography", "quality", "wcag", "a11y"]
}
```

#### 1.2 Create LICENSE file

**File:** `design/LICENSE` — Standard MIT license.

#### 1.3 Copy existing files into plugin structure

| Source (current) | Destination (plugin) |
|-----------------|---------------------|
| `skills/design/design-quality/SKILL.md` | `design/skills/design-quality/SKILL.md` |
| `skills/design/design-quality/layers/a11y.md` | `design/skills/design-quality/layers/a11y.md` |
| `skills/design/design-quality/layers/bringhurst.md` | `design/skills/design-quality/layers/craft.md` |
| `skills/design/design-quality/presets/linear-mercury.md` | `design/skills/design-quality/presets/clean-functional.md` |
| `skills/design/design-quality/presets/stripe-vercel.md` | `design/skills/design-quality/presets/premium-depth.md` |
| `skills/design/design-quality/presets/apple-notion.md` | `design/skills/design-quality/presets/refined-simple.md` |
| `skills/design/design-quality/references/guard-checks.md` | `design/skills/design-quality/references/guard-checks.md` |
| `skills/design/design-quality/references/review-rubric.md` | `design/skills/design-quality/references/review-rubric.md` |
| `skills/design/design-brief/SKILL.md` | `design/skills/design-brief/SKILL.md` |
| `skills/design/design-review/SKILL.md` | `design/skills/design-review/SKILL.md` |
| `commands/a11y.md` | `design/commands/a11y.md` |

#### Research Insight: Phase Ordering (architecture-strategist)

> Old file deletion should happen in Phase 7 exclusively, not Phase 1. Deleting installed skills before updating CLAUDE.md creates a broken intermediate state where CLAUDE.md points to deleted files. Phase 1 should be additive-only.

---

### Phase 2: Content Updates (Paths, Names, Frontmatter)

All content changes in a single pass — paths, renames, and frontmatter. Touch each file once.

#### 2.1 Path migration (absolute → relative)

Convert all 16 absolute path occurrences to relative markdown links.

**Primary approach:** Use markdown link syntax per compound-engineering convention:
```markdown
# Instead of:
Load `~/.claude/skills/design/design-quality/layers/craft.md`

# Use:
Load [craft.md](layers/craft.md)

# Cross-skill (from design-brief to design-quality):
Load [craft.md](../design-quality/layers/craft.md)
```

**Files requiring path updates:**

| File | Absolute paths | Change to |
|------|---------------|-----------|
| `skills/design-quality/SKILL.md` | 5 occurrences | Relative: `[craft.md](layers/craft.md)`, `[a11y.md](layers/a11y.md)`, `[<name>.md](presets/<name>.md)`, etc. |
| `skills/design-brief/SKILL.md` | 4 occurrences | `[SKILL.md](../design-quality/SKILL.md)`, `[craft.md](../design-quality/layers/craft.md)`, etc. |
| `skills/design-review/SKILL.md` | 6 occurrences | Same pattern as design-brief, plus references |
| `commands/a11y.md` | 1 occurrence | `[a11y.md](../skills/design-quality/layers/a11y.md)` |

**Fallback if `../` doesn't resolve:** Use `${CLAUDE_PLUGIN_ROOT}/skills/design-quality/layers/craft.md`. Test early before proceeding to Phase 3.

#### 2.2 Rename bringhurst → craft

**Content changes across ALL files:**

| Find | Replace |
|------|---------|
| `bringhurst` (layer ID in config) | `craft` |
| `bringhurst.md` (filename references) | `craft.md` |
| `Bringhurst` (display name) | `Craft` |
| `(bringhurst)` in rubric tables | `(craft)` |
| `[bringhurst]` in guard check tags | `[craft]` |
| `B1`-`B6` check IDs in craft.md | `C1`-`C6` |

**Preserve:** Inside `craft.md` itself, keep: `<!-- Inspired by Robert Bringhurst's "The Elements of Typographic Style" -->`

#### Research Insight: Check ID Unification (pattern-recognition)

> The dual numbering system (layer-specific IDs like A1/B1 AND sequential 1-37 in guard-checks.md) is an anti-pattern. **Unify to prefixed IDs everywhere.** Replace sequential numbers in guard-checks.md with the layer-specific IDs:
>
> - Aesthetic checks (currently 1-10, unprefixed): assign `E1-E10` prefix
> - Craft checks (currently 11-16): use `C1-C6` (matching the renamed craft.md)
> - A11y checks (currently 17-37): use the existing `A`/`M`/`V` prefixes from a11y.md
> - Judgment checks (currently 33-37): keep as `J1-J5` or similar
>
> This prevents the numbering collision that already happened once (judgment checks 21-25 reusing a11y check numbers).

#### 2.3 Rename presets

Each preset file gets a new name and an inspiration comment:

| Old filename | New filename | Inspiration comment |
|-------------|-------------|-------------------|
| `linear-mercury.md` | `clean-functional.md` | `<!-- Inspired by Linear and Mercury aesthetics -->` |
| `stripe-vercel.md` | `premium-depth.md` | `<!-- Inspired by Stripe and Vercel aesthetics -->` |
| `apple-notion.md` | `refined-simple.md` | `<!-- Inspired by Apple and Notion aesthetics -->` |

**Content changes across files — find/replace all preset references.**

**Note (simplicity-reviewer):** If preset renames feel like unnecessary churn, they can be deferred to v1.1. The plugin works with current names. The renames are motivated by attribution cleanliness, not functionality.

#### 2.4 YAML frontmatter updates

Fix the `user_invocable` → `user-invocable` bug and set correct invocation controls.

**Skill frontmatter:**

```yaml
# design-quality/SKILL.md (orchestrator — background knowledge)
---
description: >-
  Unified design quality engine with three composable layers (craft, aesthetic,
  a11y). Auto-activates during UI coding to enforce typography, spacing, color
  tokens, and accessibility standards. Use when working on UI files, components,
  or when the user mentions design, aesthetics, taste, or polish.
user-invocable: false
---

# design-brief/SKILL.md
---
description: >-
  Generate a unified style brief from all active design layers before coding UI.
  Outputs craft constraints, aesthetic rules, and a11y requirements.
argument-hint: "[preset-name]"
---

# design-review/SKILL.md
---
description: >-
  Full design quality audit with composite scoring. Runs guard checks across all
  active layers and produces a quality report with layer sub-scores out of 100.
argument-hint: "[file-path | component-name | all]"
---

# design-direction/SKILL.md (NEW — side-effect workflow)
---
description: >-
  Creative exploration for defining a project's visual direction. Guides through
  structured dialogue covering typography, color, spacing, then generates a custom
  preset file and updates CLAUDE.md.
argument-hint: "[amend | variant | fresh]"
disable-model-invocation: true
---

# design-fix/SKILL.md (NEW — side-effect workflow)
---
description: >-
  Apply design quality fixes grouped by severity with user approval. Fixes
  violations found by guard checks. Use after design review or standalone.
argument-hint: "[file-path | all]"
disable-model-invocation: true
---
```

**Command frontmatter:**

```yaml
# Each command file
---
name: design:<command-name>
description: "Brief description"
argument-hint: "[args]"
---
```

#### Research Insight: Invocation Control (skill-authoring)

> - `user-invocable: false` for design-quality — correct. Background knowledge that auto-loads, not a user action. Hides from `/` menu.
> - `disable-model-invocation: true` for design-direction and design-fix — **critical**. Both write files (presets, source code). Claude must never auto-decide to run these. User explicitly invokes via `/design:direction` or `/design:fix`.
> - design-brief and design-review: leave as default (model CAN auto-invoke). The orchestrator's proactive recommendations table says to suggest these — Claude needs permission to do so.
> - Drop non-spec fields from skill frontmatter: `license`, `metadata`, `keywords`. These belong in `plugin.json`, not individual SKILL.md files.

#### 2.5 Severity sync fix

**Problem (architecture-strategist + learnings-researcher):** Severity is defined in three places — a11y.md check tables, a11y.md guard section (lines 123-141), and guard-checks.md. This caused mismatches before and will cause them again.

**Fix:** Remove the "Guard Checks" section from `a11y.md` entirely (lines 123-141). Let `guard-checks.md` be the single canonical source for which checks fire at which severity.

**Also:** Standardize severity vocabulary across all files:
- Use `Error / Warning / Suggestion` everywhere (guard-checks.md, review-rubric.md, craft.md)
- Map a11y.md's internal `Critical / Serious / Moderate` to `Error / Warning / Suggestion`
- The mapping: Critical → Error, Serious → Warning, Moderate → Suggestion

#### 2.6 Centralize loading in orchestrator

**Problem (architecture-strategist):** Both design-brief and design-review duplicate the orchestrator's loading sequence (4-6 steps each) rather than delegating.

**Fix:** Refactor both skills to say: "Ensure the design-quality engine is loaded (it handles layer/preset/config resolution). Then proceed with brief/review-specific logic." The loading sequence lives in one place — `design-quality/SKILL.md` Step 1.

---

### Phase 3: Write New Skills (if proceeding with full vision)

**Skip this phase for MVP — proceed to Phase 4.**

#### 3.1 design-direction SKILL.md (NEW)

**File:** `design/skills/design-direction/SKILL.md`

Creative exploration skill. Helps users discover their project's visual identity through structured dialogue, then generates a custom preset file.

**Target:** 200-300 lines. Follow the established user-invocable template: Setup, Arguments, Execution, Output.

**Conversation format (5 questions max):**

| # | Question | Type | Purpose |
|---|----------|------|---------|
| 1 | What kind of interface? | Multiple choice (4 options) | Data-dense tool / content app / marketing / transactional |
| 2 | How should it feel? | Multiple choice (5 options) | Quiet / Confident / Warm / Sharp / Bold |
| 3 | 1-3 apps you admire? | Open-ended with examples | Primary anti-slop mechanism — grounds aesthetic in real taste |
| 4 | Light or dark? | Multiple choice (3) | Light-first / Dark-first / Both equally |
| 5 | Accent color? | Optional | Yes (hex/description) / No (auto-pick) |

**Output:** Generates preset file to `<project-root>/.claude/presets/<name>.md` matching the 8-section schema (Philosophy, Reference Apps, Typography, Color, Spacing, Elevation, Motion, Component Patterns, Do/Don't).

**Preset resolution order** (add to orchestrator):
1. `<project-root>/.claude/presets/<name>.md` (project-local, highest priority)
2. Plugin built-in: `skills/design-quality/presets/<name>.md` (fallback)

**Edge cases:**
- Existing preset: Ask — amend, create variant, or start fresh
- Partial invocation: `/design:direction colors` modifies only the color section
- References match a starter preset: Offer it directly instead of generating
- Contradictory answers: Surface the tension and offer two interpretations

#### Research Insight: Anti-Slop Mechanisms (design-direction-specialist)

> Three pillars from the frontend-design skill translated to preset generation:
> 1. **Never converge** — maintain a "recently used" font list; rotate neutral families (stone/gray/slate/zinc); vary accent colors per mood
> 2. **Commit to an extreme** — Quiet should be aggressively minimal (near-monochrome, no stagger animation). Bold should lean hard into saturation and dark backgrounds. The enemy is timid middle-ground.
> 3. **Context-specific** — the Philosophy section must contain one unique "signature move" phrase. Bad: "Clean, modern, and professional." Good: "Quiet authority. The interface earns trust through what it omits."
>
> **Font mapping per mood:**
> | Mood | Headings | Body |
> |------|----------|------|
> | Quiet | Inter, SF Pro, system-ui | Same (consistency is the point) |
> | Confident | Satoshi, GT Walsheim, Geist | Geist, Inter |
> | Warm | Instrument Sans, Plus Jakarta Sans | Same or slightly different weight |
> | Sharp | JetBrains Mono (display), Space Grotesk | Geist, Inter |
> | Bold | Clash Display, Syne, Cabinet Grotesk | Geist, DM Sans |

#### 3.2 design-fix SKILL.md (NEW)

**File:** `design/skills/design-fix/SKILL.md`

Auto-correction skill with approval workflow. **Target:** 150-200 lines.

**Two modes, single pipeline:**

| Mode | Trigger | Finding Source |
|------|---------|---------------|
| Post-review | Same session as `/design:review` | Reads review findings from conversation context |
| Standalone | `/design:fix` with no prior review | Runs guard checks internally to collect findings |

**Design constraint (learnings-researcher):** design-fix is a check **consumer**, never a check **author**. It loads checks from `guard-checks.md` and layer files. It does not define its own check logic.

**Approval flow:**

```
================================================================
DESIGN FIX: src/components/ActivityFeed.tsx
================================================================

ERRORS (4 issues — all auto-fixable)
────────────────────────────────────
  [aesthetic] Line 12: Hardcoded hex `text-[#333]`
              Fix: Replace with `text-foreground`
  [a11y]     Line 22: Touch target too small (24px)
              Fix: Change `h-6 w-6` to `min-h-11 min-w-11 p-2`
  ...

WARNINGS (3 issues)
SUGGESTIONS (2 issues — require judgment, not auto-applied)
================================================================

Apply fixes?
  1. Fix all errors and warnings (7 issues)
  2. Fix errors only (4 issues)
  3. Don't fix — I'll handle it manually
  4. Select specific issues to skip
```

**Auto-fix confidence tiers (design-fix-specialist):**

| Tier | Examples | Auto-fixable? |
|------|----------|---------------|
| **High confidence** | Hardcoded color → token, missing aria-label on icon buttons, touch target < 44px, missing focus ring, missing `lang`, spacing snap to grid, measure → `max-w-prose`, animation without `motion-safe:` | Always safe |
| **Medium confidence** | Non-keyboard handler → `<button>`, font family replacement, elevation adjustment, weight normalization, `inert` attribute | Usually safe, edge cases exist |
| **Low confidence** | Color-only differentiator, vague link text, visual hierarchy, scale violation, weight/typeface sprawl, DOM vs visual order, image alt text | Requires human judgment — never auto-applied |

**Auto-fix config in CLAUDE.md:**

```markdown
**Auto-fix:** none
```

| Value | Behavior |
|-------|----------|
| `none` | Present all findings for manual approval (default) |
| `errors` | Auto-apply error fixes; present warnings for approval |
| `errors-warnings` | Auto-apply errors + warnings; present suggestions for approval |
| `all` | Auto-apply errors + warnings; present suggestions for approval (same as errors-warnings but suggestions shown) |
| `silent` | Auto-apply errors + warnings, skip suggestions, no prompt. For CI/pre-commit. |

**Auto-fix boundary (architecture-strategist):** Never auto-fix judgment checks (J1-J5) or low-confidence items regardless of config. These require human decision-making.

**Re-scoring:** After applying fixes, run composite score and show before/after delta (when post-review) or absolute score (when standalone).

**Review Phase 3 handoff:** design-review's existing Phase 3 (Fix) becomes a handoff point:
- If `Auto-fix: silent`: immediately run fix pipeline inline
- If `Auto-fix: errors` or `errors-warnings`: auto-apply configured tier, ask about rest
- If `Auto-fix: none` (default): suggest `/design:fix` or offer inline fix

**Error handling:**
- Fix introduces new issues → flagged for human review (never auto-fixed, prevents loops)
- File changed since review → skip with explanation ("Expected: `text-[#333]`, Found: `text-foreground` (already fixed?)")
- Multi-file: failures in one file don't block others

---

### Phase 4: Write Commands

All commands follow the thin-wrapper pattern — load the skill and pass arguments.

#### 4.1 commands/direction.md

```yaml
---
name: design:direction
description: Explore and define your project's visual direction through structured dialogue
argument-hint: "[amend | variant | fresh]"
---
```

```markdown
# /design:direction

Load the design-direction skill and execute with $ARGUMENTS.

See [SKILL.md](../skills/design-direction/SKILL.md) for full instructions.
```

#### 4.2 commands/brief.md

```yaml
---
name: design:brief
description: Generate design constraints from all active layers before coding UI
argument-hint: "[preset-name]"
---
```

```markdown
# /design:brief

Load the design-brief skill and execute with $ARGUMENTS.

See [SKILL.md](../skills/design-brief/SKILL.md) for full instructions.
```

#### 4.3 commands/review.md

```yaml
---
name: design:review
description: Run full design quality review with composite scoring (/100)
argument-hint: "[file-path | component-name | all]"
---
```

```markdown
# /design:review

Load the design-review skill and execute with $ARGUMENTS.

See [SKILL.md](../skills/design-review/SKILL.md) for full instructions.
```

#### 4.4 commands/fix.md

```yaml
---
name: design:fix
description: Apply design fixes grouped by severity with user approval
argument-hint: "[file-path | all]"
---
```

```markdown
# /design:fix

Load the design-fix skill and execute with $ARGUMENTS.

See [SKILL.md](../skills/design-fix/SKILL.md) for full instructions.
```

#### 4.5 commands/a11y.md

This is the one non-thin-wrapper command — it bypasses the orchestrator and loads only the a11y layer directly. Migrate from existing `commands/a11y.md`, updating paths to relative.

```yaml
---
name: design:a11y
description: Run standalone accessibility and visual design audit (a11y layer only)
argument-hint: "[file-path | component-name]"
---
```

The body contains its own setup + execution instructions (~40 lines) rather than delegating to a skill. This is an intentional asymmetry — 4 commands map to dedicated skills, 1 maps to a layer.

---

### Phase 5: Plugin Metadata & Documentation

#### 5.1 CREDITS.md

| Source | What it inspired | Author | License |
|--------|-----------------|--------|---------|
| **Frontend Design Skill** | Creative direction, anti-AI-slop aesthetics | Anthropic (Prithvi Rajasekaran, Alexander Bricken) | Apache 2.0 |
| **RAMS** | Accessibility layer scope and structure | Brian Lovin | Unknown |
| **bringhurst-typography** | Encoding Bringhurst's typographic rules as an AI skill | agrimsingh | Not specified |
| **Compound Engineering** | Workflow loop (plan, work, review, compound) | Kieran Klaassen / Every, Inc. | MIT |
| **The Elements of Typographic Style** | Typographic principles (measure, rhythm, scale, weight, restraint) | Robert Bringhurst (book) | N/A |

All content is original prose. Sources credited as inspirations, not dependencies.

**Note (simplicity-reviewer):** For MVP, a `<!-- Inspired by ... -->` comment in the orchestrator is sufficient. CREDITS.md is for distribution.

#### 5.2 CHANGELOG.md

```markdown
# Changelog

## [1.0.0] - 2026-02-XX

### Added
- Plugin packaging with `.claude-plugin/plugin.json`
- `/design:direction` — creative exploration skill
- `/design:fix` — auto-correction with approval workflow
- MIT license
- CREDITS.md with inspiration sources

### Changed
- Layer rename: `bringhurst` → `craft` (check IDs B1-B6 → C1-C6)
- Preset renames: `linear-mercury` → `clean-functional`, `stripe-vercel` → `premium-depth`, `apple-notion` → `refined-simple`
- All absolute paths → relative (plugin-portable)
- YAML frontmatter corrected (`user_invocable` → `user-invocable`)
- Three-way severity sync resolved (guard-checks.md is now canonical)
- Check IDs unified to prefixed scheme across all files

### Removed
- Manual installation requirement (now a plugin)
- Duplicate guard severity section from a11y.md
```

#### 5.3 README.md

Plugin overview: what it does, component counts, installation, workflow diagram, lean CLAUDE.md config example, available commands, credits.

#### 5.4 Plugin CLAUDE.md

Development instructions for contributing to the plugin:
- Pre-commit checklist (version bump in plugin.json, changelog update)
- Naming conventions: check IDs use prefixed scheme (C for craft, A/M/V for a11y, E for aesthetic, J for judgment)
- Severity vocabulary: always `Error / Warning / Suggestion`
- Severity authority: a11y.md check tables → guard-checks.md derives from them
- Reference link convention: `[filename.md](./path/filename.md)` not backtick paths
- How to add a new layer or preset
- Command naming: `name: design:<command>` in frontmatter

---

### Phase 6: CLAUDE.md Config Updates

#### 6.1 Lean project CLAUDE.md example (~10 lines)

The plugin provides the skills. Projects only need a config section:

```markdown
## Design Quality

**Layers:** craft, a11y
**Aesthetic:** clean-functional
**Strictness:** standard
**Teaching:** normal
**Auto-fix:** none
```

#### 6.2 Update global CLAUDE.md

Replace the current `~/.claude/CLAUDE.md` Design Quality section:

**Remove:** All `~/.claude/skills/design/...` path references, manual skill loading instructions, old preset names, old layer names

**Add:** Brief note that the `design` plugin provides all design quality capabilities. Reference plugin commands (`/design:direction`, `/design:brief`, `/design:review`, `/design:fix`, `/design:a11y`). Keep a minimal CLAUDE.md instruction: "The `design` plugin provides design quality capabilities. Active layers and config are in `## Design Quality` below."

#### 6.3 Update repo CLAUDE.md

Same changes as global — lean config pointing to plugin.

---

### Phase 7: Cleanup & Verification

#### 7.1 Pre-deletion inventory (learnings-researcher)

Before deleting anything, discover all installed files:

```bash
# Build installation inventory
find ~/.claude/commands/ -name "*.md" -type f 2>/dev/null | grep -i "design\|a11y\|brief\|review"
find ~/.claude/skills/design/ -type f 2>/dev/null
# Also check wrong paths (lesson from a11y replacement)
find ~/.claude/skills/commands/ -name "*.md" -type f 2>/dev/null
```

#### 7.2 Delete old file structure

After plugin is verified working:
- Delete `skills/design/` directory (old location in repo root)
- Delete `commands/` directory entries that moved to plugin
- Delete installed copies under `~/.claude/skills/design/`
- Delete installed copies under `~/.claude/commands/` (a11y.md, design-brief.md, design-review.md)

#### 7.3 Verification checklist

```bash
# No absolute paths remain
grep -r "~/.claude/skills" design/ | wc -l  # Should be 0

# No old names remain (except in CREDITS/CHANGELOG/aliases)
grep -r "linear-mercury" design/ --include="*.md" | grep -v CREDITS | grep -v CHANGELOG  # Should be 0
grep -r "bringhurst" design/ --include="*.md" | grep -v CREDITS | grep -v CHANGELOG | grep -v "Inspired by"  # Should be 0

# No old frontmatter bugs
grep -r "user_invocable" design/ --include="*.md"  # Should be 0

# Plugin structure is valid
cat design/.claude-plugin/plugin.json | python3 -m json.tool  # Should parse

# All commands exist
ls design/commands/  # direction.md brief.md review.md fix.md a11y.md

# All skills exist
ls design/skills/*/SKILL.md  # 5 SKILL.md files

# Severity sync: no guard section remains in a11y.md
grep -c "## Guard Checks" design/skills/design-quality/layers/a11y.md  # Should be 0

# Post-deletion: old files are gone
test ! -f ~/.claude/commands/a11y.md && echo "OK" || echo "STALE"
test ! -d ~/.claude/skills/design/ && echo "OK" || echo "STALE"
```

#### 7.4 Multi-agent code review (learnings-researcher)

Before shipping, run multi-perspective review checking:
1. Internal consistency of all renamed references (craft/bringhurst, preset names)
2. Path correctness for all relative path conversions
3. Severity alignment across a11y.md, guard-checks.md, review-rubric.md
4. No orphaned cross-references between skills
5. Check ID uniqueness (no prefix collisions)

#### 7.5 Functional verification

- [ ] Install plugin and verify auto-loading when working on UI files
- [ ] `/design:direction` — run, generate a custom preset, verify CLAUDE.md update
- [ ] `/design:brief` — verify craft + a11y + aesthetic layers all generate constraints
- [ ] Guard checks fire inline during coding (test with intentional violations)
- [ ] `/design:review` — verify composite score with all three layer sub-scores
- [ ] `/design:fix` — verify grouped presentation, approval flow, re-scoring
- [ ] `/design:a11y` — verify standalone a11y audit works
- [ ] Old command names no longer resolve (clean break)

---

## Acceptance Criteria

### Functional Requirements

- [ ] Plugin installs via Claude Code plugin system (no manual file copying)
- [ ] All 5 commands work: `/design:direction`, `/design:brief`, `/design:review`, `/design:fix`, `/design:a11y`
- [ ] Guard checks fire inline when layers are active during coding
- [ ] `/design:direction` generates a custom preset file and updates CLAUDE.md
- [ ] `/design:fix` groups findings by severity and supports approval workflow
- [ ] `/design:review` produces composite score with craft, aesthetic, and a11y sub-scores
- [ ] No absolute `~/.claude/skills/` paths in any file
- [ ] All check IDs use prefixed scheme (no flat sequential numbering)
- [ ] Severity defined in one place per concern (no three-way sync)

### Non-Functional Requirements

- [ ] MIT license applied
- [ ] CREDITS.md attributes all inspiration sources
- [ ] CHANGELOG.md documents all changes
- [ ] Lean CLAUDE.md config (< 10 lines for project config)
- [ ] All skills under 500 lines
- [ ] Frontmatter uses `user-invocable` (hyphen, not underscore)

---

## Dependencies & Risks

### Dependencies

| Dependency | Impact | Mitigation |
|-----------|--------|-----------|
| Claude Code relative path resolution | All cross-skill loading depends on this | Test `../` in Phase 2 before proceeding; fall back to `${CLAUDE_PLUGIN_ROOT}` if needed |
| Claude Code plugin command namespacing | `/design:*` format must work | Verified against compound-engineering which uses same pattern |
| `name` field in command frontmatter | Required for proper namespacing per compound-engineering pattern | Add `name: design:<cmd>` to all command files |

### Risks

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|-----------|
| `../` paths don't resolve across skill dirs | Medium | High — blocks cross-skill loading | Use `${CLAUDE_PLUGIN_ROOT}` fallback; test in Phase 2 before any content changes |
| Preset renames break muscle memory | Medium | Low — temporary confusion | Can defer renames to v1.1 if needed |
| Direction skill produces generic presets | Medium | Medium — undermines anti-slop goal | Curated font mapping per mood; "signature move" requirement in Philosophy; test with diverse inputs |
| New issues introduced by auto-fix | Medium | Medium — user sees regressions | Post-fix validation scan; new issues from fixes are never auto-fixed |
| Check ID renumbering introduces errors | Medium | Medium — breaks guard checks | Multi-agent code review in Phase 7.4 |

---

## Open Questions

| # | Question | Assumed Default | Research Finding |
|---|----------|----------------|-----------------|
| 1 | Direction output file location | `<project>/.claude/presets/<name>.md` | Confirmed by direction-specialist. Resolution order: project-local first, plugin built-ins fallback. |
| 2 | Direction behavior when preset exists | Ask: amend, variant, or fresh | Specialist added partial invocation: `/design:direction colors` for single-section changes |
| 3 | Fix approval granularity | Group-level with per-issue skip | Specialist designed full UX with 4 options + selective exclusion |
| 4 | Fix re-scoring | Yes — delta format post-review, absolute format standalone | Confirmed with sample output |
| 5 | Does /design:fix require prior /design:review? | No — standalone runs guard checks internally | Specialist: findings from conversation context (post-review) or fresh scan (standalone) |
| 6 | Should backward-compat aliases be added? | No for MVP. Only user controls both CLAUDE.md files. | Simplicity reviewer: just rename and update configs. No alias infrastructure. |
| 7 | Should preset renames happen in v1.0? | Yes per brainstorm, but deferrable | Simplicity reviewer: current names are more descriptive. Can defer to v1.1. |
| 8 | Auto-fix scope | Errors + warnings only. Never judgment checks. | Architecture: restrict to high/medium confidence tiers per fix-specialist classification |

---

## Success Metrics

- Plugin installs cleanly via Claude Code plugin system
- All workflow steps function correctly in sequence
- Zero absolute paths in the plugin
- All inspiration sources properly credited
- Check IDs unified — no dual numbering
- Severity sync eliminated — one source of truth per concern
- Guard checks auto-load via keyword matching (no explicit path in CLAUDE.md)

---

## References & Research

### Internal References
- Brainstorm: `docs/brainstorms/2026-02-20-design-plugin-restructure-brainstorm.md`
- A11y layer solution doc: `docs/solutions/code-quality/a11y-layer-licensing-and-coverage-upgrade.md`
- Plugin format reference: `~/.claude/plugins/cache/every-marketplace/compound-engineering/2.31.1/.claude-plugin/plugin.json`
- Frontend Design skill: `~/.claude/plugins/marketplaces/claude-plugins-official/plugins/frontend-design/skills/frontend-design/SKILL.md`
- Skill authoring guide: `~/.claude/plugins/cache/every-marketplace/compound-engineering/2.31.1/skills/create-agent-skills/SKILL.md`
- Plugin structure guide: `~/.claude/plugins/marketplaces/claude-plugins-official/plugins/plugin-dev/skills/plugin-structure/SKILL.md`

### External References
- [Claude Code Plugins Reference](https://code.claude.com/docs/en/plugins-reference) — official plugin.json schema, path resolution, namespacing
- [Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) — frontmatter fields, length limits, writing style
- [GitHub Issue #11328](https://github.com/anthropics/claude-code/issues/11328) — agent namespace bug (closed NOT_PLANNED)
- [Anthropic Frontend Design Skill](https://github.com/anthropics/courses/tree/master/prompt_engineering_interactive_tutorial/skills/frontend-design) — Apache 2.0
- [WCAG 2.2 Quick Reference](https://www.w3.org/WAI/WCAG22/quickref/) — W3C, October 2023

### Existing Files to Modify (Summary)

| File | Change |
|------|--------|
| `skills/design/design-quality/layers/bringhurst.md` | **COPY + RENAME** → `design/skills/design-quality/layers/craft.md` |
| `skills/design/design-quality/layers/a11y.md` | **COPY** → `design/skills/design-quality/layers/a11y.md` |
| `skills/design/design-quality/presets/*.md` (3 files) | **COPY + RENAME** to style names |
| `skills/design/design-quality/references/*.md` (2 files) | **COPY** + update references |
| `skills/design/design-quality/SKILL.md` | **COPY** + major updates (paths, names, config, loading centralization) |
| `skills/design/design-brief/SKILL.md` | **COPY** + update paths, names, delegate loading |
| `skills/design/design-review/SKILL.md` | **COPY** + update paths, names, Phase 3 handoff, delegate loading |
| `commands/a11y.md` | **COPY** + update paths |
| `design/skills/design-direction/SKILL.md` | **CREATE** (new creative exploration skill) |
| `design/skills/design-fix/SKILL.md` | **CREATE** (new auto-correction skill) |
| `design/commands/direction.md` | **CREATE** |
| `design/commands/brief.md` | **CREATE** |
| `design/commands/review.md` | **CREATE** |
| `design/commands/fix.md` | **CREATE** |
| `design/.claude-plugin/plugin.json` | **CREATE** |
| `design/LICENSE` | **CREATE** |
| `design/README.md` | **CREATE** |
| `design/CHANGELOG.md` | **CREATE** |
| `design/CREDITS.md` | **CREATE** |
| `design/CLAUDE.md` | **CREATE** (dev instructions) |
| `~/.claude/CLAUDE.md` | **UPDATE** — lean config, remove manual paths |
| `CLAUDE.md` (repo root) | **UPDATE** — lean config, remove manual paths |
| Old `skills/design/` + `commands/` | **DELETE** (Phase 7 only, after verification) |
| Old `~/.claude/skills/design/` + `~/.claude/commands/` | **DELETE** (Phase 7 only, after verification) |
