# User Preferences

- Uses Ghostty as terminal emulator

## Design Quality

The `design` plugin provides all design quality capabilities. Commands: `/design:direction`, `/design:brief`, `/design:review`, `/design:fix`, `/design:a11y`.

**Layers:** craft, a11y
**Aesthetic:** clean-functional
**Strictness:** standard
**Teaching:** normal
**Auto-fix:** none

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
