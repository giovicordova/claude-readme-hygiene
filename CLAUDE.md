# Project: claude-readme-hygiene

Skill development workfolder for the `claude-readme-hygiene` Claude Code skill.

## Structure
- `SKILL.md` — the skill definition (symlinked into `~/.claude/skills/claude-readme-hygiene/`)
- `docs-hygiene.md` — companion global rule (symlinked into `~/.claude/rules/`)
- `examples.md` — template examples referenced by SKILL.md

## Gotchas
- Changes here propagate instantly via symlinks to `~/.claude/`. Test carefully.
- Do NOT run the hygiene skill on this project — it would recursively audit itself.
- This is a documentation artifact, not a software project. No build, test, or lint commands.
