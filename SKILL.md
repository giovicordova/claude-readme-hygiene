---
name: claude-readme-hygiene
argument-hint: "[audit | create | rewrite]"
description: Audit, create, or rewrite CLAUDE.md and README.md. Ensures CLAUDE.md stays minimal and operator-only (complementing the global ~/.claude/CLAUDE.md without duplicating it), and README is publication-ready for GitHub expert review. Auto-creates either file from templates if missing. Use this instead of claude-md-management when you need both files audited together, the separation test enforced, or either file created from scratch.
---

# CLAUDE.md & README.md Hygiene

## Core Principle

These two files serve fundamentally different audiences and must never overlap.

| File | Audience | Purpose |
|------|----------|---------|
| `CLAUDE.md` (project) | Claude Code agent | Operator config: only what Claude cannot infer from code alone AND what isn't already in the global `~/.claude/CLAUDE.md` |
| `README.md` | Human developers | Publication: the front page of the repo on GitHub — install, use, contribute |

**The inheritance model:** Think of it like a mixing console. The global `~/.claude/CLAUDE.md` is the master bus — communication style, error recovery, git conventions, safety boundaries, coherency rules. The project `CLAUDE.md` is a channel strip — it only adds what's unique to *this specific project*. Never duplicate the master bus on a channel strip.

---

## Activation

Use when the user asks to:
- Audit, fix, clean up, or rewrite CLAUDE.md and/or README.md
- Make README "GitHub-ready" or "expert-reviewable"
- Reduce bloat in CLAUDE.md or improve README quality
- Create these files for a new project
- Run a general hygiene check on project documentation

Also run proactively when the Coherency Rule (from global CLAUDE.md) triggers after a structural change.

---

## Workflow

Execute these steps in order. Do not write anything until Step 7.

### Step 1 — Discover

Glob for `CLAUDE.md`, `README.md`, and any `@`-imported files referenced within CLAUDE.md (e.g., `@path/to/file`). Also check for `.planning/` folder (GSD workflow).

Note which files exist and which are missing.

### Step 2 — Read Global CLAUDE.md

Read `~/.claude/CLAUDE.md` to understand what rules are already inherited globally. Everything in the global file is **off-limits for the project CLAUDE.md** — do not duplicate, restate, or paraphrase any global rule.

### Step 3 — Read Project Files

Load full content of all discovered project files (CLAUDE.md, README.md, any @-imports).

### Step 4 — Analyze the Codebase

Read these to build a list of facts Claude can already infer from code:
- Package manifest: `package.json`, `pyproject.toml`, `Cargo.toml`
- Build/task config: `Makefile`, `justfile`, `Taskfile.yml`
- Lock files: `package-lock.json`, `uv.lock`, `Cargo.lock`
- CI: `.github/workflows/*.yml`, `.gitlab-ci.yml`
- Linters/formatters: `.eslintrc*`, `prettier.config*`, `ruff.toml`, `pyproject.toml [tool.ruff]`, `biome.json`, `rustfmt.toml`
- Top-level directory structure (`ls`)
- Type definitions and state management files (if present)

### Step 5 — Audit or Draft CLAUDE.md

**If CLAUDE.md exists:** Apply the CLAUDE.md rules below. Produce a line-by-line verdict: keep, remove, or rewrite — with reasoning. Flag any lines that duplicate the global file.

**If CLAUDE.md does not exist:** Draft one from the Project CLAUDE.md Template below, populated with facts from Step 4.

### Step 6 — Audit or Draft README.md

**If README.md exists:** Apply the README.md rules below. Note missing required sections, broken links, quality violations.

**If README.md does not exist:** Draft one from the README.md Rules below, populated with facts from Step 4.

### Step 7 — Show Diffs and Confirm

Present a clear diff for each file (or the full draft for new files). Request explicit confirmation before writing any changes.

---

## Project CLAUDE.md Rules

Project CLAUDE.md is a **terse, machine-readable operator file**. It is never read by humans visiting the repo. It **inherits all behavior from the global `~/.claude/CLAUDE.md`** and must only contain project-specific additions or overrides.

### What belongs here (keep/add only if):

