# Repo Audit

Audit all files changed on the current branch against the project's rules and
conventions. Produce a prioritized report of violations, then optionally fix them.

## Instructions

### 1. Discover project rules

Run these in parallel:
- Check if `CLAUDE.md` exists in the project root and read it
- Glob for all `.claude/rules/*.md` files and read them
- If no CLAUDE.md and no rules exist, inform the user that there are no documented
  conventions to audit against and ask if they'd like to create some

Collect all discovered rules as the audit baseline.

### 2. Gather branch context

Run these in parallel:

```bash
git diff main...HEAD --name-only      # committed changes on branch
git diff --cached --name-only         # staged but uncommitted changes
git diff main...HEAD --stat           # change summary
git log main...HEAD --oneline         # commits on branch
```

Combine both committed and staged file lists (deduplicated) as the full set of
files to audit. When reading file contents, always read the working-tree version
(not the committed version) so staged edits are included.

### 3. Categorize changed files

Group changed files by their top-level directory (e.g., `src/`, `lib/`, `tests/`,
`packages/`, etc.). Auto-detect categories from the actual file paths — do NOT
use hardcoded categories.

Separate test files (matching `*.test.*`, `*.spec.*`, `__tests__/*`) into their
own category regardless of directory.

### 4. Spawn parallel audit agents

Launch up to 4 `Explore` agents concurrently, one per file category (combine
small categories). Each agent must:

- Read all changed files in its category
- Read ALL discovered rules from step 1
- Check each file against the rules for violations
- Note: only flag violations of rules that are actually documented — do not
  invent rules that aren't written down

### 5. Compile the report

Merge agent findings into a single prioritized report:

```
## Repo Audit Report

### Critical Issues
(numbered list — things that will break at runtime or violate hard rules)

### Architecture Violations
(numbered list — structural issues: wrong file placement, import violations, etc.)

### Style Violations
(numbered list — convention mismatches, naming issues, etc.)

### Clean Areas
(bullet list — things that passed audit, so the user sees coverage)

### Priority Table
| Priority | Issue | File(s) | Action |
```

### 6. Offer to fix

After presenting the report, ask: "Want me to fix any of these?"

If the user says yes (all or specific items), apply fixes and run the project's
test command if one is documented in CLAUDE.md or package.json.
