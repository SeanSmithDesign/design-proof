---
title: "A11y Layer: Licensing & Coverage Upgrade"
date: 2026-02-20
category: code-quality
tags:
  - accessibility
  - wcag-2.2
  - layer-replacement
  - refactoring
  - code-review
component: design-quality-engine
problem_type: refactoring
severity: critical
status: resolved
related_commits:
  - e124bdb
  - 4ad8102
---

# A11y Layer: Licensing & Coverage Upgrade

Replaced the RAMS accessibility layer (adapted from Brian Lovin's work, no license) with a 100% original a11y layer. Expanded coverage from WCAG 2.1 only (31 checks) to WCAG 2.1 + 2.2 + Modern HTML (46 checks). Fixed 6 consistency issues found during post-implementation code review.

## Problem

The design quality engine's accessibility layer ("RAMS") had two issues:

1. **Attribution risk**: Content was adapted from Brian Lovin's work without a license. Using it in a distributed skill system created ambiguity around attribution obligations.
2. **Coverage gaps**: RAMS covered only WCAG 2.1 (June 2018). WCAG 2.2 (October 2023 W3C Recommendation) added four new success criteria. Modern HTML patterns (`<dialog>`, `inert`, Popover API) weren't covered at all.

## Root Cause

The original RAMS layer was built by referencing an existing accessibility checklist rather than writing from WCAG specifications directly. This created both the attribution concern and a natural ceiling on coverage — the layer could only contain what the source material contained.

## Solution

### Phase 1: Layer Replacement (commit e124bdb)

Wrote `layers/a11y.md` from scratch using WCAG specifications as the primary source. Structure:

| Section | IDs | Count | Coverage |
|---------|-----|-------|----------|
| WCAG 2.1 Critical | A1-A5 | 5 | Image alt, accessible names, form labels, keyboard access, link semantics |
| WCAG 2.1 Serious | A6-A12 | 7 | Focus indicators, keyboard interaction, color-only info, touch targets, contrast, page language, link text |
| WCAG 2.1 Moderate | A13-A20 | 8 | Heading hierarchy, tabIndex, ARIA roles, motion, live regions, skip nav, autocomplete, page title |
| WCAG 2.2 | A21-A24 | 4 | Accessible auth (3.3.8), redundant entry (3.3.7), consistent help (3.2.6), dragging alternatives (2.5.7) |
| Modern HTML | M1-M4 | 4 | Native `<dialog>`, `inert`, Popover API, DOM vs visual order |
| Visual Design | V1-V18 | 18 | Layout, typography, color, component states, error messaging |
| **Total** | | **46** | |

Additional content: 11 teaching moment templates, guard severity classification, standalone `/a11y` output format.

Updated 8 files across the skill system (orchestrator, guard-checks, rubric, design-brief, design-review, command, project config, global config). Deleted old `rams.md` from repo and installed locations.

### Phase 2: Review Fixes (commit 4ad8102)

Code review (3 parallel agents: pattern-recognition, architecture-strategist, code-simplicity) found 12 issues. Fixed all P1 and P2:

| Priority | Issue | Fix |
|----------|-------|-----|
| P1 | Stale `~/.claude/commands/rams.md` not deleted | Deleted; path was `~/.claude/commands/`, not `~/.claude/skills/commands/` |
| P2 | Duplicate contrast check — A10 and V11 both test WCAG 1.4.3 | Removed V11, renumbered V12-V19 to V11-V18 |
| P2 | Guard-checks "25a" numbering anomaly | Renumbered to sequential "25" |
| P2 | Numbering collision — judgment checks 21-25 reused a11y check numbers | Renumbered judgment checks to 33-37 |
| P2 | Severity mismatches across files | Aligned: focus indicators Error->Warning, reduced motion Warning->Suggestion, consistent help Suggestion->Warning |
| P2 | Review rubric missing WCAG 2.2 + Modern HTML pass criteria | Added 6 pass criteria + 2 common violations to rubric |

## Key Technical Decisions

### Structured ID prefixes over flat numbering

Used `A-series` (WCAG), `M-series` (Modern HTML), `V-series` (Visual) instead of RAMS's flat R1-R31. This prevents numbering collisions, scales naturally for future WCAG releases (A25+), and provides semantic grouping.

### Intentional cross-layer overlap

V-series checks (e.g., V7 inconsistent weight, V8 cramped line height) intentionally overlap with bringhurst and aesthetic layers. Accessibility has a legitimate perspective on typography and spacing — different lens, different pass criteria. A single violation can be flagged by multiple layers in `/design-review`.

### Content relationship to RAMS

~60% topic overlap (same WCAG standards apply regardless of implementation), 100% original prose, ~40% entirely new coverage. The overlap is inherent to the domain — WCAG 1.4.3 contrast requirements are the same whether you write the check description yourself or adapt someone else's.

## Prevention Strategies

### 1. Single source of truth for severity

The a11y layer defines guard severity in three places: the check tables in `a11y.md`, the guard section in `a11y.md`, and `guard-checks.md`. This caused the severity mismatch issue. Future approach: treat `a11y.md` check tables as authoritative; derive guard-checks.md from them.

### 2. Verify installed file locations during deletion

When removing a skill/command, grep all possible install locations:
```bash
# Check both possible locations for commands
ls ~/.claude/commands/<name>.md
ls ~/.claude/skills/commands/<name>.md
# Check installed skill layers
ls ~/.claude/skills/design/design-quality/layers/<name>.md
```

### 3. Avoid flat sequential numbering in extensible systems

Flat numbering (1-32) breaks when sections are added independently. Use namespaced IDs (A1-A24, M1-M4, V1-V18) or semantic IDs. The "25a" anomaly was a clear signal the numbering was under strain.

### 4. Update scoring rubrics when adding check categories

New check categories (WCAG 2.2, Modern HTML) need corresponding pass criteria in the review rubric. Without them, those checks have no weight in the composite score — they exist in the layer but are invisible to `/design-review` scoring.

## Lessons Learned

1. **Flat numbering doesn't scale.** The judgment-check collision (21-25 used twice) and the "25a" workaround were symptoms of a sequential numbering system that couldn't accommodate growth. Namespaced prefixes (A/M/V) solved this immediately.

2. **Deletion requires an installation inventory.** The stale `~/.claude/commands/rams.md` was missed because there was no manifest of installed files. The developer tried the wrong path first (`~/.claude/skills/commands/`), suggesting the installation topology wasn't well-documented.

3. **Duplicate checks emerge at domain boundaries.** A10 (accessibility perspective on contrast) and V11 (visual perspective on contrast) tested the same criterion. When a concern spans two conceptual domains, pick one home and cross-reference from the other.

4. **Code review catches what authoring misses.** The 3-agent parallel review found issues across pattern consistency, architecture coherence, and simplicity that weren't apparent during the writing phase. Multi-perspective review is particularly valuable for reference documentation where internal consistency matters.

## Related Files

| File | Role |
|------|------|
| `skills/design/design-quality/layers/a11y.md` | Layer definition (46 checks, teaching moments, guard section) |
| `skills/design/design-quality/references/guard-checks.md` | Quick-reference checklist for inline guard mode |
| `skills/design/design-quality/references/review-rubric.md` | Composite scoring criteria |
| `skills/design/design-quality/SKILL.md` | Orchestrator (loads layers, manages config) |
| `skills/design/design-brief/SKILL.md` | Pre-coding constraint generation |
| `skills/design/design-review/SKILL.md` | Post-coding composite audit |
| `commands/a11y.md` | Standalone `/a11y` command entry point |

## External References

- [WCAG 2.1 Quick Reference](https://www.w3.org/WAI/WCAG21/quickref/) (W3C, June 2018)
- [WCAG 2.2 Quick Reference](https://www.w3.org/WAI/WCAG22/quickref/) (W3C, October 2023)
- WCAG version progression: 2.0 -> 2.1 -> 2.2 -> 3.0 (in draft, no release date)
