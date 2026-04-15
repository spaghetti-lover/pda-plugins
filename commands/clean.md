---
allowed-tools: Bash(find:*), Bash(git ls-files:*), Bash(git status:*), Bash(rm:*)
description: Remove redundant planning artifacts and untracked test/spec files that were generated during development but never committed
---

## Context

- Untracked files (git doesn't know about these): !`git ls-files --others --exclude-standard`
- Git status summary: !`git status --short`

## Your task

1. From the untracked files list above, identify all **redundant artifacts** (see criteria below).
2. Show the full list grouped by category with a reason for each file.
3. Ask the user to confirm — either all at once or per category.
4. Delete only the confirmed files and report what was removed.

## What counts as a redundant artifact

**Planning files** (any location in the repo):

- `SPEC.md`, `PLAN.md`, `plan.md`, `todo.md`, `TODO.md`

**Untracked test files** — any untracked file matching these patterns:

- `test_*.py`, `*_test.py`, `*.test.ts`, `*.test.tsx`, `*.spec.ts`, `*.spec.tsx`, `*.test.js`, `*.spec.js`, even test folder
- These are scratch tests written during development; if they were permanent they would have been committed

**Untracked temporary files**:

- `*.tmp`, `*.bak`, `scratch.*`, `debug.*`

## Rules

- **Only delete untracked files** (shown by `git ls-files --others --exclude-standard`). Never touch committed or staged files.
- Never delete files outside the current working directory.
- Always show the grouped list and get confirmation before any `rm`.
- If no artifacts are found, report "Nothing to clean." and stop.
- After cleanup, run `git status --short` and show the result so the user can verify.
