---
name: implement-feature
description: Implement a feature, fix, or code change while matching the codebase's existing code style and conventions (naming, formatting, patterns, helper reuse) and keeping comments minimal and consistent with the surrounding code. Use whenever the user asks to implement, build, add, write, or fix code — especially after a feature has been discussed or planned — to write code that reads like the existing codebase and to avoid over-commenting.
disable-model-invocation: true
---

# Implement Feature

Use this skill when implementing any feature, fix, or code change. The goal is to write the code that was discussed so that it reads like the existing codebase — matching its **code conventions** (naming, formatting, patterns, helper reuse) **and** its **comment conventions** (restrained, consistent). New code should be indistinguishable in style from the code around it. The default failure modes are introducing a foreign style and adding too many, too verbose comments that clutter the diff.

## Code convention rules

1. **Match the surrounding code first.** Before writing, read the file you're editing and nearby files. Copy their naming, formatting, structure, and idioms. The new code must look like whoever wrote the old code wrote it.

2. **Follow the codebase's naming idioms.** Match its casing and verb/noun patterns. Name modules, variables, and functions so their intention is clear without reading the implementation — but consistent with how existing names are formed, not a new convention.

3. **Reuse over reinvent.** Search for an existing helper, utility, or config value that already does the job and use it. Don't recompute, re-implement, or hardcode a value when a helper already provides it.

4. **Match formatting and idioms.** Follow the existing indentation, import ordering, error-handling patterns, and language idioms. Don't introduce a new style, library, or abstraction the project doesn't already use.

5. **Follow the existing structure.** Place new code where similar code already lives and respect the project's file/module organization. Don't create a new layout when an established one fits.

6. **Use the right cleanup idiom.** When a resource's cleanup does more than just close (it flushes, finalizes, emits, etc.), use `x := createX(); x.DoSth(); x.Close()` rather than a `closeX := createX(); closeX()` alias.

7. **Don't expand scope.** Implement exactly what was discussed — no extra features, refactors, or "while I'm here" changes.

## Comment rules

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

1. Read the file you're editing and nearby files to learn the existing conventions before writing anything.
2. Implement the feature exactly as discussed/planned. Don't expand scope.
3. Follow the project's existing patterns, naming, formatting, and structure; reuse existing helpers and utilities rather than reinventing them.
4. Add comments only where the comment rules above justify one, matching the surrounding style.
5. Before finishing, re-read your diff. Confirm the code matches the surrounding conventions, and **delete any comment that doesn't earn its place** — anything that just restates the code, narrates steps, or describes the edit.

## Quick self-check before done

- Would this code look out of place next to the existing code? → align it.
- Did I reinvent something a helper already does, or hardcode a value a helper provides? → reuse the helper.
- Did I introduce a naming, formatting, or structural style the project doesn't already use? → match the existing one.
- Would these comments look out of place next to the existing code? → align them.
- Is any comment explaining *what* instead of *why*? → delete it.
- Could a rename remove a comment? → rename, remove the comment.
- More comments than the existing file would have? → cut them down.
