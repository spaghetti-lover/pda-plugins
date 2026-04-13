---
name: reviewing-performance
description: Analyzes code for performance issues including algorithmic complexity, N+1 queries, memory leaks, missing caching, blocking operations, and inefficient data structures. Use when performing a performance review, perf review, or as part of code-review.
---

# Performance Review

Analyze code for performance issues and optimization opportunities.

## Scope

- Specific files/paths if given
- `git diff` for PR/branch
- Staged/unstaged changes if no scope specified

## Checklist

1. **Algorithmic complexity** — O(n²) or worse, unnecessary sorting, redundant computation in loops, missing early exits
2. **Database & I/O** — N+1 queries, missing indexes, over-fetching (SELECT *), unbounded queries (missing LIMIT), sequential I/O that could be parallel
3. **Memory** — leaks (event listeners, growing caches), unnecessary allocations in hot loops, large object copies, unbounded collections
4. **Caching** — missing memoization for repeated pure calls, cache invalidation issues, missing HTTP cache headers
5. **Concurrency** — blocking main thread, sequential awaits that could be parallel, thread pool exhaustion, missing connection pooling
6. **Frontend** (if applicable) — unnecessary re-renders, large bundle size, layout thrashing, missing virtualization

## Classification

Per finding: **Impact** (HIGH/MEDIUM/LOW), **Confidence** (HIGH/MEDIUM/LOW), **Category** (COMPLEXITY/DATABASE/MEMORY/CACHING/CONCURRENCY/FRONTEND).

## Output format

```
### [IMPACT] Title
- **File**: `path:line`
- **Category**: CATEGORY
- **Confidence**: HIGH/MEDIUM/LOW
- **Description**: issue and performance impact
- **Current code**: snippet
- **Estimated impact**: latency/throughput/memory effect
- **Recommendation**: optimized approach
```

## Summary

End with impact counts table and assessment: **FAST** (no issues), **NEEDS_OPTIMIZATION** (has high-impact items), **ACCEPTABLE** (only low/medium items).
