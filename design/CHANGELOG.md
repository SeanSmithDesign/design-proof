# Changelog

## [1.0.0] - 2026-02-20

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
