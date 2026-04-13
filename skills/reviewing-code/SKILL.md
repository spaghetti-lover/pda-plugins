---
name: reviewing-code
description: Performs comprehensive code review across 4 dimensions (security, architecture, logic, performance) using parallel agents. Aggregates findings into a unified report with prioritized verdicts. Use when asked to review code, review a PR, or perform a code review.
---

# Code Review

Orchestrates 4 parallel review agents, then aggregates findings into a unified report.

## 1. Determine scope

- **Specific files/dirs**: use those directly
- **PR/branch**: `git diff main...HEAD`
- **Last commit**: `git diff HEAD~1`
- **No scope given**: `git diff --cached` + `git diff`

Collect file list and diffs to pass to each agent.

## 2. Spawn 4 agents in parallel

Launch all simultaneously via the Agent tool. Each agent reads its methodology file first, then reviews the files.

**Agent 1 — Security**: Read `skills/reviewing-security/SKILL.md`, follow its checklist. End with PASS/WARN/FAIL.

**Agent 2 — Architecture**: Read `skills/reviewing-architecture/SKILL.md`, follow its checklist. End with top 1-3 improvements.

**Agent 3 — Logic**: Read `skills/reviewing-logic/SKILL.md`, follow its checklist. End with CLEAN/NEEDS_FIXES/RISKY.

**Agent 4 — Performance**: Read `skills/reviewing-performance/SKILL.md`, follow its checklist. End with FAST/ACCEPTABLE/NEEDS_OPTIMIZATION.

Each agent prompt:

```
You are a {dimension} review agent.
FIRST: Read skills/reviewing-{dimension}/SKILL.md — follow its checklist and output format exactly.
Files to review: {file_list}
Diff context: {diff_summary}
```

## 3. Aggregate results

**Deduplicate**: merge findings that flag the same code location, use the more severe classification.

**Priority order**:

1. Security CRITICAL/HIGH
2. Logic BUG/LIKELY_BUG
3. Performance HIGH
4. Architecture HIGH
5. Everything else by severity

## 4. Report format

Output directly to user:

```
# Code Review Report

**Scope**: {files} | **Date**: {date}

| Dimension    | Status                    | Findings |
|-------------|---------------------------|----------|
| Security     | PASS/WARN/FAIL           | X        |
| Architecture | assessment               | X        |
| Logic        | CLEAN/NEEDS_FIXES/RISKY  | X        |
| Performance  | FAST/ACCEPTABLE/NEEDS_OPT| X        |

**Overall**: APPROVE / REQUEST_CHANGES / NEEDS_DISCUSSION

## Critical (fix before merge)
## Important (should fix)
## Suggestions (nice to have)
## Detailed Findings by Dimension
```

## 5. Verdict

- **APPROVE**: no critical/high findings
- **REQUEST_CHANGES**: has critical/high security or confirmed bugs
- **NEEDS_DISCUSSION**: has high architecture/performance concerns (may be intentional)

## Options

User can customize: `"review only security and performance"`, `"review with opus"`, `"review PR #123"`.
