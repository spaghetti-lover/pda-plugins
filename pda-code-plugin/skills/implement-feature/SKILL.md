---
name: implement-feature
description: Implement a feature, fix, or code change while keeping comments minimal and matching the surrounding code's existing comment style. Use whenever the user asks to implement, build, add, write, or fix code — especially after a feature has been discussed or planned — to avoid over-commenting and to stay consistent with the codebase's conventions.
metadata:
  mcpmarket-version: 1.0.0
---

# Implement Feature

Use this skill when implementing any feature, fix, or code change. The goal is to write the code that was discussed **and** keep comments restrained and consistent with the existing codebase — the default failure mode is adding too many, too verbose comments that clutter the diff.

## Comment rules (the main point of this skill)

1. **Match the surrounding code first.** Before writing comments, look at the existing comments in the same file and nearby files. Copy their density, length, tone, and format (e.g. `//` vs `/** */`, full sentences vs short fragments, language used). The new code must read like it was written by whoever wrote the old code. If the existing file has almost no comments, write almost no comments.

2. **Default to no comment.** Code should explain itself through good names. Only add a comment when the code cannot be made self-explanatory by renaming or restructuring. If you're about to write a comment, first ask: *can a better name remove the need for this comment?*

3. **Comment the "why", never the "what".** A comment may explain a non-obvious reason, a tricky edge case, a workaround, or a constraint. Never restate what the code already says.
   - ❌ `// increment the counter` above `counter++`
   - ✅ `// API returns 1-based page index, so subtract 1`

4. **Keep each comment short.** One line where possible. No multi-paragraph essays, no banner/decoration comments, no section-divider blocks unless the file already uses them.

5. **Don't add comments for these:** obvious code, every function/parameter, restating type signatures, narrating each step, change-log notes ("added X", "modified Y"), or instructions to the reviewer. Version control already records what changed — don't leave comments about the edit itself.

6. **Respect existing doc conventions.** If the codebase documents public APIs with a specific format (JSDoc, docstrings, GoDoc, etc.), follow that exact format for new public APIs — but still keep them concise. Don't introduce a new documentation style the project doesn't already use.

7. **Don't comment out code.** Delete it instead.

## Implementation workflow

1. Implement the feature exactly as discussed/planned. Don't expand scope.
2. Reuse existing helpers and utilities rather than reinventing them; follow the project's existing patterns, naming, and formatting.
3. Add comments only where rules above justify one, matching the surrounding style.
4. Before finishing, re-read your diff and **delete any comment that doesn't earn its place** — anything that just restates the code, narrates steps, or describes the edit.

## Quick self-check before done

- Would these comments look out of place next to the existing code? → align them.
- Is any comment explaining *what* instead of *why*? → delete it.
- Could a rename remove a comment? → rename, remove the comment.
- More comments than the existing file would have? → cut them down.
