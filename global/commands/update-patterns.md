# Update Patterns from Branch

Analyze the current branch's changes for new patterns, conventions, or architectural
decisions that aren't yet captured in CLAUDE.md or `.claude/rules/`. Present findings
to the user and update documentation with their approval.

## Instructions

### 1. Discover existing rules

Run these in parallel:
- Check if `CLAUDE.md` exists and read it
- Glob for all `.claude/rules/*.md` files and read them

If no rules exist yet, that's fine — this skill can bootstrap project documentation
from scratch. Note which rules already exist so you don't propose duplicates.

### 2. Gather branch context

Run these in parallel:

```bash
git diff main...HEAD --name-only      # changed files
git diff main...HEAD --stat           # change summary
git log main...HEAD --oneline         # commits on branch
```

### 3. Spawn analysis agents

Launch up to 2 `Explore` agents concurrently. Each agent reads the full contents
of all changed files in its category AND any existing rules.

**Agent 1 — New patterns in app code** (all non-test changed files):
- Read all changed non-test files
- Look for the pattern categories listed below
- For each candidate pattern, note: what the pattern is, where it appears, and
  why it might be worth documenting

**Agent 2 — New patterns in tests** (all test files):
- Read all changed test files
- Look for new testing patterns, utilities, or conventions
- For each candidate, note: what it is, where it appears, and why it
  might be worth documenting

### 4. Evaluate candidates

For each candidate pattern found by the agents, determine:

1. **Is it already documented?** — Check against existing rules. Skip if covered.
2. **Is it a one-off or a pattern?** — Only propose patterns that appear in 2+ files
   or represent an intentional architectural decision (even if in 1 file).
3. **Is it specific enough to be actionable?** — Vague observations aren't useful.
4. **Where does it belong?** — Classify into the right destination:
   - `CLAUDE.md` — high-level rules, decision matrices, golden rules
   - Existing `.claude/rules/<topic>.md` — if it fits an existing rule file's scope
   - New `.claude/rules/<topic>.md` — if it opens a genuinely new category

### 5. Present findings

Format as a numbered report:

```
## New Patterns Detected

### 1. [Pattern Name]
**What:** Brief description of the pattern
**Where:** File(s) where it appears
**Example:**
\`\`\`code snippet showing the pattern\`\`\`
**Proposed rule:** The actual text to add
**Destination:** Which file to add it to

### 2. [Pattern Name]
...

---

### No Action Needed
(list anything reviewed but already covered or too one-off to document)
```

If no new patterns are found, say so clearly and explain what was checked.

### 6. Apply with permission

After presenting findings, ask: **"Which of these should I add to the rules? (all / numbers / none)"**

- Wait for the user's response before making any changes
- For approved items, apply the changes to the appropriate rule files
- Create new files (CLAUDE.md, .claude/rules/) if they don't exist yet
- Show a diff summary of what was added after applying

---

## Pattern Categories to Look For

### Architecture & structure
- New folder conventions or organizational patterns
- New module boundary rules (what imports what)
- New barrel export patterns or exceptions
- New page/route patterns

### Component patterns
- New component conventions (props API, composition)
- Reusable layout or wrapper patterns

### Data & state
- New data fetching patterns (caching, optimistic updates, polling)
- New hook composition patterns
- State management conventions
- API integration patterns

### Routing & navigation
- URL structure conventions
- Navigation patterns
- Query parameter conventions

### Styling
- New styling patterns or conventions
- Theme token usage patterns

### Testing
- New mock patterns or test utilities
- Test structure conventions
- Custom render wrappers or providers

### Naming
- New naming conventions for files, components, hooks, constants
- Terminology changes

---

## Things to NOT propose

- Patterns that are clearly just one developer's personal style
- Extremely obvious conventions (e.g., "use const for constants")
- Patterns already enforced by linters or formatters
- Implementation details that aren't generalizable
- Temporary workarounds or TODOs
