# claude-sync

Sync your [Claude Code](https://docs.anthropic.com/en/docs/claude-code) configuration across devices. Back up your memories, commands, settings, and project knowledge to a private Git repo — then pull them down on any machine.

**This is a template.** Click **"Use this template"** to create your own private copy, then follow the setup below.

---

## Why

Claude Code gets smarter the more you use it — but only on the machine you're using. Your corrections, preferences, project knowledge, and custom workflows live in `~/.claude/` and vanish when you switch devices.

**claude-sync** fixes that. It gives you:

- **Portable configuration** — memories, commands, settings, and project knowledge sync to a Git repo and pull down on any machine
- **Session-start hooks** — every session auto-pulls the latest config and loads your branch notes
- **Professional workflows** — 10 slash commands for auditing, pattern detection, session management, and knowledge capture
- **Cross-platform** — works on macOS, Linux, and WSL with any Claude account

---

## The AI Knowledge Flywheel

The most powerful thing about claude-sync isn't the file syncing — it's what it enables.

Every time you work with Claude, knowledge is created: you correct an approach, confirm a pattern, make an architectural decision, discover a convention. Without persistence, that knowledge evaporates at the end of each session. Claude makes the same mistakes, asks the same questions, misses the same context — every time.

**claude-sync turns that into a flywheel.** Three layers of persistent knowledge compound over time:

### Layer 1: Global Memory — Who You Are
Your cross-project identity. How you like to work, what patterns you prefer, what mistakes Claude should never repeat. These follow you everywhere — every project, every device, every session.

*"Skip the trailing summary — I can read the diff." "Don't abstract prematurely — three similar lines are better than a premature helper." "Always verify a convention exists in project docs before citing it as rationale."*

### Layer 2: Project Memory — What You Know
Per-project tribal knowledge that Claude can't derive from the code alone. What you're working on, what the gotchas are, where things live outside the repo. This is loaded only when you're in that specific project.

*"Billing migration is in progress — drawer is done, form validation is remaining." "The `orgId` field is auto-injected by the hook wrapper, don't flag it as missing." "Deploy issues are tracked in the Linear project OPS-INFRA."*

### Layer 3: Repo Rules — What Everyone Knows
Documented conventions in `CLAUDE.md` and `.claude/rules/` that live in the repo itself. These aren't personal — they're shared project documentation that any developer (human or AI) should follow. They travel with Git, not with claude-sync.

*"Pages must be thin route shells — no business logic, max 15 lines." "All new API calls target v2. Legacy v1 endpoints are frozen." "Test files are co-located with source, never in a top-level `__tests__/` folder."*

**The `/learn` command is the engine.** At the end of each session, it reviews your conversation and routes every finding to the right layer. Corrections become memories. Patterns become rules. Decisions become context. Nothing is lost.

Over weeks and months, Claude stops being a generic assistant and becomes a collaborator that knows your codebase, your preferences, and your team's conventions. The flywheel accelerates: fewer corrections needed → more trust → more ambitious tasks delegated → more knowledge captured.

---

## What Gets Synced

| Item | Local Path | Repo Path | Description |
|------|-----------|-----------|-------------|
| Global memories | `~/.claude/memory/` | `global/memory/` | Your cross-project preferences and corrections |
| Global commands | `~/.claude/commands/` | `global/commands/` | Slash commands available in every session |
| Global CLAUDE.md | `~/.claude/CLAUDE.md` | `global/CLAUDE.md` | Instructions that apply to every session |
| Settings | `~/.claude/settings.json` | `global/settings.json` | Hooks, env vars, plugins (permissions stay local) |
| Project memories | `~/.claude/projects/<encoded>/memory/` | `projects/<name>/memory/` | Per-project knowledge, synced by canonical name |

**Not synced:** session data, history, auth tokens, cache, in-repo `.claude/` config (that travels with each repo's Git history).

---

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- `git` with SSH or HTTPS access to your repo
- `jq` — `brew install jq` (macOS) or `sudo apt install jq` (Linux/WSL)
- `rsync` — pre-installed on macOS and most Linux distributions

---

## Setup

### 1. Create your copy

Click **"Use this template"** → **"Create a new repository"** → make it **private**.

### 2. Clone it

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO_NAME ~/claude-sync
```

### 3. Run setup

```bash
~/claude-sync/scripts/claude-sync setup
```

This will:
- Create `~/.claude/memory/` and `~/.claude/commands/` if they don't exist
- Copy the template commands and CLAUDE.md to your local Claude config
- Install two session-start hooks (branch notes + config sync)
- Merge template settings into your existing `~/.claude/settings.json`
- Install a `claude-sync` symlink at `~/.local/bin/` so you can run `claude-sync` from anywhere
- Add `~/.local/bin` to your PATH if needed

### 4. Register your projects

For each project whose Claude memories and branch notes you want to sync:

```bash
# Default — branch notes stored in sync repo
claude-sync add-project my-app /path/to/my-app

# Notion as note source
claude-sync add-project my-app /path/to/my-app \
  --notes notion --notion-page abc123def456

# Obsidian as note source
claude-sync add-project my-app /path/to/my-app \
  --notes obsidian --obsidian-vault ~/Documents/Vault --obsidian-note "Projects/my-app/notes.md"
```

Projects are identified by their git remote URL (auto-detected), so worktrees and multiple clones of the same repo all resolve to the same project. The local path is stored for memory directory mapping. On another machine, register the same project with its local path there — the remote URL ties them together.

### 5. Push your initial config

```bash
claude-sync push
```

### 6. Verify

```bash
claude-sync doctor
```

---

## Using claude-sync

### What happens automatically

Every Claude Code session triggers two hooks — no action required:

**Branch notes** — Claude detects the current project (by git remote) and branch, then reads the branch notes from your configured source (sync repo, Notion, or Obsidian). You get a summary of where you left off on this specific branch. Works seamlessly with worktrees — each worktree's branch has its own notes.

**Config sync** — Pulls the latest configuration from your repo. If you pushed changes from another machine, you'll see what's new. If the pull fails (no network, merge conflict), Claude tells you what went wrong so you can fix it — but never blocks your session.

### A typical coding session

The commands below are designed to bookend your work. At the start of a session, the hooks handle context automatically. At the end, a short ritual captures everything valuable before you close the terminal.

**Start of session** — automatic. The hooks fire, you see your branch notes and sync status. Start working.

**During the session** — just code. If you switch branches or navigate to a different worktree mid-session, run `/notes` to load the context for where you are now.

**End of session** — this is where the value compounds. Run `/learn` to capture any corrections, decisions, or patterns from the session into the right memory layer. Then run `/repo-audit` to verify your branch is clean against the project's rules before you step away. Together, these two commands take a minute and pay for themselves many times over — `/learn` makes the next session smarter, and `/repo-audit` catches issues while the context is still fresh rather than surfacing them in code review the next day.

For longer sessions or when you're switching between tasks, `/recap` is a natural stopping point. It writes branch notes summarizing where things stand, which Claude reads back to you next time. The loop closes itself.

### CLI commands

```bash
# Push local config to your repo
claude-sync push

# Check what's different between local and repo
claude-sync status

# Register a new project for memory syncing
claude-sync add-project my-app /path/to/my-app

# Health check
claude-sync doctor
```

### Adding another machine

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO_NAME ~/claude-sync
~/claude-sync/scripts/claude-sync setup
# After setup, the short form works:
claude-sync add-project my-app /home/you/projects/my-app
```

Your global memories, commands, settings, and project memories are all there. Sign into any Claude account — the config is account-agnostic.

---

## Slash Commands

All commands are installed globally and available in every Claude Code session.

| Command | Purpose | When to use |
|---------|---------|-------------|
| `/learn` | Capture session knowledge to the right memory layer | End of session, after corrections or decisions |
| `/recap` | Write branch notes + invoke `/learn` | End of session, especially longer ones |
| `/notes` | Read branch notes for current project/branch | After switching branches or worktrees mid-session |
| `/commit` | Safe commit/push with context confirmation | Any time you're ready to commit |
| `/sync` | Push config to cloud repo | After `/learn` if you want immediate cross-device sync |
| `/repo-audit` | Audit branch against project rules | Before opening a PR, end of session |
| `/update-patterns` | Detect undocumented patterns on branch | After establishing new conventions |
| `/init-rules` | Bootstrap CLAUDE.md for a new project | Once per project, when onboarding |
| `/doctor` | Health check for sync setup | After setup, when something seems broken |
| `/memory-audit` | Clean up redundant/stale memories | Weekly to monthly |

---

### Session workflow

<details>
<summary><b><code>/learn</code></b> — Session Knowledge Capture</summary>

The engine of the knowledge flywheel. Reviews your conversation and routes every finding to the right persistence layer:

- **Repo rules** — Invokes `/update-patterns` to document conventions in `CLAUDE.md` and `.claude/rules/` (creates them even in projects that have none yet)
- **Project memory** — Saves project-specific context: active work status, codebase gotchas, corrections, external references
- **Global memory** — Saves cross-project preferences: collaboration style, coding philosophy, behavioral corrections

Run this at the end of any session where Claude learned something — whether that's a correction you gave, a decision you made, or a pattern you established. This is what makes the next session better than the last one.
</details>

<details>
<summary><b><code>/recap</code></b> — Session Recap & Branch Notes</summary>

Summarize what was accomplished and write branch notes for the next session.

- Reviews the session for completed work, in-progress items, blockers
- Detects the current project (by git remote) and branch
- Creates or updates `notes.md` in `projects/<name>/branches/<branch>/` in the sync repo
- Invokes `/learn` to persist durable knowledge alongside the ephemeral branch state

The branch notes it writes are exactly what the session-start hook reads back to you next time — forming a complete session continuity loop. Each branch gets its own notes, so worktrees never collide.
</details>

<details>
<summary><b><code>/notes</code></b> — Read Branch Notes</summary>

Manually load the branch notes for your current project and branch. This is the same logic that runs automatically on session start.

Run this when you've navigated to a different worktree mid-session, switched branches, or just want to re-read where things stand. Lightweight and read-only.
</details>

<details>
<summary><b><code>/commit</code></b> — Safe Commit & Push</summary>

Shows your current context (repo, branch, worktree, changes) before committing, so you never accidentally commit to the wrong branch in a multi-worktree setup.

Options:
- Commit with auto-generated or custom message
- Commit and push with auto-generated or custom message
- Switch branch first, then commit
- Cancel

</details>

<details>
<summary><b><code>/sync</code></b> — Manual Config Push</summary>

Push your current configuration to the cloud repo. Run this after `/learn` if you want the captured knowledge available on another machine immediately, or let the next session's auto-pull handle it.
</details>

### Code quality

<details>
<summary><b><code>/repo-audit</code></b> — Branch Audit</summary>

Audit all changed files on your current branch against the project's documented rules.

- Dynamically discovers `CLAUDE.md` and `.claude/rules/*.md`
- Groups changed files by directory and spawns parallel audit agents
- Produces a prioritized report: Critical → Architecture → Style → Clean Areas
- Offers to fix violations

Run this before opening a PR, after a large refactor, or at the end of a session to catch issues while the context is fresh.
</details>

<details>
<summary><b><code>/update-patterns</code></b> — Pattern Detection</summary>

Analyze your branch for new patterns or conventions that aren't documented yet.

- Compares changed files against existing rules
- Identifies patterns worth codifying (architecture, naming, data, testing, etc.)
- Proposes new rules with specific text and destination files
- Creates `CLAUDE.md` and `.claude/rules/` if they don't exist yet

Run this after implementing a feature that establishes a new pattern, or during code review when you notice undocumented conventions. Note: `/learn` invokes this automatically, so you often don't need to run it directly.
</details>

### Project onboarding

<details>
<summary><b><code>/init-rules</code></b> — Bootstrap Project Rules</summary>

Scan a project's codebase and generate an initial `CLAUDE.md` and `.claude/rules/` with detected patterns, conventions, and architecture.

- Detects language, framework, and tooling from config files
- Reads representative source files to identify patterns
- Drafts a CLAUDE.md with project overview, file structure, build commands, and golden rules
- Presents everything for approval before writing

Run this when starting to use Claude in a project that has no Claude configuration, or when onboarding a team to Claude Code. You only need to run it once per project — after that, `/update-patterns` and `/learn` grow the documentation incrementally.
</details>

### Maintenance

<details>
<summary><b><code>/doctor</code></b> — Health Check</summary>

Diagnose common setup issues: missing dependencies, broken hooks, unregistered projects, stale config.

Run this after initial setup, when sync seems broken, or on a new machine to verify everything is wired up.
</details>

<details>
<summary><b><code>/memory-audit</code></b> — Memory Hygiene</summary>

Audit your Claude memory system for redundancy, staleness, and bloat.

- Finds duplicate or overlapping memories across layers
- Flags stale entries about completed work or deleted code
- Catches memories stored in the wrong layer (global vs project vs rules)
- Identifies verbose entries that could be trimmed
- Detects MEMORY.md index drift (orphaned entries, missing links)
- Offers to fix all issues with permission

Run this weekly to monthly. Every memory file is loaded into Claude's context window, so redundant or stale memories waste tokens and can cause conflicting instructions. Think of it as garbage collection for your AI knowledge base.
</details>

---

## How It Works

### Project identification

Projects are identified by **git remote URL**, not directory path. When you register a project with `add-project`, the script auto-detects the remote from `git remote get-url origin`. The session-start hook and `/notes` command match against this remote, which means:

- **Worktrees just work** — every worktree of the same repo shares the same remote
- **Multiple clones work** — re-clone to a different path and it still resolves
- **Cross-machine works** — different local paths, same remote, same project

### Branch notes

Each branch gets its own `notes.md` in the sync repo:

```
projects/my-app/branches/feature-billing/notes.md
projects/my-app/branches/fix-auth-bug/notes.md
```

Branch names with `/` (e.g., `feature/billing`) are normalized to `-` (`feature-billing`). Notes are written by `/recap` and read by the session-start hook and `/notes`.

### Note sources

By default, branch notes are stored as markdown files in the sync repo. You can configure alternative sources per project:

**Notion** — Notes are fetched from a Notion page via the API. Requires:
- `NOTION_API_KEY` environment variable (create an [integration](https://www.notion.so/my-integrations))
- A page ID configured via `--notion-page` during `add-project`

**Obsidian** — Notes are read from a markdown file in an Obsidian vault. Requires:
- Vault path configured via `--obsidian-vault`
- Note path within the vault via `--obsidian-note`

Note: Notion and Obsidian sources are read-only from Claude's perspective — the session-start hook and `/notes` can read from them, but `/recap` always writes to the sync repo. To use Notion or Obsidian as the write target, manage those notes manually and point Claude at them for reading.

### Path encoding

Claude Code stores project configs at `~/.claude/projects/<encoded-path>/` where the path is encoded by replacing `/` with `-`:

```
/Users/alice/projects/my-app  →  -Users-alice-projects-my-app
/home/alice/projects/my-app   →  -home-alice-projects-my-app
```

The sync script computes this encoding automatically from the registered project path.

### Settings merge

On pull, the script merges `hooks`, `env`, and `enabledPlugins` from the repo into local settings. **Permissions are never overwritten** — they're machine-specific.

### Project registration

Each machine has a `.local-config.json` (gitignored) mapping project names to local config:

```json
{
  "projects": {
    "my-app": {
      "path": "/Users/alice/projects/my-app",
      "remote": "git@github.com:org/my-app.git",
      "notes_source": "sync"
    }
  }
}
```

On another machine, the same project with a different local path:

```json
{
  "projects": {
    "my-app": {
      "path": "/home/alice/projects/my-app",
      "remote": "git@github.com:org/my-app.git",
      "notes_source": "sync"
    }
  }
}
```

Both sync to `projects/my-app/` in the repo — same memories, same branch notes.

---

## Repo Structure

```
├── global/
│   ├── memory/               # Your global memories (builds up over time)
│   ├── commands/             # Slash commands (10 included)
│   ├── CLAUDE.md             # Global instructions
│   └── settings.json         # Settings template with hooks
├── projects/
│   └── <project-name>/
│       ├── memory/           # Project-scoped memories
│       └── branches/
│           └── <branch>/
│               └── notes.md  # Branch-level session notes
├── scripts/
│   └── claude-sync           # The sync engine
├── .gitignore
└── README.md
```

---

## Troubleshooting

### Sync fails on session start

The agent hook reports the error without blocking your session. Common causes:

| Issue | Fix |
|-------|-----|
| No network | Connect and run `claude-sync pull` manually |
| Merge conflict | `cd ~/claude-sync && git pull --rebase`, then `claude-sync push` |
| Repo not cloned | Re-clone and run `claude-sync setup` |

### Commands not available

```bash
ls ~/.claude/commands/
```

If empty, run `claude-sync setup` to reinstall them.

### Project memories not syncing

Verify the project is registered:

```bash
cat ~/claude-sync/.local-config.json
```

If missing, register it:

```bash
claude-sync add-project my-app /path/to/my-app
```

### Full reset

```bash
rm -rf ~/claude-sync
git clone https://github.com/YOUR_USERNAME/YOUR_REPO_NAME ~/claude-sync
~/claude-sync/scripts/claude-sync setup
# Re-register your projects
```

### Run a health check

```bash
claude-sync doctor
```

---

## Contributing

This is a template — fork it, customize it, make it yours. If you find improvements that would benefit everyone, PRs are welcome.

---

## License

MIT
