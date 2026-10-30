---
name: code-review
description: Run a production-grade multi-agent code review on local changes (uncommitted diff, staged diff, or diff vs. a base branch). Fans out four specialist reviewers in parallel — performance, security, logic, architecture — then aggregates findings into a single structured report. Use when the user asks to "review my changes", "review this branch", "run a local review", "multi-agent review", or mentions reviewing before pushing/committing.
allowed-tools:
  - Read
  - Grep
  - Glob
  - Agent
  - TaskCreate
  - TaskGet
  - TaskList
  - TaskUpdate
  - Bash(git status:*)
  - Bash(git diff:*)
  - Bash(git log:*)
  - Bash(git show:*)
  - Bash(git ls-files:*)
  - Bash(git merge-base:*)
  - Bash(git rev-parse:*)
  - Bash(git branch:*)
user-invocable: true
---

# Local Code Review (Multi-Agent)

Reviews the current local changes using four specialist subagents running in parallel. Produces a single structured report. **No remote systems are touched** — this is purely local; no GitLab/GitHub comments are posted.

## When to Use

- User says: "review my changes", "review this branch", "review before I push", "run a local review", "multi-agent review".
- User has uncommitted work, staged changes, or a feature branch diverged from `master`/`main` and wants feedback before pushing or opening a PR.
- **Do NOT** use this skill to review a remote PR — that's the `/review` skill. This one is for local diffs only.

## Core Workflow

### Step 1 — Determine the diff scope

Ask the user what they want reviewed if it's not obvious from context. Otherwise pick the most likely scope and state your choice in one sentence before proceeding.

Resolution order:

1. **User-specified range** (e.g. "review my changes against main", "review the last 3 commits", "review staged changes"). Use exactly what they asked for.
2. **Uncommitted + staged work on a dirty tree**: if `git status --porcelain` is non-empty, default to `git diff HEAD` (covers both staged and unstaged).
3. **Feature branch on a clean tree**: if the current branch is not the main branch and the tree is clean, default to `git diff $(git merge-base HEAD <base>)..HEAD` where `<base>` is `master` (or `main` if `master` doesn't exist).
4. **Clean tree on the main branch**: ask the user what range they want. Do not invent one.

Resolve the base branch with:

```
git rev-parse --verify master 2>/dev/null || git rev-parse --verify main
```

### Step 2 — Collect changed files and the diff

Run these in parallel:

- `git diff --name-only <range>` — list of changed file paths
- `git diff <range>` — the unified diff
- `git log --oneline <range>` — commit titles for context (skip for uncommitted-only reviews)

If the diff is empty, stop and tell the user there's nothing to review.

If the diff is very large (> ~2000 lines), warn the user and ask whether to:
- proceed anyway (reviewers may sample),
- narrow to specific paths (`git diff <range> -- <paths>`), or
- narrow to a specific commit range.

### Step 3 — Fan out to the four reviewer subagents IN PARALLEL

**This step is non-negotiable: all four Agent calls MUST be issued in a single assistant message so they run concurrently.** Do not await one before launching the next.

Launch (in one message, four parallel `Agent` tool calls):

1. `subagent_type: pda-code:performance-reviewer` — performance analysis
2. `subagent_type: pda-code:security-reviewer` — security vulnerabilities
3. `subagent_type: pda-code:logic-reviewer` — bugs and logic errors
4. `subagent_type: pda-code:architecture-reviewer` — design and code structure

For each agent, the prompt MUST contain:

- The resolved diff range (e.g. `origin/master..HEAD`, or `HEAD (uncommitted)`).
- The full list of changed file paths.
- The complete unified diff. If the diff is too large for a single prompt, chunk it by file and tell the agent that chunking occurred.
- An explicit instruction: *"Return findings in the structured output format specified in your agent definition. Do not call any GitLab MCP tools. Do not post comments anywhere."*
- A reminder that they can use `Read` / `Grep` / `Glob` on the working tree to gather additional context, but **not** write, edit, or run non-git shell commands.

### Step 4 — Aggregate findings

Once all four subagents return, consolidate:

1. **Parse each agent's structured output** into a list of findings.
2. **Deduplicate**: if two agents flag the same `file:line` with overlapping concerns, merge them and keep the most severe severity.
3. **Sort**: by severity (`high` → `medium` → `low`), then by file path, then by line.
4. **Count**: totals per category and per severity — used in the executive summary.

### Step 5 — Produce the final report

Output a single markdown report directly to the user (do NOT write it to a file unless the user asks). Use this exact structure:

```
# Code Review Report

**Scope:** <range>
**Files changed:** <N>
**Findings:** <total>  (high: <h>, medium: <m>, low: <l>)

## Executive Summary

<2–4 sentences. Name the riskiest issue first. If there are no findings,
say so plainly and congratulate nobody — just move on.>

## Findings by Severity

### 🔴 High

- **[Category]** `<file>:<line>` — <issue>
  _Fix:_ <concrete suggestion>

### 🟡 Medium
...

### 🟢 Low
...

## Findings by Category

### Performance
- `<file>:<line>` [severity] — <issue>. _Fix:_ <suggestion>

### Security
...

### Logic
...

### Architecture
...

## Action Items

1. <Most important thing to fix before pushing>
2. <Next>
3. <Next>
```

Skip empty sections entirely. If no issues at all, output only the header, a one-line "No issues found across performance / security / logic / architecture." summary, and nothing else.

## Review Guidelines

- **Only comment on issues.** Do NOT add comments praising good practices or noting that code "looks fine" — that's noise. Silence is the correct signal for a clean file.
- **Every finding needs a concrete fix.** "This is slow" without a replacement is not useful. Name the construct, pattern, or API.
- **Cite `file:line` exactly** so the user can click straight to it.
- **Be concise and natural.** A little humor or Gen-Z phrasing is fine in the executive summary and action items; findings themselves stay precise and technical.
- **Each logical change gets its own finding.** Don't merge unrelated issues into one bullet.
- **Stay in-lane per agent.** Performance findings don't belong under Security, etc. If an agent returned an off-topic finding, re-categorize during aggregation.
- **Do not post to any remote system.** This skill is strictly local. The report is displayed to the user in the chat.

## Important Notes

- **No GitLab / GitHub writes.** This skill never creates PR comments, issues, or reviews on any remote. If the user wants that, point them at `/review` instead.
- **No code modifications.** The skill and its subagents are read-only. They may suggest fixes, but do not apply them. If the user then says "apply those fixes", handle that as a separate follow-up task outside this skill.
- **Parallelism is mandatory.** If you call the four agents sequentially, you are using the skill wrong. All four calls go in one assistant message.
- **Context from the working tree is encouraged.** Subagents can `Read`/`Grep` the repo to verify assumptions (e.g. "is this function really only called from one place?") before reporting a finding — but must not over-invest in exploration beyond the diff.

