# README.md Rules

README is a **human-facing public document**. A developer unfamiliar with the project must be able to install and use it within 2 minutes of reading.

## Required sections (in this order):

1. **Title + tagline** — `# ProjectName` then one sentence: what it is and who it is for.
2. **Badges** (recommended) — CI status, version, license, coverage.
3. **Overview** — 2-4 sentences: problem solved, approach, target user.
4. **Installation** — exact copy-pasteable commands; note OS requirements.
5. **Usage** — at least one realistic, runnable example with a code block.
6. **Configuration** (if applicable) — env vars or key options as a table with columns: Name | Default | Description.
7. **Contributing** — how to run tests, branch naming, PR expectations.
8. **License** — one line + link or badge.

## Quality rules:

- Every code block must have a language tag (` ```bash `, ` ```ts `, etc.).
- Verify all local markdown links resolve to files that actually exist.
- Use second person ("you") and active voice throughout.
- Describe behaviour and interface, not internal implementation.
- No Claude-specific instructions, no session-scoped notes, no internal tooling references.
- No placeholder sections (`## Contributing — TODO`). Either write it or omit it.

## What belongs in README that does NOT belong in CLAUDE.md:

- Project description and features (for humans discovering the project)
- Installation and setup instructions
- Usage examples and API documentation
- Contributing guidelines
- License information
- Screenshots, badges, links
- Architecture overview (if useful for human contributors)
