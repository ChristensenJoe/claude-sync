# Initialize Project Rules

Bootstrap a CLAUDE.md and `.claude/rules/` for the current project by scanning the
codebase. Use this when a project has no Claude configuration yet and you want to
give Claude context about the project's patterns, conventions, and architecture.

## Instructions

### 1. Detect the project

Gather basic project info in parallel:

```bash
# Check for project metadata files
ls package.json Cargo.toml pyproject.toml go.mod Gemfile build.gradle pom.xml *.csproj Makefile 2>/dev/null

# Check git info
git remote get-url origin 2>/dev/null
git log --oneline -20

# Check existing Claude config
ls CLAUDE.md .claude/rules/*.md 2>/dev/null
```

If a `CLAUDE.md` already exists, inform the user and ask if they want to enhance it
or start fresh. Do not overwrite without confirmation.

### 2. Scan the codebase

Launch up to 3 `Explore` agents to understand the project:

**Agent 1 — Structure & stack:**
- Read project config files (package.json, tsconfig, etc.)
- Map the top-level directory structure
- Identify the language, framework, build tools, test framework
- Identify the package manager and key scripts

**Agent 2 — Patterns & conventions:**
- Read 10-15 representative source files across the project
- Identify import patterns, naming conventions, file organization
- Look for shared utilities, hooks, helpers, or common abstractions
- Note any consistent patterns (e.g., container/view split, barrel exports)

**Agent 3 — Testing & CI:**
- Read test files to understand testing conventions
- Check for CI config (.github/workflows/, .gitlab-ci.yml, etc.)
- Identify test runners, assertion libraries, mock patterns
- Note any custom test utilities or setup files

### 3. Draft CLAUDE.md

Create a `CLAUDE.md` at the project root with:

- **Project overview** — one paragraph: what it is, the stack, key dependencies
- **File structure** — annotated tree of the important directories
- **Build & run** — commands for install, dev, build, test, lint
- **Architecture** — how the codebase is organized, key patterns
- **Import conventions** — how imports work, any aliases or restrictions
- **Key files** — the most important files a developer should know about
- **Golden rules** — 3-5 critical conventions derived from the codebase patterns

### 4. Draft rule files

If the project has enough complexity, create `.claude/rules/` files for:

- **coding-conventions.md** — naming, style patterns, common pitfalls
- **testing.md** — test structure, mock patterns, test utilities
- Additional topic-specific rules only if clearly warranted

Keep rules minimal and high-signal. A few well-chosen rules are better than an
exhaustive list. Only document patterns that are:
- Non-obvious (not derivable by just reading the code)
- Consistently followed across the project
- Important enough that violating them would cause issues

### 5. Present for approval

Show the user:
- The proposed CLAUDE.md content
- Any proposed rule files
- What you chose NOT to document and why

Ask: **"Does this look right? Any changes before I write these files?"**

Only write files after the user confirms.

### 6. Suggest next steps

After writing:
- "Run `/repo-audit` on your next branch to see these rules in action"
- "Run `/update-patterns` after your next feature to grow the docs"
- "Run `/learn` at the end of sessions to capture decisions"
