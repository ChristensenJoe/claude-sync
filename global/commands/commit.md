# Commit

Safe commit and push workflow that confirms your context before acting. Prevents
accidentally committing to the wrong branch or worktree.

## Instructions

### 1. Show current context

Display exactly four lines, clearly labeled:

```
Repo:      <repo name from git remote, e.g., massdriver-ui>
Branch:    <current branch from git branch --show-current>
Worktree:  <basename of current working directory>
Changes:   <one-line summary, e.g., "3 files modified, 1 file added (unstaged)" or "2 staged, 4 unstaged">
```

To get this information:
- Repo name: `git remote get-url origin 2>/dev/null` — extract the repo name from the URL
- Branch: `git branch --show-current`
- Worktree: `basename "$(pwd)"`
- Changes: `git status --porcelain` — count staged (lines starting with `M `, `A `, `D `, `R `) and unstaged (lines starting with ` M`, ` D`, `??`) files, summarize in one line

If there are no changes at all, say "No changes to commit" and stop.

### 2. Present options

Ask the user to choose using AskUserQuestion:

**Option 1: Commit** — Claude generates a commit message based on the diff, commits staged + unstaged changes.

**Option 2: Commit & push** — Same as option 1, but also pushes to the remote after committing.

**Option 3: Commit (custom message)** — Ask the user for a commit message, then commit.

**Option 4: Commit & push (custom message)** — Ask the user for a commit message, then commit and push.

**Option 5: Switch branch** — Ask which branch to switch to (`git switch <branch>`), then restart from step 1.

**Option 6: Cancel** — Do nothing.

### 3. Execute

For options 1-4:

1. Stage all changes: `git add -A`
2. Run `git diff --cached` to see what's being committed
3. For auto-generated messages (options 1-2): draft a concise commit message based on the diff — focus on the "why" not the "what", 1-2 sentences max
4. For custom messages (options 3-4): use the user's message exactly
5. Commit using a heredoc for the message:
   ```bash
   git commit -m "$(cat <<'EOF'
   <message>

   Co-Authored-By: Claude <noreply@anthropic.com>
   EOF
   )"
   ```
6. For push options (2, 4): run `git push`. If the branch has no upstream, use `git push -u origin <branch>`.

After committing, show the commit hash and a one-line confirmation.
