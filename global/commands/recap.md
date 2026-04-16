# Session Recap

Summarize what was accomplished in this session and write branch notes for the
current project and branch. Use at the end of a work session to capture progress
and set up the next session for a clean start.

This pairs with the branch notes session-start hook — what you write here is what
Claude reads back to you when you start your next session (or when you run `/notes`).

## Instructions

### 1. Review the session

Scan the full conversation for:

- **Completed work** — tasks finished, features implemented, bugs fixed
- **In-progress work** — things started but not finished, with current state
- **Decisions made** — architectural choices, approach changes, scope adjustments
- **Blockers discovered** — issues that need resolution before continuing
- **Next steps** — logical follow-ups that should happen next session

### 2. Identify the project and branch

1. Read `~/claude-sync/.local-config.json`
2. Run `git remote get-url origin` and match against registered projects' `remote` fields
3. Run `git branch --show-current` to get the branch name
4. Replace `/` with `-` in the branch name for the directory path

If the current directory is not a registered project, inform the user and suggest
running `claude-sync add-project <name> [path]` to register it. Do not create
notes outside the sync repo.

### 3. Read existing branch notes

Check if `~/claude-sync/projects/<project-name>/branches/<branch-name>/notes.md`
exists. If it does, read it to understand existing context.

### 4. Write branch notes

Create or update `notes.md` at
`~/claude-sync/projects/<project-name>/branches/<branch-name>/notes.md`
with a clear, scannable format:

```markdown
# Branch Notes: <branch-name>

Last updated: YYYY-MM-DD

## In Progress
- [ ] Item currently being worked on — current state and what remains
- [ ] Another in-progress item

## Up Next
- [ ] Logical next step from current work
- [ ] Another upcoming item

## Done (recent)
- [x] Completed item — brief note on what was done
- [x] Another completed item

## Blocked
- [ ] Blocked item — what's blocking it and what needs to happen

## Context
- Any important context for next session
- Decisions made that affect upcoming work
```

**Rules for updating:**
- Move completed items to "Done (recent)" with a checkmark
- Add new items discovered during the session
- Update in-progress items with current state
- Keep "Done" list trimmed — only last ~5-10 completed items
- Use absolute dates, not relative ("2026-04-15" not "today")

### 5. Invoke /learn

After writing branch notes, invoke `/learn` via the Skill tool to persist any
decisions, feedback, or project knowledge from this session to the appropriate
memory layer. This ensures nothing valuable from the session is lost — branch
notes capture the ephemeral task state, while `/learn` captures the durable
knowledge.

### 6. Present summary

Give the user a brief recap:
- What was accomplished
- What's remaining
- Any blockers or decisions that need attention
- Confirm branch notes were written and where
- Suggest running `/sync` to push immediately, or note that the next
  `claude-sync push` will include the notes
