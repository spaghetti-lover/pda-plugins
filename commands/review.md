---
description: Conduct a five-axis code review — logic, readability, architecture, security, performance
---

# Code Review

Orchestrate a comprehensive code review across five dimensions by running the specialized review skills in parallel and aggregating their findings into a single prioritized report.

## Scope

Determine review scope from the user's invocation:

- **Specific files/paths** if given as arguments
- **PR/branch** — use `git diff <base>...HEAD` when reviewing a branch or PR
- **Staged/unstaged changes** if no scope specified — `git diff` and `git diff --staged`

Pass the same scope to every skill so findings line up.

## Orchestration

Spawn the five review skills **in parallel** as independent agents (one Agent tool call per skill, all in a single message). Each agent runs its skill against the same scope and returns a structured report.

| Axis          | Skill                                          | Focus                                                 |
|---------------|------------------------------------------------|-------------------------------------------------------|
| Logic         | `skills/reviewing-logic/SKILL.md`              | Bugs, correctness, edge cases, concurrency            |
| Readability   | `skills/reviewing-readability/SKILL.md`        | Naming, control flow, dead code, comments, clarity    |
| Architecture  | `skills/reviewing-architecture/SKILL.md`       | SOLID, coupling, cohesion, modularity, scalability    |
| Security      | `skills/reviewing-security/SKILL.md`           | OWASP Top 10, injection, auth, secrets, crypto        |
| Performance   | `skills/reviewing-performance/SKILL.md`        | Complexity, N+1, memory, caching, blocking I/O        |

Each agent prompt should include:
1. The exact scope (files, diff range, or staged/unstaged)
2. Instruction to follow the named skill's checklist and output format verbatim
3. Instruction to return only the skill's structured report — no preamble

Do **not** run the skills sequentially or inline — parallel subagents protect the main context window from raw findings and keep wall-clock time bounded by the slowest axis.

## Aggregation

Once all five agents return, merge their reports into a single review with this structure:

```
# Code Review

**Scope**: <files / diff range>

## Verdict
<one-line overall call: BLOCK / NEEDS_FIXES / APPROVE_WITH_NITS / APPROVE>

## Top Priorities
1. <highest-severity finding across all axes — file:line — one-line summary>
2. ...
3. ...
(up to 5)

## Findings by Axis

### Logic & Correctness — <verdict from skill>
<copied findings, unchanged>

### Security — <verdict from skill>
<copied findings>

### Architecture — <verdict from skill>
<copied findings>

### Performance — <verdict from skill>
<copied findings>

### Readability — <verdict from skill>
<copied findings>

## Severity Roll-up

| Axis         | Critical/High/Required | Medium/Suggestion | Low/Nit |
|--------------|------------------------|-------------------|---------|
| Logic        | X                      | X                 | X       |
| Security     | X                      | X                 | X       |
| Architecture | X                      | X                 | X       |
| Performance  | X                      | X                 | X       |
| Readability  | X                      | X                 | X       |
```

## Verdict Rules

Compute the overall verdict from the per-axis assessments using the most severe outcome:

- **BLOCK** — any of: security FAIL, logic NEEDS_FIXES with HIGH-confidence BUG, architecture HIGH-impact violation that breaks the system
- **NEEDS_FIXES** — any required readability finding, performance NEEDS_OPTIMIZATION, logic LIKELY_BUG, security WARN
- **APPROVE_WITH_NITS** — only SUGGESTION/NIT/LOW findings remain
- **APPROVE** — every axis came back CLEAN/PASS/FAST/CLEAR

## Top Priorities

Pick the 1–5 highest-leverage findings across all axes (security CRITICAL/HIGH first, then logic BUGs, then HIGH-impact architecture/performance, then REQUIRED readability). These go at the top so the author can act without reading the full report.

## Notes

- Preserve each skill's output format verbatim inside its section — do not rewrite findings
- If two axes flag the same line, keep both entries but cross-reference in the Top Priorities list
- If a skill returns no findings, still include its section with the CLEAN verdict line
