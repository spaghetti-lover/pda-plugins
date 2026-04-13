---
name: reviewing-logic
description: Analyzes code for bugs and correctness issues including off-by-one errors, null handling, boolean logic mistakes, race conditions, and edge cases. Use when performing a logic review, correctness review, bug review, or as part of code-review.
---

# Logic & Correctness Review

Analyze code for bugs, logic errors, and correctness issues.

## Scope

- Specific files/paths if given
- `git diff` for PR/branch
- Staged/unstaged changes if no scope specified

## Checklist

1. **Control flow** — off-by-one, unreachable code, missing break/return, incorrect precedence, infinite loops
2. **Null/undefined/empty** — null dereferences, missing checks, empty collection handling
3. **Boolean logic** — incorrect AND/OR, negation errors, tautologies/contradictions
4. **Data handling** — type coercion bugs, integer overflow, float comparison, encoding issues, reference vs value semantics
5. **Edge cases** — empty input, boundary values (0, -1, MAX_INT), unicode, error propagation, resource cleanup in error paths
6. **Async/concurrency** — missing await, unhandled rejections, race conditions, deadlocks, shared mutable state

## Classification

Per finding: **Severity** (BUG/LIKELY_BUG/POSSIBLE_BUG/SMELL), **Confidence** (HIGH/MEDIUM/LOW).

## Output format

```
### [SEVERITY] Title
- **File**: `path:line`
- **Confidence**: HIGH/MEDIUM/LOW
- **Description**: what the bug is, when it triggers, incorrect behavior
- **Buggy code**: snippet
- **Expected vs actual behavior**
- **Fix**: corrected snippet
```

## Summary

End with severity counts table and verdict: **CLEAN** (no bugs), **NEEDS_FIXES** (has bugs), **RISKY** (has possible bugs needing investigation).
