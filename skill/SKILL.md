---
name: claude-readme-hygiene
argument-hint: "[audit | create | rewrite]"
description: Audit, create, or rewrite CLAUDE.md and README.md. Ensures CLAUDE.md stays minimal and operator-only (complementing the global ~/.claude/CLAUDE.md without duplicating it), and README is publication-ready for GitHub expert review. Auto-creates either file from templates if missing. Use this instead of claude-md-management when you need both files audited together, the separation test enforced, or either file created from scratch.
allowed-tools: Read, Glob, Grep, Bash, Edit
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

## Dispatch

Route based on `$ARGUMENTS`:

- **`audit`** (default when no argument given): Run all steps 1–7. Analyze existing files and propose targeted fixes.
- **`create`**: Run Steps 1–2b and 4 (discover, read global, consult docs, analyze codebase). Skip Step 3. Draft both files from templates in Steps 5–6 using "create" mode. Proceed to Step 7 for confirmation.
- **`rewrite`**: Run all steps 1–7. Treat existing content as raw input but rebuild each file from scratch rather than patching. The result should read as if written fresh, not edited.

If `$ARGUMENTS` is empty or unrecognized, default to **audit**.

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

**Fallback:** If the anthropic-docs MCP is unavailable, use the rules in [references/claude-md-rules.md](references/claude-md-rules.md) as the authority instead.

### Step 3 — Read Project Files

Load full content of all discovered project files (CLAUDE.md, README.md, any @-imports).

*Skipped in `create` mode.*

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

Apply the rules from [references/claude-md-rules.md](references/claude-md-rules.md).

- **`audit` mode:** Produce a line-by-line verdict: keep, remove, or rewrite — with reasoning. Flag any lines that duplicate the global file.
- **`create` mode:** Draft from the Project CLAUDE.md Template in the references file, populated with facts from Step 4.
- **`rewrite` mode:** Rebuild the file from scratch using existing content as raw input. The result should meet all rules as if newly created.

### Step 6 — Audit or Draft README.md

Apply the rules from [references/readme-rules.md](references/readme-rules.md).

- **`audit` mode:** Note missing required sections, broken links, quality violations.
- **`create` mode:** Draft from the README rules, populated with facts from Step 4.
- **`rewrite` mode:** Rebuild the file from scratch. Preserve accurate information but restructure and rewrite for clarity.

### Step 7 — Show Diffs and Confirm

Present a clear diff for each file (or the full draft for new files). Request explicit confirmation before writing any changes.

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
