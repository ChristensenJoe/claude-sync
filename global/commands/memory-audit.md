# Memory Audit

Audit your Claude Code memory system for redundancy, staleness, and bloat. Run this
periodically (weekly to monthly) to keep your memories lean and your token usage low.

## Why this matters

Every memory file is loaded into Claude's context window at the start of relevant
sessions. Redundant, stale, or overly verbose memories waste tokens and can cause
conflicting instructions. A clean memory system means faster sessions and sharper
responses.

## Instructions

### 1. Gather all memory files

Read everything in parallel:

**Global memory:**
- Read `~/.claude/memory/MEMORY.md` (the index)
- Read every `.md` file in `~/.claude/memory/`

**Global CLAUDE.md:**
- Read `~/.claude/CLAUDE.md` if it exists

**Project memory (current project):**
- Read the project memory `MEMORY.md` index (from your system context auto-memory path)
- Read every `.md` file in that project memory directory

### 2. Check for issues

Analyze all collected files for these categories of problems:

**Duplicates & redundancy:**
- Two or more memory files that say the same thing in different words
- A memory that restates something already in CLAUDE.md or `.claude/rules/`
- A global memory that duplicates a project memory (or vice versa)
- MEMORY.md index entries that are near-duplicates of each other

**Stale entries:**
- Project memories about work that appears to be completed (check git log if useful)
- Memories referencing files, functions, or patterns that no longer exist
- Memories with dates that are months old and describe temporary state
- "In progress" or "active work" entries that haven't been updated recently

**Wrong layer:**
- Project-specific rules stored in global memory (should be project memory)
- Cross-project preferences stored in project memory (should be global)
- Instructions stored as memories that belong in CLAUDE.md (or vice versa)
- Conventions stored in memory that should be in `.claude/rules/`

**Bloat & verbosity:**
- Memories that are excessively long when a shorter version would suffice
- Memories with unnecessary preamble or context that doesn't aid recall
- **Why:** or **How to apply:** sections that just restate the rule itself

**Index drift:**
- MEMORY.md entries pointing to files that don't exist
- Memory files that exist but aren't listed in MEMORY.md
- MEMORY.md entries whose descriptions don't match the file's actual content

### 3. Present the report

Format findings clearly:

```
## Memory Audit Report

### Summary
- X global memories, Y project memories, Z total tokens estimated
- N issues found

### Duplicates & Redundancy
(numbered list — which files overlap and what to consolidate)

### Stale Entries
(numbered list — what looks outdated and why)

### Wrong Layer
(numbered list — what should move where)

### Bloat
(numbered list — what could be trimmed and how)

### Index Issues
(numbered list — missing or orphaned entries)

### Clean
(bullet list — files that passed audit)
```

### 4. Fix with permission

After presenting the report, ask: **"Which of these should I fix? (all / numbers / none)"**

For approved items:
- **Duplicates:** Merge into the better-written version, delete the other, update MEMORY.md
- **Stale:** Delete the file, remove from MEMORY.md
- **Wrong layer:** Move the file to the correct location, update both MEMORY.md indexes
- **Bloat:** Rewrite the memory more concisely, preserve the core rule/fact
- **Index drift:** Add missing entries, remove orphaned entries, fix descriptions

After fixes, show a summary of what changed and the new file count.

### 5. Suggest a sync

If any changes were made, suggest running `/sync` to push the cleaned-up state to
the config repo.
