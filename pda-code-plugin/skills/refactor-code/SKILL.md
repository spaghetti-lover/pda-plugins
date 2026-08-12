---
name: refactor-code
description: Refactor existing code — restructuring, renaming, extracting, or cleaning it up — without changing external behavior, while matching the codebase's existing conventions (naming, formatting, patterns, helper reuse) and keeping comments minimal. Use whenever the user asks to refactor, clean up, simplify, restructure, extract, rename, or improve existing code without changing what it does.
disable-model-invocation: true
---

# Refactor Code

Use this skill when refactoring existing code. The goal is to improve the code's internal structure — readability, duplication, naming, organization — **without changing its observable behavior**, and to leave it reading like it was always written this way, in the codebase's own style. The default failure modes are silently changing behavior, expanding scope beyond what was asked, and refactoring in one large unreviewable step instead of small verifiable ones.

## Behavior-preservation rules

1. **Behavior stays identical.** Inputs, outputs, side effects, error handling, and edge cases must not change. If a refactor seems to require a behavior change, stop and confirm with the user before proceeding — don't fold it in silently.

2. **Establish a safety net first.** Before refactoring, check whether tests already cover the code. Run them to confirm they pass before you start. If no relevant tests exist and the codebase has a test convention, consider whether to add a characterization test first so you can verify behavior didn't change — but don't block on this if the user just wants the refactor done.

3. **Refactor in small, verifiable steps.** Prefer a sequence of small changes (extract method, rename, move, inline) over one large rewrite. After each step, re-run tests or otherwise verify behavior is unchanged before moving to the next step.

4. **Don't mix refactoring with feature changes.** If you notice a bug or missing feature while refactoring, don't fix or add it in the same change — mention it to the user separately. Refactoring and behavior changes are different diffs.

## Code convention rules

1. **Match the surrounding code first.** Read the file being refactored and nearby files before changing anything. The result should look like whoever wrote the surrounding code wrote it — not like a new style was introduced.

2. **Follow the codebase's naming idioms.** When renaming or extracting, match existing casing and verb/noun patterns. Name things so intention is clear without reading the implementation, consistent with how existing names are formed.

3. **Reuse over reinvent.** While restructuring, replace ad-hoc logic with an existing helper or utility if one already does the job, instead of leaving duplicated logic in place.

4. **Match formatting and idioms.** Keep the existing indentation, import ordering, and language idioms. Don't introduce a new pattern, library, or abstraction the project doesn't already use, unless that is the explicit point of the refactor.

5. **Follow the existing structure.** When moving or extracting code, place it where similar code already lives. Don't invent a new file/module layout when an established one fits.

6. **Use the right cleanup idiom.** When a resource's cleanup does more than just close (it flushes, finalizes, emits, etc.), use `x := createX(); x.DoSth(); x.Close()` rather than a `closeX := createX(); closeX()` alias.

7. **Don't expand scope.** Refactor exactly what was discussed — don't reorganize unrelated code, rename unrelated symbols, or restyle files you weren't asked to touch.

## Comment rules

1. **Match the surrounding code first.** Copy the density, length, tone, and format of existing comments (`//` vs `/** */`, full sentences vs fragments). If the file has almost no comments, keep it that way.

2. **Re-evaluate comments as you restructure.** A comment written for the old structure may no longer make sense after extraction/renaming — update or remove it rather than letting it go stale or float next to the wrong code.

3. **Default to no comment.** Prefer a better name or clearer structure over adding a comment. Only add one when the code truly can't be made self-explanatory.

4. **Comment the "why", never the "what".** Preserve and carry forward comments that explain a non-obvious reason, edge case, or workaround. Drop comments that just restate what the code does.
   - ❌ `// increment the counter` above `counter++`
   - ✅ `// API returns 1-based page index, so subtract 1`

5. **Keep each comment short.** One line where possible. No multi-paragraph essays, no banner/decoration comments unless the file already uses them.

6. **Don't add comments describing the refactor itself.** No "extracted this into a helper", "renamed from X", or "moved from Y" — version control already records that.

7. **Don't comment out code.** Delete it instead.

## Refactoring workflow

1. Read the code being refactored and nearby files to learn existing conventions before changing anything.
2. Confirm scope: refactor exactly what was discussed, nothing more.
3. Check for existing tests covering this code; run them to get a passing baseline.
4. Make the change in small steps (extract, rename, move, inline, deduplicate), matching existing conventions and reusing existing helpers.
5. Re-run tests (or otherwise verify behavior) after each step, and again at the end.
6. Update or remove comments that no longer fit the new structure; add new ones only where the comment rules above justify it.
7. Before finishing, re-read your diff. Confirm behavior is unchanged, the code matches surrounding conventions, and no unrelated code was touched.

## Quick self-check before done

- Does the code behave exactly as before for every input, including edge cases and errors? → if unsure, verify with tests.
- Did I touch anything outside the agreed scope? → revert it.
- Did I mix in a bug fix or feature change? → split it out and flag it to the user instead.
- Would this code look out of place next to the existing code? → align it.
- Did I leave duplicated logic a helper already handles? → reuse the helper.
- Are there stale comments left over from the old structure? → update or delete them.
- Did I add comments describing the refactor itself? → delete them.
