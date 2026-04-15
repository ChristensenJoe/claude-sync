# Doctor — Diagnose Configuration Issues

Run a health check on your Claude Code sync setup and report any issues.

## Instructions

1. Run the doctor command:

   ```bash
   $HOME/claude-sync/scripts/claude-sync doctor
   ```

2. Read the output and present a summary to the user:
   - List what passed and what failed
   - For any failures, explain what the issue means and how to fix it
   - If everything passed, confirm the setup is healthy

3. If there are fixable issues, offer to run `claude-sync setup` to resolve them.
