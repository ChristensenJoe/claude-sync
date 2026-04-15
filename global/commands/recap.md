# Session Recap

Summarize what was accomplished in this session and update TODO.md with current
status. Use at the end of a work session to capture progress and set up the next
session for a clean start.

This pairs with the TODO.md session-start hook — what you write here is what
Claude reads back to you when you start your next session.

## Instructions

### 1. Review the session

Scan the full conversation for:

- **Completed work** — tasks finished, features implemented, bugs fixed
- **In-progress work** — things started but not finished, with current state
- **Decisions made** — architectural choices, approach changes, scope adjustments
- **Blockers discovered** — issues that need resolution before continuing
- **Next steps** — logical follow-ups that should happen next session

### 2. Read current TODO.md

Check if `TODO.md` exists in the current directory:
- If it exists, read it to understand existing items
- If it doesn't exist, create one

### 3. Update TODO.md

Update (or create) `TODO.md` with a clear, scannable format:

```markdown
# TODO

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

## Notes
- Any important context for next session
- Decisions made that affect upcoming work
```

**Rules for updating:**
- Move completed items to "Done (recent)" with a checkmark
- Add new items discovered during the session
- Update in-progress items with current state
- Keep "Done" list trimmed — only last ~5-10 completed items
- Use absolute dates, not relative ("2026-04-15" not "today")

### 4. Invoke /save-notes

After updating TODO.md, invoke `/save-notes` via the Skill tool to persist any
decisions, feedback, or project knowledge from this session to the appropriate
memory layer. This ensures nothing valuable from the session is lost.

### 5. Present summary

Give the user a brief recap:
- What was accomplished
- What's remaining
- Any blockers or decisions that need attention
- Confirm TODO.md was updated
