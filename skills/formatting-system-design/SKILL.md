---
name: formatting-system-design
description: Use when asked to format, restructure, or organize a system design document. Triggers on "format system design", "restructure design doc", or when a system design file needs standardized structure.
---

# Formatting System Design

Restructures system design documents into a standardized section order. Flags missing information instead of inventing it.

## Required Section Order

1. **Problem** — what problem does the system solve?
2. **Proposed Solution** — high-level approach, inputs/outputs, why it matters
3. **Back-of-the-Envelope Estimation** — scale estimates, QPS, storage, bandwidth
4. **APIs Design / User Flow** — API endpoints or user-facing flows
5. **Data Model Design** — ER diagrams, schema definitions, key relationships
6. **High-Level Design** — architecture overview, major components and their interactions
7. **Detailed Design** — deep dive into critical components, data flow, algorithms
8. **Bottlenecks & Trade-offs** — identify bottlenecks, discuss mitigations, trade-offs made

Optional trailing sections: **Open Questions**, **References**.

## Process

1. Read the entire source document
2. Map existing content to the 8 required sections
3. Identify what's **missing** — report to user, do NOT fabricate
4. Reorganize existing content into the standard order
5. Preserve all original content — diagrams, tables, code blocks, mermaid charts
6. Keep the original writing style and voice

## Missing Information Rules

**NEVER assume or generate missing content.** Instead, add a callout:

```markdown
> **[MISSING]** This section needs: {what's missing and why it matters}
```

Examples of what to flag:
- No scale estimates → flag in Back-of-the-Envelope
- No API endpoints defined → flag in APIs Design
- No bottleneck discussion → flag in Bottlenecks
- Data model exists but missing indexes/constraints discussion → flag it

## Section Guidelines

### Back-of-the-Envelope Estimation
- Table format: metric | value
- Include: DAU, QPS (read/write), storage, bandwidth
- Derive conclusions (e.g., "single Postgres is sufficient")

### APIs Design / User Flow
- REST/gRPC endpoints with method, path, request/response
- Or user flow diagrams for UI-heavy systems
- Include error cases

### Data Model Design
- ER diagram (mermaid preferred)
- Table definitions with types
- Explain non-obvious relationships and design choices

### High-Level Design
- Architecture diagram (mermaid preferred)
- Name each component and its responsibility
- Show data flow between components

### Detailed Design
- Deep dive into 2-3 most critical components
- Sequence diagrams for complex flows
- Algorithms, state machines, retry logic

### Bottlenecks & Trade-offs
- Identify single points of failure
- Discuss scaling limits of current design
- Trade-offs made and alternatives considered
- Potential mitigations

## What NOT to Do

- Don't add content the source document doesn't contain
- Don't remove content that doesn't fit neatly into a section — put it in the closest match
- Don't change technical decisions or opinions
- Don't "improve" diagrams beyond reformatting
- Don't split a document into multiple files unless asked
