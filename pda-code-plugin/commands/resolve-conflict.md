---
description: Rebase the current branch onto the latest main/master and resolve conflicts
argument-hint: [base branch, if not main/master]
---

Rebase the current branch onto the latest upstream base branch so its MR/PR can be merged.

If `$ARGUMENTS` names a branch, use it as the base. Otherwise detect the base from
`git remote show origin` (or `origin/HEAD`), which is usually `master` or `main`.

## Before rebasing

1. Stop and ask the user how to proceed if the current branch **is** the base branch, or if the
   base cannot be determined.
2. Run `git status`. If the working tree is dirty, stash it (`git stash push -u`) and restore it
   after the rebase; say that you did.
3. Create a backup branch at the current tip: `git branch <branch>-backup-prerebase`. Tell the
   user its name and SHA — it is their rollback if the rebase goes wrong.
4. `git fetch origin <base>`, then review what is about to collide:
   - `git log --oneline origin/<base>..HEAD` — your commits.
   - `git log --oneline HEAD..origin/<base>` — what landed upstream.
   - The files touched by both sides, which is where conflicts will be:
     `comm -12 <(git diff --name-only <merge-base>..HEAD | sort) <(git diff --name-only <merge-base>..origin/<base> | sort)`
   - Migration/schema directories on both sides, to catch duplicate numbering early.
5. Record a test baseline **before** touching anything: run the repo's test suite and note the
   result. Without it you cannot tell a rebase breakage from a pre-existing failure.

## Resolving

6. Run `git rebase origin/<base>`. For each conflict, read the surrounding code on **both** sides
   before editing — enough to know what each side was trying to do.
7. Resolve by intent, not by picking a side. Taking `--ours` or `--theirs` wholesale is almost
   always wrong when both sides changed real behaviour. The common hard case: upstream rewrote or
   moved the code your change lived in, so git shows your whole block as an addition. Then port
   only your *semantic* change into the new upstream structure, and drop your copy of the
   structure itself.
8. After resolving each file, grep it for leftover `<<<<<<<`, `=======`, `>>>>>>>` markers, and
   check that every symbol touched by the resolution still exists and is still used.
9. `git add` the resolved files and `git rebase --continue`. If a conflict is genuinely ambiguous
   — two plausible resolutions with different behaviour — stop and ask rather than guessing.

## Verifying

10. Re-run the test suite and compare against the step 5 baseline.
11. If a test now fails, do not assume it is your conflict resolution. Check out `origin/<base>`
    clean and run the same test there. A failure that reproduces on the base branch is
    pre-existing or environmental (network, containers, missing services) — say so explicitly and
    do not try to fix it. Only a failure unique to the rebased branch is yours.
12. If a test fails because upstream changed behaviour your branch depends on, fix it, then tell
    the user plainly: what upstream changed, what you did about it, and that the fix touches
    upstream behaviour inside their commit. Offer to split it into its own commit.
13. Run project-specific consistency checks that a rebase can silently break — schema/migration
    drift checks, lockfile or dependency checks, codegen. Distinguish new drift from drift that
    was already there.
14. Confirm no unrelated or local-only files rode along in the rebase:
    `git diff --name-only origin/<base>..HEAD`. Watch for lockfiles pointing at local paths,
    config holding credentials, editor/agent settings, and scratch files.

## Finishing

15. Force-push only with `--force-with-lease`, and only when the branch is **not** `main`,
    `master`, or the detected base. On any of those, stop and tell the user.
16. Report: the conflicts and how each was resolved, the test result against the baseline, any
    failure shown to be pre-existing, anything upstream changed that affected the branch, and the
    backup branch name.

Never run `git rebase --skip` — it silently drops a commit. Never `git add -A` during a rebase.
