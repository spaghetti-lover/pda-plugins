---
allowed-tools: Read, Bash(cat:*), Bash(ls:*), Write, Edit
description: Combine the project's existing CLAUDE.md with the pda-plugin behavioral guidelines to reduce common LLM coding mistakes
---

## Context

- Current directory: !`pwd`
- Check for existing CLAUDE.md: !`ls -la CLAUDE.md 2>/dev/null || echo "NO_CLAUDE_MD"`

## Your task

Merge the behavioral guidelines below into the project's CLAUDE.md file.

### Rules

1. **Read first**: If a `CLAUDE.md` already exists in the current working directory, read its full contents before making any changes.
2. **Preserve everything**: Keep ALL existing content from the original CLAUDE.md intact — project-specific instructions, conventions, tool configs, etc. Do not remove, reword, or reorder any existing content.
3. **Append the guidelines**: Add the behavioral guidelines section below **at the end** of the existing file, separated by a clear divider.
4. **No CLAUDE.md yet**: If there is no `CLAUDE.md`, create one with only the guidelines below.
5. **Idempotent**: If the guidelines section (identified by the `## Behavioral Guidelines` heading) already exists in the file, **replace it** with the latest version below instead of duplicating it.
6. **No other files**: Only touch `CLAUDE.md` in the current working directory. Do not modify any other files.

### Guidelines to merge

Append the following block:

```
---

## Behavioral Guidelines

> Reduce common LLM coding mistakes. These bias toward caution over speed — for trivial tasks, use judgment.

### 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

### 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

### 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

### 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:

1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.
```

### After merging

Show the user:
- Whether CLAUDE.md was created or updated
- A brief summary of what was preserved vs added
