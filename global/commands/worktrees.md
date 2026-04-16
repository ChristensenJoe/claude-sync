# Worktrees

Manage git worktrees for the current repository. List, create, switch, and clean
up worktrees with branch context awareness.

## Instructions

### 1. Show current state

Run `git worktree list` and parse the output. Display a table:

```
## Worktrees

  * main              ~/projects/my-app                    (clean)
    feature-billing   ~/projects/my-app--feature-billing   (3 files modified)
    fix-auth          ~/projects/my-app--fix-auth          (clean, merged)
```

For each worktree:
- Mark the current one with `*`
- Show the branch name and path
- Show status: clean, N files modified, or merged (if the branch has been merged to main)

Then present options using AskUserQuestion:

### 2. Options

**Switch** — Ask which worktree to switch to. After switching (`cd` to the worktree
path), run `/notes` to load branch context for the new location.

**Create** — Ask for:
- Branch name (create new branch, or use existing untracked branch)
- Base branch (default: main)
- Run `git worktree add <path> -b <branch> <base>` (or without `-b` for existing branches)
- Path defaults to `<repo-root>--<branch-name>` (sibling directory to the main checkout)
- After creation, offer to switch to it

**Delete** — Show worktrees that can be deleted (not the main working tree). If the
worktree has uncommitted changes, warn and ask for confirmation. Run
`git worktree remove <path>`. Also offer to delete the branch if it's been merged.

**Clean up merged** — Find worktrees whose branches have been merged to main.
List them and offer to delete all or select specific ones. For each:
- `git worktree remove <path>`
- `git branch -d <branch>` (safe delete, only works if merged)
- Also clean up any branch notes in the sync repo:
  `rm -rf <sync-repo>/projects/<project>/branches/<branch>/`

**Prune** — Run `git worktree prune` to clean up stale worktree references
(worktrees whose directories have been manually deleted).

### 3. After any action

Re-display the worktree list so the user sees the updated state.
