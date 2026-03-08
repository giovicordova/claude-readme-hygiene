# Project CLAUDE.md Rules

Project CLAUDE.md is a **terse, machine-readable operator file**. It is never read by humans visiting the repo. It **inherits all behavior from the global `~/.claude/CLAUDE.md`** and must only contain project-specific additions or overrides.

## What belongs here (keep/add only if):

Per official Anthropic docs, CLAUDE.md should contain:

- Bash commands Claude can't guess (build, test, lint, dev server for THIS project).
- Code style rules that differ from defaults and aren't enforced by linter/formatter config.
- Testing instructions and preferred test runners.
- Repository etiquette (branch naming, PR conventions) specific to this project.
- Architectural decisions specific to this project.
- Developer environment quirks (required env vars, local services).
- Common gotchas or non-obvious behaviors ("this file looks unused but is loaded dynamically").
- `@path/to/file` import pointers for deferred context loading.
- Explicit **overrides** of a global CLAUDE.md rule (e.g., "Override: no auto-commits in this project — we use staged PRs").

## Remove a line if any condition is true:

Per official Anthropic docs, CLAUDE.md should NOT contain:

- Anything Claude can figure out by reading code (inferable from `package.json`, CI config, linter config, `tsconfig.json`, etc.).
- Standard language conventions Claude already knows.
- Detailed API documentation (link to docs instead).
- Information that changes frequently.
- Long explanations or tutorials.
- File-by-file descriptions of the codebase.
- Self-evident practices like "write clean code".

Additional removal criteria specific to this skill:

- It duplicates or restates ANYTHING from the global `~/.claude/CLAUDE.md` (communication style, git format, error recovery, safety boundaries, or any behavioral rule).
- It describes project purpose, features, installation, or usage (that belongs in README).
- It is a section header or prose paragraph with no actionable rule inside.

## Format constraints:

- **Target: ~30 lines** for a typical single-package project; ~60 lines for a complex monorepo. Anthropic recommends under ~200 lines absolute max — anything longer causes Claude to ignore instructions.
- Imperative, terse style: `Run `npm test` before committing.` NOT `You should always make sure to run the test suite before any commit.`
- Bullet points or short labeled sections only. Zero prose paragraphs.
- No README-style sections: no Overview, Features, Architecture, or Getting Started headers.
- If content grows past the target, move reference material to skills or `.claude/rules/` files (not into CLAUDE.md).

---

## Project CLAUDE.md Template

Use this template when creating a new CLAUDE.md. Remove any section that has nothing project-specific to say. An empty section is worse than no section.

```markdown
# Project: [Name]

[One sentence: what this project does — for Claude's context, not human onboarding]

## Stack
- [Language/framework/runtime]
- [Key dependencies that affect how code should be written]
- [Database/services/external APIs]

## Commands
- dev: `[command]`
- test: `[command]`
- lint: `[command]`
- build: `[command]`

## Structure
- `src/` — [what's in here]
- `lib/` — [what's in here]
- [any non-obvious folder or file that Claude needs to know about]

## Conventions
- [Naming patterns, state management approach, etc.]
- [Anything Claude would get wrong without being told]

## Gotchas
- [Known quirks, workarounds, things that look wrong but aren't]
- [Files that look unused but are loaded dynamically]
- [APIs or services with unexpected behavior]

## Source-of-Truth Docs
- [List files Claude must read before major changes and update after structural changes]
- [e.g., `.planning/` folder if using GSD workflow]

## Global Overrides
- [Only if something from ~/.claude/CLAUDE.md doesn't apply to this project]
- [e.g., "Override: no auto-commits — this project uses staged PRs"]
```

For worked examples, see [examples.md](../examples.md).
