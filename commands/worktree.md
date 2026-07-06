# Worktree

Inspect the current git worktree layout and help the user manage worktrees.

## Steps

1. Run `git worktree list --porcelain` to get all active worktrees with full detail.

2. Parse and display the worktrees in a clean markdown table with columns:
   - **Path** — absolute path of the worktree
   - **Branch** — checked-out branch (or `HEAD detached` if detached)
   - **Commit** — short SHA
   - **Type** — `main` for the primary worktree, `linked` for all others

3. Use AskUserQuestion to ask what the user wants to do next:

```
What would you like to do with worktrees?
```

Options:
- **Create a new worktree** — ask for a branch name and target path, then run `git worktree add <path> <branch>` (creating the branch with `-b` if it doesn't exist yet)
- **Remove a worktree** — ask which path to remove, confirm it's safe (check for uncommitted changes via `git -C <path> status --short`), then run `git worktree remove <path>`
- **Prune stale worktrees** — run `git worktree prune -v` to clean up references to deleted directories
- **Nothing, just wanted to see the list** — stop here

4. Execute the chosen action. For destructive actions (remove, prune), show what will be affected and confirm with the user before running.
