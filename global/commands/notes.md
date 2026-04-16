# Branch Notes

Read and summarize the branch notes for the current project and branch. This is
the same logic that runs automatically on session start — use this command when
you've navigated to a different worktree or switched branches mid-session.

## Instructions

1. Read `~/claude-sync/.local-config.json`. If it doesn't exist, inform the user
   that no sync config is set up and suggest running `claude-sync setup`.

2. Run `git remote get-url origin 2>/dev/null` in the current directory. If this
   fails (not a git repo), inform the user that the current directory is not a
   git repository.

3. Match the remote URL against the `remote` field of each project in
   `.local-config.json`. If no project matches, inform the user that this
   project is not registered and suggest `claude-sync add-project`.

4. Run `git branch --show-current` to get the current branch name. Replace `/`
   with `-` in the branch name for the directory lookup.

5. Based on the project's `notes_source` (default is `sync`):

   - **sync:** Read `~/claude-sync/projects/<project-name>/branches/<branch-name>/notes.md`
   - **notion:** Fetch the Notion page content:
     ```bash
     curl -s -H "Authorization: Bearer $NOTION_API_KEY" \
          -H "Notion-Version: 2022-06-28" \
          "https://api.notion.com/v1/blocks/<notion_page_id>/children"
     ```
     Parse and summarize the block content.
   - **obsidian:** Read the markdown file at `<obsidian_vault>/<obsidian_note>`

6. If notes exist, provide a clear summary of where things stand — what's in
   progress, what's remaining, any blockers. If no notes exist for this branch,
   say so and suggest running `/recap` at the end of the session to create them.
