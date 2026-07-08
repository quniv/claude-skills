# claude-skills

Custom slash commands for [Claude Code](https://claude.ai/code). Drop these into
`~/.claude/commands/` (global) or `.claude/commands/` (per-project) and invoke with `/<name>`.

## Install

```bash
git clone git@github.com:quniv/claude-skills.git
ln -s "$(pwd)/claude-skills/commands/"*.md ~/.claude/commands/
```

Or symlink individual files if you only want some of them.

## Skills

| Command | Purpose |
|---|---|
| `/architect` | Runs a structured Q&A discovery session for a new project, then produces a full set of architecture documents. |
| `/commit` | Stages changes, writes a commit message, confirms the branch, and optionally pushes. |
| `/custom` | Meta-builder for Claude Code customizations — creates or edits skills, hooks, settings, agents, or CLAUDE.md files through a guided plan-then-execute flow. |
| `/done` | Closes out the current feature worktree: cleans up, updates the tracker, and preps for the next task. |
| `/feat` | Feature-branch orchestrator — sets up a clean worktree, then drives implementation via the task orchestrator. |
| `/fix` | Focused debugging workflow — branches, investigates, proposes a fix, and applies it after confirmation. |
| `/go` | Task orchestrator — breaks a request into subtasks and drives them to completion using specialized agents, in parallel where possible. |
| `/hey` | Read-only project pulse check — git status, open PRs/issues, worktrees — with a suggested next step if something needs attention. |
| `/pr` | End-to-end PR workflow — creates the PR, then loops through conflicts, failing checks, and review comments until it's mergeable. |
| `/worktree` | Inspects and manages git worktree layout. |

## Notes

- Each skill is a self-contained Markdown file — read it before using it, they're not black boxes.
- Several skills (`/pr`, `/fix`, `/feat`, `/go`) assume the `gh` CLI is authenticated and lean on `AskUserQuestion`-style confirmation before anything destructive (pushes, commits, conflict resolution).
- These were written for a specific workflow (zsh, git, GitHub) — adjust freely for yours.
