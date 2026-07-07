---
name: writing-pr
description: Use this skill when the user wants to write, draft, or improve a Pull Request (PR) or Merge Request (MR) description. Triggers include: "write a PR", "draft MR description", "help me write a pull request", "PR description for my changes", or any request to document code changes for review. Generates structured, reviewer-friendly PR/MR descriptions following team standards.

---

Help the user write a clear, well-structured Pull Request or Merge Request description that tells a story reviewers can immediately understand.

The user provides context about their changes: what they did, why, which files changed, and how they tested it. They may paste a diff, list changed files, or describe the feature/fix in natural language.

## Output Format

Always produce the PR description using this exact template (use Markdown headings, not bold labels):

---

# Title: Short, descriptive summary (no ticket placeholder unless user provides one)

# Context

## What problem does this solve?

1-2 sentences describing the problem and motivation. Do not assume the reviewer knows the background.

## Note for reviewer *(if there is a design doc, external spec, or non-obvious decision the reviewer must read first)*

Point to the relevant file or link. Example: "The full design — including every option considered and accepted trade-offs — is in `internal/extractor/DESIGN.md`."

# Scope

| Area           | Change               |
| -------------- | -------------------- |
| `path/to/file` | What changed and why |

# Solution & Proof

## How does your technical solution work?

### The flow

Describe the end-to-end pipeline as a numbered or bulleted prose sequence. If the flow is non-trivial, add a mermaid sequence diagram:

```mermaid
sequenceDiagram
    ...
```

### All approaches

For each alternative considered (including the chosen one), list it as a sub-bullet with **What**, **Pros**, **Cons**, and a one-line verdict.

- **Approach Name**:
  - **What**: ...
  - **Pros**: ...
  - **Cons**: ...
    => Verdict

- **Chosen Approach** *(chosen)*:
  - **What**: ...
  - **Pros**: ...
  - **Cons**: ...
    => Verdict

### Why this one?

One short paragraph. Lead with the decisive reason — the thing that ruled out all other approaches.

## How do you know it works?

List each scenario tested with a concrete result. Be honest — include partial failures or known edge-case issues:

- **Happy path**: Worked! (As in demo)
- **[Edge case]**: Worked! / Partially broken — [brief description] — acceptable / needs follow-up
- **[Another edge case]**: ...

# UI Changes

## Visual Proof

Attach screenshots, GIFs, or a demo video. For video uploads use the GitLab/GitHub upload syntax. If no UI changes, omit this section entirely.

# Testing

## Automated Tests

List each test file and what scenario it covers. If there is a playground link, include it.

- `path/to/test_file.go`: [what it tests]

## Manual Verification Steps (for localhost)

Numbered steps a reviewer can follow to verify the change locally. Keep it short and concrete:

1. [Start the service / run the command]
2. [Set up any dependency or fake]
3. [Take the action]
4. [Check the result]

---

## Writing Guidelines

**Use Markdown headings (`#`, `##`, `###`), not bold labels.** The template above uses headings — follow it exactly.

**Tell a story.** The description should answer: *What was missing or broken? What did you change? Does it work?* A reviewer should never need to open a ticket to understand the PR.

**Be specific, not vague.**

- ❌ "Fixed the bug" → ✅ "Fixed off-by-one error in pagination that caused the last item to be skipped"
- ❌ "Updated some files" → ✅ "Updated `watcher.go` to tail only complete newline-terminated lines"

**Show all alternatives.** The "All approaches" section must list every option you considered, not just the winner. Reviewers need to see that you evaluated trade-offs.

**Be honest in "How do you know it works?".** Include partial failures or known edge cases. Hiding rough edges erodes trust; documenting them builds it.

**Keep scope tight.** If the changes bundle unrelated fixes or refactors, flag it:

> ⚠️ Consider splitting this into two PRs. Smaller PRs get reviewed and merged faster.

**UI changes always need visual proof.** If the user mentions any frontend/UI changes but provides no screenshots or GIF, remind them:

> This looks like a UI change — please add screenshots or a GIF before requesting review.

**Add a mermaid diagram when the flow is non-trivial.** If the pipeline or sequence involves more than three participants or has branching logic, a `sequenceDiagram` is worth more than a paragraph.

## Inferring Missing Information

If the user gives incomplete context, fill in reasonable placeholders using `[brackets]` and leave a note at the end:

> I've used placeholders for [ticket] and the manual verification steps — fill these in before submitting.

Do not invent technical details. If you are unsure how something works, ask before writing the Solution section.