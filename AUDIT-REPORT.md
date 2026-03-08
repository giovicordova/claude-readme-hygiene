# Claude Code Audit Report

> Generated on 2026-03-08

## Project Understanding

Develop and maintain the `claude-readme-hygiene` skill and its companion `docs-hygiene.md` rule from a dedicated workfolder, with symlinks ensuring changes propagate to `~/.claude/`.

## Current State

| Area | Status |
|------|--------|
| CLAUDE.md | Missing |
| Skills | 1 found (SKILL.md — the skill being developed) |
| Sub-agents | None |
| Hooks | Not configured |
| MCP | Not configured |
| Permissions | Default |
| Settings | Default |
| Rules | 1 found (docs-hygiene.md — the rule being developed) |

## Findings

### CLAUDE.md

#### [improve] Missing project CLAUDE.md

- **Current**: No CLAUDE.md exists. Claude has no persistent context about what this project is or how it should be worked on.
- **Recommendation**: Create a minimal CLAUDE.md that tells Claude this is a skill development workfolder, that SKILL.md and docs-hygiene.md are symlinked into `~/.claude/`, and that changes propagate instantly. This prevents Claude from treating this as a generic project and avoids accidentally overwriting the skill with a hygiene audit of itself.
- **Project relevance**: Without this, every new session starts blind. Claude might try to "fix" these files according to the skill's own rules, creating a recursive mess.
- **Source**: [Write effective instructions](https://docs.anthropic.com/en/docs/claude-code/memory#write-effective-instructions)

### Skills

#### [good] SKILL.md is well-structured and within size limits

- **Current**: 282 lines with clear YAML frontmatter (`name`, `description`), structured workflow (7 steps), templates with examples, and a separation test. Description is specific enough for Claude to know when to activate it.
- **Project relevance**: This is the product — it's well-crafted.
- **Source**: [Configure skills](https://docs.anthropic.com/en/docs/claude-code/skills#configure-skills)

#### [improve] Description could include negative triggers

- **Current**: Description says what the skill does but not what it doesn't do. The `claude-md-management` plugin covers overlapping territory (`revise-claude-md`, `claude-md-improver`).
- **Recommendation**: Add a clause to the description distinguishing this skill from the plugin, e.g., "Use this instead of claude-md-management when you need to audit both CLAUDE.md and README.md together, enforce the separation test, or create either file from scratch."
- **Project relevance**: With both installed, Claude may pick the wrong one or trigger both. A clearer description prevents misfires.
- **Source**: [Skill not triggering / triggers too often](https://docs.anthropic.com/en/docs/claude-code/skills#skill-not-triggering)

#### [improve] SKILL.md exceeds recommended 500-line soft limit awareness

- **Current**: At 282 lines the skill is within limits, but it includes two full template examples (minimal and complex) that are reference material, not instructions.
- **Recommendation**: Move the template examples to a supporting file like `examples.md` and reference it from SKILL.md: `For template examples, see [examples.md](examples.md)`. This keeps the core instructions focused and reduces token cost when the skill loads.
- **Project relevance**: Every token in SKILL.md loads into context when the skill activates. Leaner instructions = better adherence.
- **Source**: [Add supporting files](https://docs.anthropic.com/en/docs/claude-code/skills#add-supporting-files)

#### [improve] No `argument-hint` in frontmatter

- **Current**: The skill can be invoked as `/claude-readme-hygiene` but the autocomplete menu gives no hint about expected arguments.
- **Recommendation**: Add `argument-hint: "[audit | create | rewrite]"` to the frontmatter so users see what actions are available during autocomplete.
- **Project relevance**: Gio doesn't code — clear affordances in the UI reduce friction.
- **Source**: [Frontmatter reference](https://docs.anthropic.com/en/docs/claude-code/skills#frontmatter-reference)

### Sub-agents

#### [good] No sub-agents needed

- **Current**: None configured.
- **Project relevance**: This is a single-skill project. Sub-agents would add unnecessary complexity. Correct as-is.
- **Source**: [Create custom subagents](https://docs.anthropic.com/en/docs/claude-code/sub-agents)

### Hooks

#### [good] No hooks needed

- **Current**: None configured.
- **Project relevance**: There's no build step, no tests, no CI. Hooks have nothing deterministic to enforce here. Correct as-is.
- **Source**: [Automate workflows with hooks](https://docs.anthropic.com/en/docs/claude-code/hooks-guide)

### MCP

#### [good] No MCP servers needed

- **Current**: None configured.
- **Project relevance**: The skill doesn't interact with external services. Correct as-is.
- **Source**: [Connect Claude Code to tools via MCP](https://docs.anthropic.com/en/docs/claude-code/mcp)

### Permissions

#### [good] Default permissions are appropriate

- **Current**: Default permission mode.
- **Project relevance**: This is a documentation-only project. No dangerous commands to allowlist or deny. Correct as-is.
- **Source**: [Configure permissions](https://docs.anthropic.com/en/docs/claude-code/permissions)

### Settings

#### [good] No custom settings needed

- **Current**: Default settings.
- **Project relevance**: Single-file skill project. No overrides necessary. Correct as-is.
- **Source**: [Claude Code settings](https://docs.anthropic.com/en/docs/claude-code/settings)

### Rules

#### [good] docs-hygiene.md rule is concise and focused

- **Current**: 3 bullet points, proper YAML frontmatter with `description`. Covers read-before-work, update-after-change, and flag-contradictions.
- **Project relevance**: This is the companion product — it's clean and effective.
- **Source**: [Organize rules with .claude/rules/](https://docs.anthropic.com/en/docs/claude-code/memory#organize-rules-with-clauderules)

#### [improve] Rule lacks `paths` scoping

- **Current**: The rule loads for every project globally. It applies to all files in all contexts.
- **Recommendation**: This is intentional — the rule should fire everywhere. However, consider whether a `description` field alone is sufficient or if the rule text should explicitly mention it's global by design. No change needed unless scope narrows in the future.
- **Project relevance**: Informational — the current behavior is correct for a global rule.
- **Source**: [Path-specific rules](https://docs.anthropic.com/en/docs/claude-code/memory#path-specific-rules)

### Feature Selection

#### [good] Minimal setup matches project scope

- **Current**: 1 skill + 1 rule. No over-engineering.
- **Project relevance**: This is a documentation artifact, not a software project. The setup is appropriately light.
- **Source**: [Match features to your goal](https://docs.anthropic.com/en/docs/claude-code/features-overview#match-features-to-your-goal)

#### [fix] No git repository initialized

- **Current**: This workfolder is not a git repo. Changes to SKILL.md and docs-hygiene.md are not version-controlled.
- **Recommendation**: Initialize a git repo (`git init`) and make an initial commit. This enables checkpointing, rewind, and history tracking for the skill as it evolves. It also enables publishing to GitHub if desired.
- **Project relevance**: Without git, there's no undo for skill edits. One bad session could overwrite the skill with no recovery path.
- **Source**: [Best Practices](https://docs.anthropic.com/en/docs/claude-code/best-practices)

## Priority Actions

1. **[fix]** Initialize git repo — no version control means no safety net for skill edits
2. **[improve]** Create a minimal CLAUDE.md — prevents Claude from treating this as a generic project or recursively auditing itself
3. **[improve]** Add `argument-hint` to SKILL.md frontmatter — better UX in the autocomplete menu
4. **[improve]** Move template examples to a supporting file — reduces token cost when skill loads
5. **[improve]** Sharpen skill description to distinguish from `claude-md-management` plugin — prevents misfires

## Next Steps

To implement these recommendations:

1. Start a new Claude Code session
2. Enter Plan mode (Shift+Tab)
3. Share this report: "Read AUDIT-REPORT.md and create an implementation plan for the recommendations"
4. Review the plan, then switch to Normal mode to execute
