# Learn

Review the current conversation and persist important findings to the right
persistence layer. This is the engine of the knowledge flywheel — every `/learn`
makes the next session smarter.

## Where things go

Each finding goes to exactly ONE of these layers based on scope:

### 1. Repo rules / CLAUDE.md — handled by `/update-patterns`
Things that any developer (human or AI) working in this repo should follow:
architectural patterns, naming conventions, coding standards, API conventions,
new shared infrastructure.

**Do not update rules/CLAUDE.md directly.** This skill automatically invokes
`/update-patterns` at the end to handle layer 1 findings. That skill will create
CLAUDE.md and `.claude/rules/` if they don't exist yet — building up project
documentation over time is the goal.

*Example: "mutation results must match cached query fields" → handled by `/update-patterns`*

### 2. Project memory — project-specific but user-specific
Things about this user's work in this project that wouldn't belong in repo docs.

- Active work status, branch context, what's done vs remaining
- User corrections to AI approach that are specific to this project
- Project-specific references (Linear boards, Slack channels, dashboards)

**Where:** The project memory directory (provided in your system context as the
auto-memory path) with `MEMORY.md` index.

*Example: "Bundles drawer DnD is implemented, mock data remaining" → project memory*

### 3. Global memory — user-specific, not project-specific
Preferences and info about the user that apply across all projects.

- Collaboration style, communication preferences
- Role, expertise, general workflow preferences
- Corrections that aren't project-specific

**Where:** The global memory directory at `~/.claude/memory/` with `MEMORY.md` index.

*Example: "User prefers HOCs when the pattern fits" → global memory*

## Decision guide

Ask yourself for each finding:

1. **Would another developer in this repo benefit from knowing this?** → Handled by `/update-patterns` (auto-invoked)
2. **Is it about this user's work or preferences within this project?** → Project memory
3. **Is it about the user regardless of project?** → Global memory

**When in doubt:** Ask the user. Present the finding and the two locations you're
considering, and let them choose.

## Instructions

1. Review the full conversation for:
   - **Project decisions** — architectural choices, naming conventions, data model decisions
   - **Feedback** — corrections to your approach, confirmed preferences, things to repeat or avoid
   - **User info** — role, expertise, collaboration style (only if new or updated)
   - **References** — external resources, dashboards, tracking systems mentioned

2. For each finding, determine which layer it belongs to (see decision guide above).
   If you're unsure between two layers, ask the user before saving.

3. For **project memory** updates:
   - Check `MEMORY.md` in the project memory directory for duplicates
   - Write each memory as its own file with standard frontmatter:
     ```markdown
     ---
     name: {{memory name}}
     description: {{one-line description}}
     type: {{user, feedback, project, reference}}
     ---
     {{content — for feedback/project: rule/fact, then **Why:** and **How to apply:** lines}}
     ```
   - Update `MEMORY.md` index with a one-line entry

4. For **global memory** updates:
   - Check `~/.claude/memory/MEMORY.md` for duplicates
   - Same file format as project memory
   - Update the global `MEMORY.md` index

5. Present a summary to the user of what was saved to memory (layers 2 and 3),
   so they can confirm or adjust.

6. **Always invoke `/update-patterns`** via the Skill tool to handle layer 1
   (repo rules/CLAUDE.md). This audits the branch for new patterns and updates
   repo docs. If no CLAUDE.md or rules exist yet, `/update-patterns` will create
   them if warranted — this is intentional. Building up project documentation
   over time is the goal.

## What NOT to save

- Duplicates of what's already in the target location (check first)
- Ephemeral task state (use tasks for that)
- Git history or recent changes (use `git log` for that)
- Code patterns derivable from reading the codebase (unless they encode a non-obvious decision)

## Guidelines

- Keep everything concise and actionable
- Use absolute dates, not relative ("2026-03-26" not "today")
- For feedback/project memories, include **Why:** and **How to apply:**
- Organize by topic, not chronologically
