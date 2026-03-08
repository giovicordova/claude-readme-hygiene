---
name: claude-readme-hygiene
argument-hint: "[audit | create | rewrite]"
description: Audit, create, or rewrite CLAUDE.md and README.md. Ensures CLAUDE.md stays minimal and operator-only (complementing the global ~/.claude/CLAUDE.md without duplicating it), and README is publication-ready for GitHub expert review. Auto-creates either file from templates if missing. Use this instead of claude-md-management when you need both files audited together, the separation test enforced, or either file created from scratch.
---

# CLAUDE.md & README.md Hygiene

## Core Principle

These two files serve fundamentally different audiences and must never overlap.

| File | Audience | Purpose | Loaded by |
|------|----------|---------|-----------|
| `CLAUDE.md` (project) | Claude Code agent | Persistent context loaded every session — only what Claude cannot infer from code AND what isn't already in the global `~/.claude/CLAUDE.md` | Claude Code at session start, automatically |
| `README.md` | Human developers | The front page of the repo on GitHub — install, use, contribute | Humans visiting the repo |

Per Anthropic's official docs, CLAUDE.md is **"persistent context Claude sees every session"** — coding standards, workflows, project architecture. It is context, not enforced configuration. The more concise it is, the more reliably Claude follows it. README.md is a human-facing document that has no effect on Claude's behavior.

**The inheritance model:** Think of it like a mixing console. The global `~/.claude/CLAUDE.md` is the master bus — communication style, error recovery, git conventions, safety boundaries, coherency rules. The project `CLAUDE.md` is a channel strip — it only adds what's unique to *this specific project*. Never duplicate the master bus on a channel strip.

**The official litmus test for CLAUDE.md content** (from Anthropic docs): For each line, ask *"Would removing this cause Claude to make mistakes?"* If not, cut it.

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

### Step 2b — Consult Official CLAUDE.md Guidance

Use the `anthropic-docs` MCP to fetch the current official guidance on CLAUDE.md:

1. `search_anthropic_docs` → query: `"CLAUDE.md best practices"`, source: `code`
2. `get_doc_page` → path from results, section: `"Write an effective CLAUDE.md"`

Extract the official include/exclude table and the loading/scoping rules. Use these as the **authority** when deciding what belongs in CLAUDE.md vs what should be cut. If the official guidance contradicts any rule in this skill, the official guidance wins.

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

### Remove a line if any condition is true:

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

### Format constraints:

- **Target: ~30 lines** for a typical single-package project; ~60 lines for a complex monorepo. Anthropic recommends under ~200 lines absolute max — anything longer causes Claude to ignore instructions.
- Imperative, terse style: `Run \`npm test\` before committing.` NOT `You should always make sure to run the test suite before any commit.`
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