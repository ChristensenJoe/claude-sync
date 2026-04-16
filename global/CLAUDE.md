# Global Claude Code Instructions

Cross-project instructions that apply to every session, regardless of working directory.
Customize this file to match your workflow — everything here is loaded automatically.

---

## Post-Task Reporting

After completing any task, provide a **## Changes Overview** section covering:

- **File-by-file summary** — every file touched, using this format:
  - **`path/to/file`** (new|modified) — one-line purpose summary
    - Bullet points covering key implementation details, data flow, and notable choices
    - ~3-8 bullets per file depending on complexity
    - Lead with the "what", follow with the "how" and "why" for non-obvious choices
- **Design decisions** — architectural choices made, alternatives considered, and why
- **Assumptions** — anything assumed that wasn't explicitly specified
- **Intentionally unchanged** — anything considered but deliberately left as-is, and why
- **New patterns or dependencies** — any new conventions, utilities, or dependencies introduced
- **Risks and tradeoffs** — potential issues or tradeoffs to be aware of

Keep it scannable — short bullets, not paragraphs. **Threshold:** provide this for any task that touches 2+ files or involves non-trivial decisions. Skip for single-file edits and short one-off questions.
