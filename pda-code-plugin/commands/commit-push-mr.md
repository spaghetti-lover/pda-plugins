---
description: Commit the current changes, push the branch, and open a GitLab MR
argument-hint: [overview | commit message notes]
---

Commit, push, and open a merge request for the current branch's changes.

1. Run `git status` and `git diff` (staged + unstaged) to see what changed. If there are no
   changes to commit, stop and say so.
2. Stage the relevant files. Prefer adding specific files over `git add -A`; if you use a broad
   add, review `git status` afterward and check file contents for anything that looks like a
   secret before committing.
3. Look at `git log` for this repo's commit message style, then draft a concise commit message
   (1-2 sentences, focused on *why*) that matches it. If `$ARGUMENTS` is given and is not the
   literal word `overview`, treat it as notes to steer the commit message.
4. Commit with that message. Do **not** add a `Co-Authored-By` trailer or any other mention of
   Claude — this commit should look like it came from the user alone.
5. Push the branch to its remote. If it has no upstream yet, set one (`git push -u origin
   <branch>`). Never force-push.
6. Write the MR description:
   - If `$ARGUMENTS` is `overview`, invoke the `pda-code:writing-pr` skill to draft a full
     PR/MR description, and use that as the description.
   - Otherwise, keep the description short and to the point — just what changed, no "overview"
     section or full PR template.
7. Create the GitLab merge request with `glab mr create`, using the commit message as the title
   and the description from step 6. If `glab` isn't authenticated, tell the user to run `glab
   auth login` instead of falling back to another tool.
8. Report the resulting MR URL.

If the branch is `main`/`master`, or if there's no upstream project configured for `glab`, stop
and ask the user how to proceed instead of guessing.
