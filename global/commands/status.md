# Status

Show the current sync status and branch notes for the active project.

## Instructions

1. Read `~/claude-sync/.last-sync-result`. If it exists, display its
   contents (sync status + branch notes). Then delete the file.

2. If the file doesn't exist, resolve the status live:
   - Run `claude-sync status` to check sync state
   - Run `git remote get-url origin` and `git branch --show-current` to identify
     the project and branch
   - Read `~/claude-sync/.local-config.json` to find the project name
   - Read `~/claude-sync/projects/<name>/branches/<branch>/notes.md`
     if it exists

3. Present a concise summary:
   - Sync state (up to date or what changed)
   - Current project and branch
   - Branch notes summary (in progress, remaining, blockers) or "no notes yet"
