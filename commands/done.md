# Done — Close Worktree

Clean up the current feature worktree, update the tracker, and prepare for the next task.

---

## Phase 1 — Detect Context

Run these in parallel:
- `git rev-parse --show-toplevel` → current worktree absolute path
- `git branch --show-current` → current branch name
- `git worktree list --porcelain` → full worktree list

From `git worktree list --porcelain`, the **first block** is always the main repo. Compare its `worktree` path to the current `--show-toplevel` output.

**If they match (you are in the main repo, not a worktree):**
Tell the user:
> "You are not inside a worktree — this is the main repo checkout. `/done` only works from inside a worktree directory. Use `git branch -d <branch>` if you want to delete a branch manually."
Stop here.

**If they differ:** you are inside a worktree. Continue.

---

## Phase 2 — Remove Worktree

Run from the main repo path:
```
git -C <main-repo-path> worktree remove <current-worktree-path>
```

**If the command fails** (worktree has uncommitted changes):
Tell the user:
> "The worktree has uncommitted changes and cannot be removed automatically.
> Commit or discard the changes first, or confirm a force-remove."

Use `AskUserQuestion`:
> "How would you like to proceed?"

Options:
- "Force remove (discard uncommitted changes)" — run `git -C <main-repo-path> worktree remove --force <path>`
- "Abort — I'll handle the changes myself" — stop here

---

## Phase 3 — Wrap Up

Tell the user:
> "Worktree `<path>` (branch `<branch>`) has been removed."
>
> "Run `/clear` to reset the conversation context and start fresh for your next task."
