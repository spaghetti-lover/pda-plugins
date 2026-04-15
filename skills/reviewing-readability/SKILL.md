---
name: reviewing-readability
description: Analyzes code for readability and simplicity issues including unclear naming, complex control flow, dead code, missing or misleading comments, over-engineering, and unnecessary length. Use when performing a readability review or as part of code-review.
---

# Readability Review

Analyze code for readability and simplicity issues that make it harder for
another engineer — or agent — to understand without the author present.

## Scope

- Specific files/paths if given
- `git diff` for PR/branch
- Staged/unstaged changes if no scope specified

## Checklist

1. **Naming** — vague identifiers (`temp`, `data`, `result`, `x`) without
   context; names that lie about what a variable/function actually does;
   inconsistency with surrounding project conventions; overly abbreviated or
   overly verbose names

2. **Control flow** — nested ternaries, deep callback pyramids, flag variables
   that could be replaced with early returns, boolean logic that requires
   mental simulation to evaluate, switch/if-else chains that could be a lookup
   table

3. **Length & concision** — functions or files that do more than one thing;
   code that achieves in 100 lines what 20 would suffice for; duplicated logic
   that should be extracted; long parameter lists that should be an options
   object

4. **Abstraction level** — premature generalization (abstractions introduced
   before the third use case); leaky abstractions that force callers to know
   implementation details; mixed abstraction levels inside a single function

5. **Comments & documentation** — comments that merely restate the code
   (`i++ // increment i`); missing comments on non-obvious intent, algorithmic
   choices, or "why not X" decisions; stale comments that no longer match the
   code; commented-out code left in place

6. **Dead code** — unused variables, parameters, imports, or exports; functions
   that are defined but never called; backwards-compatibility shims with no
   remaining callers; `// removed` / `// TODO: delete` annotations that were
   never followed through; feature flags that are permanently enabled/disabled

7. **Complexity & cleverness** — "clever" tricks that sacrifice clarity for
   brevity; unnecessary use of advanced language features where a simple loop
   would read better; magic numbers or magic strings without named constants;
   deeply nested data transformations that should be broken into named steps

8. **Consistency** — deviations from the file's or project's established
   patterns without justification; mixed naming conventions (camelCase vs
   snake_case) within the same scope; inconsistent error handling styles

## Classification

Per finding: **Severity** (REQUIRED / NIT / SUGGESTION), **Confidence**
(HIGH / MEDIUM / LOW), **Category**
(NAMING / CONTROL_FLOW / LENGTH / ABSTRACTION / COMMENTS / DEAD_CODE /
COMPLEXITY / CONSISTENCY).

- **REQUIRED** — a reader unfamiliar with this code would be confused or
  misled; must fix before merge
- **NIT** — minor style or preference issue; author may ignore
- **SUGGESTION** — a meaningful improvement that is not strictly required

## Output Format

```
### [SEVERITY] Title
- **File**: `path:line`
- **Category**: CATEGORY
- **Confidence**: HIGH / MEDIUM / LOW
- **Description**: what the readability problem is and why it matters
- **Current code**: snippet
- **Recommendation**: clearer alternative or approach
```

## Dead Code Section

After the per-finding list, always include a dedicated section:

```
## Dead Code Identified
- `symbolName` in `path:line` — reason it appears unused
- (or "None identified.")
→ Confirm with author before removing anything non-obvious.
```

## Summary

End with an impact counts table and one of these assessments:

- **CLEAR** — no required changes; code is easy to follow as written
- **NEEDS_CLEANUP** — has required findings; must be addressed before merge
- **HARD_TO_FOLLOW** — pervasive issues; significant rework needed for the
  code to be maintainable

```
| Severity   | Count |
|------------|-------|
| REQUIRED   | X     |
| SUGGESTION | X     |
| NIT        | X     |

**Assessment**: CLEAR / NEEDS_CLEANUP / HARD_TO_FOLLOW
```
