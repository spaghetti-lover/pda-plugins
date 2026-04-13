# pda-plugin

Personal Claude Code plugin by Phung Duc Anh. Extends Claude Code with custom skills, commands, hooks, and scripts.

## Project structure

```
commands/          Slash commands (markdown files with frontmatter)
skills/            Skills (each dir has a SKILL.md)
hooks/             Hook definitions (hooks.json)
scripts/           Utility scripts used by hooks/commands
agents/            Custom agent definitions
docs/              Reference documentation
```

## Skills

Skills follow the [skill conventions](docs/skill-conventions.md). Key rules:

- **Naming**: gerund form with hyphens (`reviewing-security`, not `security-review`)
- **Frontmatter**: only `name` and `description` — no `allowed-tools` or other fields
- **Description**: third person, includes what it does AND when to use it
- **Conciseness**: don't explain what Claude already knows. Checklists, not paragraphs. Under 500 lines.
- **Progressive disclosure**: SKILL.md is the overview. Put detailed reference in separate files, linked one level deep.

### Code review system

`code-review` is the orchestrator skill. It spawns 4 parallel agents, each reading its own methodology file:

| Skill | Directory | Focus |
|-------|-----------|-------|
| code-review | `skills/code-review/` | Orchestrator — spawns agents, aggregates, verdicts |
| reviewing-security | `skills/reviewing-security/` | OWASP, injection, auth, secrets, crypto |
| reviewing-architecture | `skills/reviewing-architecture/` | SOLID, modularity, coupling, scalability |
| reviewing-logic | `skills/reviewing-logic/` | Bugs, edge cases, null handling, async |
| reviewing-performance | `skills/reviewing-performance/` | Complexity, N+1, memory, caching |

Agent prompts tell each agent: `Read skills/reviewing-{dimension}/SKILL.md — follow its checklist and output format exactly.`

Sub-skills can also be invoked independently.

## Commands

- `/add-commit-push` — stage, commit, and push (no Co-Authored-By)
- `/commit-push` — commit staged changes and push (no Co-Authored-By)
- `/setup-claude-md` — merge behavioral guidelines into a project's CLAUDE.md (idempotent)

## Hooks

- **Notification**: desktop notification when Claude waits for input
- **Stop**: desktop notification when Claude finishes a response

Both use `scripts/notify.py`.

## Behavioral Guidelines

> Reduce common LLM coding mistakes. These bias toward caution over speed — for trivial tasks, use judgment.

### 1. Think Before Coding

- State assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop and ask.

### 2. Simplicity First

- No features beyond what was asked.
- No abstractions for single-use code.
- No speculative "flexibility" or "configurability".
- No error handling for impossible scenarios.
- If 200 lines could be 50, rewrite it.

### 3. Surgical Changes

- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style.
- Remove imports/variables YOUR changes made unused. Don't remove pre-existing dead code unless asked.
- Every changed line should trace to the user's request.

### 4. Goal-Driven Execution

Transform tasks into verifiable goals:
- "Add validation" → write tests for invalid inputs, then make them pass
- "Fix the bug" → write a reproducing test, then make it pass
- "Refactor X" → ensure tests pass before and after

For multi-step tasks, state a brief plan with verification steps.
