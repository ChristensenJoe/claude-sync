# claude-sync

Sync your [Claude Code](https://docs.anthropic.com/en/docs/claude-code) configuration across devices. Back up your memories, commands, settings, and project knowledge to a private Git repo — then pull them down on any machine.

**This is a template.** Click **"Use this template"** to create your own private copy, then follow the setup below.

---

## Why

Claude Code gets smarter the more you use it — but only on the machine you're using. Your corrections, preferences, project knowledge, and custom workflows live in `~/.claude/` and vanish when you switch devices.

**claude-sync** fixes that. It gives you:

- **Portable configuration** — memories, commands, settings, and project knowledge sync to a Git repo and pull down on any machine
- **Session-start hooks** — every session auto-pulls the latest config and checks your TODO list
- **Professional workflows** — 7 slash commands for auditing, pattern detection, session management, and knowledge capture
- **Cross-platform** — works on macOS, Linux, and WSL with any Claude account

---

## The AI Knowledge Flywheel

The most powerful thing about claude-sync isn't the file syncing — it's what it enables.

Every time you work with Claude, knowledge is created: you correct an approach, confirm a pattern, make an architectural decision, discover a convention. Without persistence, that knowledge evaporates at the end of each session. Claude makes the same mistakes, asks the same questions, misses the same context — every time.

**claude-sync turns that into a flywheel.** Three layers of persistent knowledge compound over time:

### Layer 1: Global Memory — Who You Are

Your cross-project identity. How you like to work, what patterns you prefer, what mistakes Claude should never repeat. These follow you everywhere — across every project, every device, every session.

| What gets stored | Example |
|------------------|---------|
| Coding style preferences | *"Prefer ternary expressions over if/else blocks unless the logic is genuinely clearer with if."* |
| Corrections to Claude's behavior | *"Don't fabricate conventions — verify a rule exists in project docs before citing it as rationale."* |
| Design philosophy | *"Don't abstract prematurely. Three similar lines of code are better than a premature helper function."* |
| Communication preferences | *"Skip the trailing summary after each response — I can read the diff."* |

**Stored at:** `~/.claude/memory/` — synced globally, read by Claude in every session.

### Layer 2: Project Memory — What You Know

Per-project context that Claude can't derive from the code alone. This is your working knowledge of a specific codebase — the kind of tribal knowledge that takes weeks to build up and is lost when you context-switch.

| What gets stored | Example |
|------------------|---------|
| Codebase corrections | *"The `organizationId` field is auto-injected by the query hook wrapper — don't flag it as missing from variables."* |
| Active work context | *"Billing page migration is in progress — drawer component is done, form validation is remaining."* |
| Naming / terminology | *"The legacy codebase calls them 'packages' but migrated code uses 'instances' — same concept, new name."* |
| External references | *"Pipeline bugs are tracked in Linear project INGEST. The oncall dashboard is at grafana.internal/d/api-latency."* |

**Stored at:** `~/.claude/projects/<project>/memory/` — synced per-project, only loaded when working in that project.

### Layer 3: Repo Rules — What Everyone Knows

Documented patterns, conventions, and architectural decisions in `CLAUDE.md` and `.claude/rules/`. Unlike the other layers, these aren't personal — they're shared project documentation that benefits every developer (human or AI) working in the repo.

| What gets stored | Example |
|------------------|---------|
| Architecture decisions | *"Pages are thin shells (5-15 lines) that import from `features/<name>/pages/`. No business logic in route files."* |
| Import conventions | *"Never import from `@mui/material` in app code — use `@massdriver/ui` individual imports instead."* |
| Testing patterns | *"Behavioral tests preferred over snapshots. Test files co-located with source, not in `__tests__/` folders."* |
| API conventions | *"All new queries target the v1 API. Legacy v0 queries are only modified in-place, never extended."* |

**Stored at:** `CLAUDE.md` and `.claude/rules/` in the repo itself — travels with Git, not synced by claude-sync.

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

## Daily Usage

### Automatic: session-start hooks

Every Claude Code session triggers two hooks:

1. **TODO.md check** — If a `TODO.md` exists in the working directory, Claude reads it and summarizes your current tasks. This gives you instant context on where you left off.

2. **Config sync** — Pulls the latest config from your repo. If anything changed (new memories, updated commands, etc.), Claude tells you. If the pull fails (no network, conflict), Claude tells you that too — but never blocks your session.

### Manual commands

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

These commands are installed globally and available in every Claude Code session. Use them by typing the command name (e.g., `/audit`).

### `/audit` — Branch Audit

Audit all changed files on your current branch against the project's documented rules.

- Dynamically discovers `CLAUDE.md` and `.claude/rules/*.md`
- Groups changed files by directory and spawns parallel audit agents
- Produces a prioritized report: Critical → Architecture → Style → Clean Areas
- Offers to fix violations

**When to use:** Before opening a PR. After a large refactor. When onboarding to a codebase and want to verify your work matches conventions.

### `/update-patterns` — Pattern Detection

Analyze your branch for new patterns or conventions that aren't documented yet.

- Compares changed files against existing rules
- Identifies patterns worth codifying (architecture, naming, data, testing, etc.)
- Proposes new rules with specific text and destination files
- Creates `CLAUDE.md` and `.claude/rules/` if they don't exist yet

**When to use:** After implementing a feature that establishes a new pattern. During code review when you notice undocumented conventions.

### `/save-notes` — Session Knowledge Capture

The engine of the knowledge flywheel. Reviews your conversation and persists findings to the right layer.

- **Layer 1 — Repo rules:** Invokes `/update-patterns` to document conventions (creates rules even in projects that have none yet)
- **Layer 2 — Project memory:** Saves project-specific context, active work status, corrections
- **Layer 3 — Global memory:** Saves cross-project preferences, collaboration style, role info

**When to use:** At the end of every meaningful session. After receiving corrections from a code review. When you realize Claude keeps making the same mistake.

### `/sync` — Manual Config Push

Push your current configuration to the cloud repo.

**When to use:** After making changes to memories, commands, or settings that you want available on other machines immediately.

### `/doctor` — Health Check

Diagnose common setup issues: missing dependencies, broken hooks, unregistered projects.

**When to use:** After setup. When sync seems broken. On a new machine.

### `/init-rules` — Bootstrap Project Rules

Scan a project's codebase and generate an initial `CLAUDE.md` and `.claude/rules/` with detected patterns, conventions, and architecture.

**When to use:** When starting to use Claude in a project that has no Claude configuration. When onboarding a team to Claude Code.

### `/recap` — Session Recap & TODO Update

Summarize what was accomplished and update `TODO.md` for the next session.

- Reviews the session for completed work, in-progress items, blockers
- Creates or updates `TODO.md` with current status
- Invokes `/save-notes` to persist decisions and knowledge
- Pairs with the TODO.md session-start hook for a complete workflow loop

**When to use:** At the end of every work session. The TODO.md you write is what Claude reads back to you next time.

---

## Session-Start Hooks

Two hooks fire automatically when you start a Claude Code session:

### TODO.md Context Hook

If a `TODO.md` exists in your working directory, Claude reads it and gives you a summary. This creates a simple but effective session continuity workflow:

1. End of session: run `/recap` → updates TODO.md
2. Start of next session: Claude reads TODO.md → you know where you left off

No manual context loading. No "where was I?" No re-explaining the task.

### Config Sync Hook

Pulls the latest configuration from your repo. Reports what changed or any errors. Never blocks your session — if the network is down, you get a notification and continue working.

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
│   ├── commands/         # Slash commands (7 included)
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
