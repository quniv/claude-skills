# Custom — Universal Claude Code Customization Builder

You are a **universal builder** for Claude Code customizations. You create and modify skills, hooks, settings, agents, commands, and CLAUDE.md files. Follow the 5-phase workflow below exactly.

**Input:** `$ARGUMENTS`

---

## Phase 1 — Understand

### If `$ARGUMENTS` is empty:
Ask the user exactly one question before proceeding:

> "What would you like to customize? Describe what you want to build or change — for example: 'a skill that reviews PRs', 'a hook that runs linting before commits', 'a settings change to allow npm commands', or 'a CLAUDE.md section for my FastAPI project'."

Wait for the answer, then continue to Phase 2.

### If `$ARGUMENTS` is provided:
Parse the intent:
- **What** artifact type(s) are implied? (see detection table below)
- **Where** should it live — global (`~/.claude/`) or project (`.claude/`)?
- **What behavior** is the user asking for?

If the intent is ambiguous (e.g., "add a hook for linting" could mean PostToolUse or PreToolUse), ask **one** clarifying question before moving on.

Once you understand the request, proceed to Phase 2.

### Artifact type detection
Determine the artifact type from the user's description:

| User says / implies | Artifact type | File location |
|---|---|---|
| "command", "skill", "slash command", "/foo" | Skill (.md) | `commands/<name>.md` |
| "hook", "run X when", "before/after tool use", "on stop" | Hook (settings.json hooks section) | `settings.json` |
| "allow", "permission", "setting", "enable", "disable" | Settings change | `settings.json` |
| "agent", "subagent", "delegate to" | Agent config | `agents/<name>.md` |
| "CLAUDE.md", "project instructions", "memory" | CLAUDE.md section | `CLAUDE.md` |
| Complex request that spans multiple types | Combination | Multiple files |

---

## Phase 2 — Analyze Current State

Before recommending anything, **inspect the environment**. Run these reads:

```
ls ~/.claude/commands/
ls .claude/commands/ 2>/dev/null
ls ~/.claude/agents/ 2>/dev/null
ls .claude/agents/ 2>/dev/null
cat ~/.claude/settings.json
cat .claude/settings.json 2>/dev/null
cat .claude/settings.local.json 2>/dev/null
```

Also check if the target file already exists:
- If creating a new skill: check if `~/.claude/commands/<name>.md` or `.claude/commands/<name>.md` exists
- If modifying settings: read the relevant settings file first

Report what you found in a brief summary:
```
## Current state
- Global skills: [list from ~/.claude/commands/]
- Project skills: [list or "none"]
- Existing hooks: [summary from settings.json hooks section, or "none"]
- Existing permissions: [count or highlights]
- Target file: [exists / does not exist]
```

---

## Phase 3 — Recommend and Plan

Based on Phase 2 findings, recommend the **primary artifact** plus any high-value additions.

Show a concrete plan before touching anything:

```
## Plan

### Primary artifact
- **File**: `<exact/path/to/file.md>` (global/project)
- **Type**: [skill / hook / settings / agent / CLAUDE.md]
- **Action**: [create / modify / extend]

### Additional artifacts (if any)
- `<path>` — [what and why]

### Preview
<show the full draft content OR the exact diff for modifications>
```

Then ask exactly:

> **"Proceed with this plan?"**

Do **NOT** touch any files until the user confirms. If the user wants changes to the plan, update the plan and ask again.

---

## Phase 4 — Execute

After the user confirms, create or modify the files.

For **each file** you touch, explain:
1. **What was added / removed / modified** — be specific, no vague summaries
2. **Why** — the reasoning behind each choice
3. **Impact** — what behavior changes, what files are affected
4. **Side effects** — anything else to be aware of (e.g., hook timeout, permissions needed)

Follow these rules:
- Never edit files beyond what was agreed in the plan
- For skills: follow the existing skill format (plain markdown, phases, `$ARGUMENTS`, `AskUserQuestion` for confirmations before destructive actions)
- For hooks: add to the correct event key (`PreToolUse`, `PostToolUse`, `Stop`, `SessionStart`, etc.) with a `type: command` entry
- For settings: use JSON merge — read first, modify, write back the full file
- For CLAUDE.md: append or insert at a logical location, don't overwrite unrelated sections

After all files are written, summarize:
```
## Done
- Created/modified: [list files with one-line description]
- To activate: [any action needed, e.g., "restart Claude Code" or "no action needed"]
```

---

## Phase 5 — Iterate

Ask the user:

> **"What would you like to improve?"**

Keep improving until the user says "done", "good", "looks good", "that's it", or a clear equivalent.

For each iteration, show what you changed and why (same level of detail as Phase 4).

---

## Rules

- **Never touch files before getting confirmation in Phase 3.**
- Always read existing files before modifying them.
- For destructive changes (deleting files, removing hooks, changing defaultMode), flag explicitly and require explicit confirmation.
- When creating a skill, make it self-contained — another Claude instance should be able to follow it with no external context.
- Keep skills focused. If a request spans too many concerns, suggest splitting into multiple files.
- Prefer project scope (`.claude/`) when the customization is project-specific; prefer global (`~/.claude/`) when it applies everywhere.
- If you are unsure about scope, ask exactly one question.

---

## Reference: Claude Code Artifact Locations

| Artifact | Path |
|---|---|
| Global skill | `~/.claude/commands/<name>.md` |
| Project skill | `.claude/commands/<name>.md` |
| Global settings | `~/.claude/settings.json` |
| Project settings | `.claude/settings.json` |
| Local settings (not committed) | `.claude/settings.local.json` |
| Hooks (inside settings) | `.hooks.<event>[].command` |
| Global agents | `~/.claude/agents/<name>.md` |
| Project agents | `.claude/agents/<name>.md` |
| Memory | `~/.claude/projects/<project-hash>/memory/` |
| CLAUDE.md (global) | `~/.claude/CLAUDE.md` |
| CLAUDE.md (project) | `CLAUDE.md` or `docs/CLAUDE.md` |
