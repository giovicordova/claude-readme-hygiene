# Project: claude-readme-hygiene

Skill development workfolder for the `claude-readme-hygiene` Claude Code skill.

## Structure
- `skill/SKILL.md` — the skill definition (`~/.claude/skills/claude-readme-hygiene/` → `skill/`)
- `skill/examples.md` — template examples referenced by SKILL.md
- `skill/references/claude-md-rules.md` — CLAUDE.md rules, removal criteria, format constraints, and template
- `skill/references/readme-rules.md` — README.md required sections, quality rules, and scope boundaries
- `rule/docs-hygiene.md` — companion global rule (`~/.claude/rules/docs-hygiene.md` → `rule/docs-hygiene.md`)

## Gotchas
- Changes here propagate instantly via symlinks to `~/.claude/`. Test carefully.
- Do NOT run the hygiene skill on this project — it would recursively audit itself.
- This is a documentation artifact, not a software project. No build, test, or lint commands.
