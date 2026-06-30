---
name: clean-projects
description: Safely clean up unused, old, or unimportant project folders by moving them to Trash (recoverable), never rm -rf. Surveys git status, last-activity, and secrets, then deletes only what's safe and asks before trashing folders that contain real .env secrets. Use when the user wants to "clean up projects", "delete old/unused folders", "free up space in Project", or similar.
metadata:
  mcpmarket-version: 1.0.0
---

# Clean Projects

Safely retire project folders the user no longer needs by moving them to the system
**Trash** (recoverable), never with `rm -rf`. The whole point is that the user can restore
anything later, so destructive deletion is forbidden in this skill.

## Core rules

1. **Never use `rm -rf`.** Always move to Trash via `gio trash <path>` (the standard XDG
   Trash). Restore by moving the folder back from `~/.local/share/Trash/files/` to the
   `Path=` recorded in its `~/.local/share/Trash/info/<name>.trashinfo` file. (Note:
   `gio trash --restore <original-path>` does NOT work here — it expects a `trash://` URI —
   so use the manual move-back below for restores.)
2. **Uncommitted / unpushed code does NOT block deletion.** The user accepts losing
   work-in-progress code; Trash keeps it recoverable anyway.
3. **The real gate is secrets, not code.** Before trashing a folder, check for **real
   `.env` files**. A real `.env` (e.g. `backend/.env`, `.env.local`) means **ask the user
   first**. Template files — `.env.example`, `.env.local.example`, `.env.sample` — are NOT
   secrets and do NOT gate deletion.
4. **Never trash active multi-repo workspaces** that only lack a root `.git` but contain
   nested, recently-committed repos. Always check for nested `.git` dirs before judging a
   "no-git" folder as abandoned.
5. **Clones of other people's repos are always safe** to trash (re-clonable from remote).

## Workflow

### Step 1 — Survey every candidate folder

Run in the project directory the user names (default `~/Project`). For each top-level
folder gather: git presence (root + nested `.git`), last commit date, remote URL,
unpushed-commit count, dirty-file count, and real `.env` files.

```bash
cd <project-dir>
for d in */; do
  d="${d%/}"
  echo "===== $d ====="
  if [ -d "$d/.git" ]; then
    echo "last=$(git -C "$d" log -1 --format='%cd' --date=short 2>/dev/null)" \
         "remote=$(git -C "$d" remote get-url origin 2>/dev/null || echo NONE)" \
         "unpushed=$(git -C "$d" log --branches --not --remotes --oneline 2>/dev/null | wc -l)" \
         "dirty=$(git -C "$d" status --porcelain 2>/dev/null | wc -l)"
  else
    echo "no root .git; nested:"; find "$d" -maxdepth 2 -name .git -type d 2>/dev/null | sed 's/^/  /'
  fi
  find "$d" -type f \( -name '.env' -o -name '.env.*' \) \
    ! -name '*.example' ! -name '*.sample' \
    -not -path '*/node_modules/*' -not -path '*/.venv/*' -not -path '*/venv/*' 2>/dev/null \
    | sed 's/^/  ENV: /'
done
```

### Step 2 — Classify each folder

- **Safe to trash**: empty; no-git but superseded/reproducible; clean git repo with a
  remote and 0 unpushed commits; a clone of someone else's repo. No real `.env`.
- **Ask first**: folder contains a real `.env` file (secrets).
- **Keep**: active nested-repo workspace, or a folder the user explicitly wants to retain.

Present a compact table (folder | last activity | unpushed | dirty | real .env? | verdict)
so the user sees the reasoning at a glance.

### Step 3 — Confirm

Use the AskUserQuestion tool for any folder that has a **real `.env`**, and whenever the
deletion set is ambiguous. Let the user opt folders in/out. Never trash anything that
wasn't confirmed.

### Step 4 — Move to Trash (not delete)

```bash
gio trash <path1> <path2> ...
```

If `gio` is unavailable, fall back to a manual XDG-trash move (copy the folder into
`~/.local/share/Trash/files/` and write a matching `~/.local/share/Trash/info/<name>.trashinfo`
recording its original `Path=`). Never fall back to `rm`.

### Step 5 — Verify and report

```bash
ls <project-dir>                                            # gone from project
ls ~/.local/share/Trash/files/ | tail                       # landed in Trash
grep -l "<folder>" ~/.local/share/Trash/info/*.trashinfo    # restore metadata exists
```

Report what was trashed, what was kept and why, and tell the user how to **restore** any
folder (move it back from Trash to its original path):

```bash
# Restore <name> to where it was deleted from:
orig=$(awk -F= '/^Path=/{print $2}' ~/.local/share/Trash/info/<name>.trashinfo)
mv ~/.local/share/Trash/files/<name> "$orig"
rm ~/.local/share/Trash/info/<name>.trashinfo
```