- It is a Bash command Claude needs to know for THIS project (build, test, lint, dev server).
- It is a code style rule NOT already enforced by an existing linter/formatter config in the repo.
- It is a project-specific convention Claude would get wrong without it (naming patterns, state management approach, architectural patterns in use).
- It is a known gotcha or landmine specific to this codebase ("this file looks unused but is loaded dynamically", "this API returns XML not JSON").
- It is a `@path/to/file` import pointer for deferred context loading.
- It is an explicit **override** of a global CLAUDE.md rule (e.g., "Override: no auto-commits in this project — we use staged PRs").
- It identifies which files/folders are the key structural landmarks of this project.

### Remove a line if any condition is true:

- It duplicates or restates ANYTHING from the global `~/.claude/CLAUDE.md` (communication style, git format, error recovery, safety boundaries, ripple/rewrite rules, coherency rules, the "Who I Am" section, or any behavioral rule).
- It describes project purpose, features, installation, or usage (that belongs in README).
- It restates something already inferable from `package.json`, CI config, or linter config (e.g., "use TypeScript" when `tsconfig.json` exists).
- It is generic best-practice advice Claude already follows by default or via the global file.
- It is a section header or prose paragraph with no actionable rule inside.

### Format constraints:

- **Hard limit: ~30 lines** for a typical single-package project; ~60 lines for a complex monorepo.
- Imperative, terse style: `Run \`npm test\` before committing.` NOT `You should always make sure to run the test suite before any commit.`
- Bullet points or short labeled sections only. Zero prose paragraphs.
- No README-style sections: no Overview, Features, Architecture, or Getting Started headers.

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

For worked examples, see [examples.md](examples.md).

---

## README.md Rules

README is a **human-facing public document**. A developer unfamiliar with the project must be able to install and use it within 2 minutes of reading.

### Required sections (in this order):

1. **Title + tagline** — `# ProjectName` then one sentence: what it is and who it is for.
2. **Badges** (recommended) — CI status, version, license, coverage.
3. **Overview** — 2-4 sentences: problem solved, approach, target user.
4. **Installation** — exact copy-pasteable commands; note OS requirements.
5. **Usage** — at least one realistic, runnable example with a code block.
6. **Configuration** (if applicable) — env vars or key options as a table with columns: Name | Default | Description.
7. **Contributing** — how to run tests, branch naming, PR expectations.
8. **License** — one line + link or badge.

### Quality rules:

- Every code block must have a language tag (` ```bash `, ` ```ts `, etc.).
- Verify all local markdown links resolve to files that actually exist.
- Use second person ("you") and active voice throughout.
- Describe behaviour and interface, not internal implementation.
- No Claude-specific instructions, no session-scoped notes, no internal tooling references.
- No placeholder sections (`## Contributing — TODO`). Either write it or omit it.

### What belongs in README that does NOT belong in CLAUDE.md:

- Project description and features (for humans discovering the project)
- Installation and setup instructions
- Usage examples and API documentation
- Contributing guidelines
- License information
- Screenshots, badges, links
- Architecture overview (if useful for human contributors)

---

## The Separation Test

For every line in either file, apply this test:

| Question | If yes → |
|----------|----------|
| Would only Claude Code need this to operate correctly? | CLAUDE.md |
| Would a human developer need this to understand/use the project? | README.md |
| Is this already in the global `~/.claude/CLAUDE.md`? | Neither — it's inherited |
| Is this already inferable from config files in the repo? | Neither — it's redundant |
| Do both Claude and humans need it? | README.md (CLAUDE.md can reference it with `See README.md`) |

---

## Hard Constraints

- Do **not** merge the two files or suggest one replaces the other.
- Do **not** pad CLAUDE.md to look comprehensive — shorter is better.
- Do **not** trim README to make it shorter for its own sake.
- Do **not** add a section to README that duplicates CLAUDE.md content.
- Do **not** duplicate ANY rule from the global `~/.claude/CLAUDE.md` in the project CLAUDE.md.
- If a file is already correct, say so explicitly and make no changes.
- When creating files from scratch, populate them with real data from the codebase analysis — never leave template placeholders like `[command]` unfilled.
- When updating existing files, reshape them to be clean and complete — don't just append. Every line earns its place.