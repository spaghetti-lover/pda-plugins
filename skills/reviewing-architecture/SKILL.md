---
name: reviewing-architecture
description: Evaluates code design quality including SOLID principles, modularity, coupling/cohesion, dependency management, and scalability. Use when performing an architecture review, design review, or as part of code-review.
---

# Architecture Review

Evaluate architectural quality of code changes.

## Scope

- Specific files/paths if given
- `git diff` for PR/branch
- Staged/unstaged changes if no scope specified

## Checklist

1. **SOLID violations** — single responsibility, open/closed, liskov, interface segregation, dependency inversion
2. **DRY violations** — duplicated logic (flag at 3+ repetitions or when it creates maintenance risk)
3. **God objects/functions** — classes or functions doing too many things
4. **Separation of concerns** — business logic mixed with IO, presentation mixed with data access, scattered cross-cutting concerns
5. **Circular dependencies** between modules
6. **Coupling** — high-level modules depending on low-level details, tight coupling to external libs without abstraction
7. **Code organization** — files in wrong directories, inconsistent naming, public API surface too large
8. **Scalability** — hardcoded values, missing extensibility points, API design issues

## Classification

Per finding: **Impact** (HIGH/MEDIUM/LOW), **Type** (SOLID_VIOLATION/COUPLING/COHESION/MODULARITY/SCALABILITY/ORGANIZATION), **Effort to fix** (HIGH/MEDIUM/LOW).

## Output format

```
### [IMPACT] Title
- **File**: `path:line`
- **Type**: CATEGORY
- **Effort**: HIGH/MEDIUM/LOW
- **Description**: issue and principle violated
- **Current pattern**: snippet
- **Recommendation**: how to restructure
```

## Summary

End with impact counts table and top 1-3 highest-impact actionable improvements.
