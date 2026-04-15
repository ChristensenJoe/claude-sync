# claude-sync

Sync your [Claude Code](https://docs.anthropic.com/en/docs/claude-code) configuration across devices. Back up your memories, commands, settings, and project knowledge to a private Git repo — then pull them down on any machine.

**This is a template.** Click **"Use this template"** to create your own private copy, then follow the setup below.

---

## Why

Claude Code gets smarter the more you use it — but only on the machine you're using. Your corrections, preferences, project knowledge, and custom workflows live in `~/.claude/` and vanish when you switch devices.

**claude-sync** fixes that. It gives you:

- **Portable configuration** — memories, commands, settings, and project knowledge sync to a Git repo and pull down on any machine
- **Session-start hooks** — every session auto-pulls the latest config and checks your TODO list
- **Professional workflows** — 8 slash commands for auditing, pattern detection, session management, and knowledge capture
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

**The `/save-notes` command is the engine.** At the end of each session, it reviews your conversation and routes every finding to the right layer. Corrections become memories. Patterns become rules. Decisions become context. Nothing is lost.

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
- Install two session-start hooks (TODO.md check + config sync)
- Merge template settings into your existing `~/.claude/settings.json`

### 4. Register your projects

For each project whose Claude memories you want to sync:

```bash
~/claude-sync/scripts/claude-sync add-project my-app /path/to/my-app
```

This maps the project's local path to a canonical name in the repo. On another machine, register the same project with its local path there.

### 5. Push your initial config

```bash
~/claude-sync/scripts/claude-sync push
```

### 6. Verify

```bash
~/claude-sync/scripts/claude-sync doctor
```

---

## Using claude-sync

### What happens automatically

Every Claude Code session triggers two hooks — no action required:

**TODO.md context** — If a `TODO.md` exists in your working directory, Claude reads it and summarizes where you left off. No re-explaining what you were doing. No "let me catch you up." You sit down, open Claude, and your context is already there.

**Config sync** — Pulls the latest configuration from your repo. If you pushed changes from another machine, you'll see what's new. If the pull fails (no network, merge conflict), Claude tells you what went wrong so you can fix it — but never blocks your session.

### A typical coding session

The commands below are designed to bookend your work. At the start of a session, the hooks handle context automatically. At the end, a short ritual captures everything valuable before you close the terminal.

**Start of session** — automatic. The hooks fire, you see your TODO summary and sync status. Start working.

**During the session** — just code. The commands stay out of your way while you work.

**End of session** — this is where the value compounds. Run `/save-notes` to capture any corrections, decisions, or patterns from the session into the right memory layer. Then run `/repo-audit` to verify your branch is clean against the project's rules before you step away. Together, these two commands take a minute and pay for themselves many times over — `/save-notes` makes the next session smarter, and `/repo-audit` catches issues while the context is still fresh rather than surfacing them in code review the next day.

For longer sessions or when you're switching between tasks, `/recap` is a natural stopping point. It writes a TODO.md summarizing where things stand, which Claude reads back to you next time. The loop closes itself.

### CLI commands

```bash
# Push local config to your repo
~/claude-sync/scripts/claude-sync push

# Check what's different between local and repo
~/claude-sync/scripts/claude-sync status

# Register a new project for memory syncing
~/claude-sync/scripts/claude-sync add-project my-app /path/to/my-app

# Health check
~/claude-sync/scripts/claude-sync doctor
```

### Adding another machine

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO_NAME ~/claude-sync
~/claude-sync/scripts/claude-sync setup
~/claude-sync/scripts/claude-sync add-project my-app /home/you/projects/my-app
```

Your global memories, commands, settings, and project memories are all there. Sign into any Claude account — the config is account-agnostic.

---

## Slash Commands

All commands are installed globally and available in every Claude Code session.

### Session workflow

These commands are the core of the daily workflow — capturing knowledge and wrapping up sessions.

#### `/save-notes` — Session Knowledge Capture

The engine of the knowledge flywheel. Reviews your conversation and routes every finding to the right persistence layer:

- **Repo rules** — Invokes `/update-patterns` to document conventions in `CLAUDE.md` and `.claude/rules/` (creates them even in projects that have none yet)
- **Project memory** — Saves project-specific context: active work status, codebase gotchas, corrections, external references
- **Global memory** — Saves cross-project preferences: collaboration style, coding philosophy, behavioral corrections

Run this at the end of any session where Claude learned something — whether that's a correction you gave, a decision you made, or a pattern you established. This is what makes the next session better than the last one.

#### `/recap` — Session Recap & TODO Update

Summarize what was accomplished and update `TODO.md` for the next session.

- Reviews the session for completed work, in-progress items, and blockers
- Creates or updates `TODO.md` in the current working directory
- Adds `TODO.md` to `.gitignore` if one exists (keeps it out of version control)
- Invokes `/save-notes` to persist decisions and knowledge

The TODO.md it writes is exactly what the session-start hook reads back to you next time — forming a complete session continuity loop.

#### `/sync` — Manual Config Push

Push your current configuration to the cloud repo. Run this after `/save-notes` if you want the captured knowledge available on another machine immediately, or let the next session's auto-pull handle it.

### Code quality

These commands help you maintain standards — auditing your branch against documented conventions and growing those conventions over time.

#### `/repo-audit` — Branch Audit

Audit all changed files on your current branch against the project's documented rules.

- Dynamically discovers `CLAUDE.md` and `.claude/rules/*.md`
- Groups changed files by directory and spawns parallel audit agents
- Produces a prioritized report: Critical → Architecture → Style → Clean Areas
- Offers to fix violations

Run this before opening a PR, after a large refactor, or at the end of a session to catch issues while the context is fresh.

#### `/update-patterns` — Pattern Detection

Analyze your branch for new patterns or conventions that aren't documented yet.

- Compares changed files against existing rules
- Identifies patterns worth codifying (architecture, naming, data, testing, etc.)
- Proposes new rules with specific text and destination files
- Creates `CLAUDE.md` and `.claude/rules/` if they don't exist yet

Run this after implementing a feature that establishes a new pattern, or during code review when you notice undocumented conventions. Note: `/save-notes` invokes this automatically, so you often don't need to run it directly.

### Project onboarding

#### `/init-rules` — Bootstrap Project Rules

Scan a project's codebase and generate an initial `CLAUDE.md` and `.claude/rules/` with detected patterns, conventions, and architecture.

- Detects language, framework, and tooling from config files
- Reads representative source files to identify patterns
- Drafts a CLAUDE.md with project overview, file structure, build commands, and golden rules
- Presents everything for approval before writing

Run this when starting to use Claude in a project that has no Claude configuration, or when onboarding a team to Claude Code. You only need to run it once per project — after that, `/update-patterns` and `/save-notes` grow the documentation incrementally.

### Maintenance

#### `/doctor` — Health Check

Diagnose common setup issues: missing dependencies, broken hooks, unregistered projects, stale config.

Run this after initial setup, when sync seems broken, or on a new machine to verify everything is wired up.

#### `/memory-audit` — Memory Hygiene

Audit your Claude memory system for redundancy, staleness, and bloat.

- Finds duplicate or overlapping memories across layers
- Flags stale entries about completed work or deleted code
- Catches memories stored in the wrong layer (global vs project vs rules)
- Identifies verbose entries that could be trimmed
- Detects MEMORY.md index drift (orphaned entries, missing links)
- Offers to fix all issues with permission

Run this weekly to monthly. Every memory file is loaded into Claude's context window, so redundant or stale memories waste tokens and can cause conflicting instructions. Think of it as garbage collection for your AI knowledge base.

---

## How It Works

### Path encoding

Claude Code stores project configs at `~/.claude/projects/<encoded-path>/` where the path is encoded by replacing `/` with `-`:

```
/Users/alice/projects/my-app  →  -Users-alice-projects-my-app
/home/alice/projects/my-app   →  -home-alice-projects-my-app
```

The sync script maps these to canonical project names via `.local-config.json` (gitignored, machine-specific).

### Settings merge

On pull, the script merges `hooks`, `env`, and `enabledPlugins` from the repo into local settings. **Permissions are never overwritten** — they're machine-specific.

### Project registration

Each machine has a `.local-config.json` mapping canonical names to local paths:

```json
{
  "projects": {
    "my-app": "/Users/alice/projects/my-app"
  }
}
```

On another machine:

```json
{
  "projects": {
    "my-app": "/home/alice/projects/my-app"
  }
}
```

Both sync to `projects/my-app/memory/` in the repo.

---

## Repo Structure

```
├── global/
│   ├── memory/           # Your global memories (builds up over time)
│   ├── commands/         # Slash commands (8 included)
│   ├── CLAUDE.md         # Global instructions
│   └── settings.json     # Settings template with hooks
├── projects/
│   └── <project-name>/
│       └── memory/       # Project-scoped memories
├── scripts/
│   └── claude-sync       # The sync engine
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
~/claude-sync/scripts/claude-sync add-project my-app /path/to/my-app
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
~/claude-sync/scripts/claude-sync doctor
```

---

## Contributing

This is a template — fork it, customize it, make it yours. If you find improvements that would benefit everyone, PRs are welcome.

---

## License

MIT
