---
name: claude-md-updater
description: |
  Update CLAUDE.md, AGENTS.md, and GEMINI.md files. Two modes available:
  1. Diff-based: Analyze git diff between two commits to update documentation incrementally
  2. Content-based: Analyze current file contents to generate documentation from scratch

  Use when:
  - Asked to update documentation based on recent changes (diff-based)
  - Asked to regenerate or create documentation from current codebase state (content-based)
  - Starting commit is lost or unknown (content-based)
  - Diff between commits is too large (content-based)
---
# Claude MD Updater

This skill guides you through keeping `CLAUDE.md`, `AGENTS.md`, and `GEMINI.md` files synchronized with your codebase.

## Mode Selection

Before starting, determine the update mode:

| Scenario | Mode |
|----------|------|
| User provides two commits/branches | Diff-based |
| User says "regenerate" or "from scratch" | Content-based |
| Starting commit lost/unknown | Content-based |
| Diff too large (>1000 files or >50k lines) | Content-based |

---

**For Diff-based mode**: Continue with the workflow below.

**For Content-based mode**: Skip to [Content-Based Update Workflow](references/content-based-workflow.md).

## Diff-Based Workflow

Follow these steps when comparing two specific git commits:

### 1. Analyze Changes

1. Get changed files between commits:
   ```bash
   git diff --name-status <commit1> <commit2>
   ```
2. Fetch actual diffs:
   ```bash
   git diff <commit1> <commit2>
   ```
   If diff is too large, diff specific files or directories.

3. Identify architectural, structural, or stylistic changes. Ignore trivial changes like typo fixes.

### 2. Locate Existing Documentation

1. Find existing context files:
   ```bash
   glob **/CLAUDE.md
   glob **/AGENTS.md
   glob **/GEMINI.md
   ```

2. Read current contents to understand existing instructions.

### 3. Update Strategy

Determine what needs to change:

* **Additions**: New instructions, architectural decisions, or rules from the commits.
* **Updates**: Modified or evolved instructions.
* **Deletions**: Stale or contradicted information.
* **File Creation/Deletion**: Create docs for new subsystems; remove docs for deleted subsystems.

**Constraints:**
* **Preserve Useful Info**: Don't delete unless contradicted or obsolete.
* **Remove Stale Info**: Prune outdated rules actively.
* **Progressive Disclosure**: Break down long files. Move specific instructions to subdirectory files.

### 4. Apply Changes

1. Use `write_file` for new files or overwrites.
2. Use `replace` for targeted edits.
3. Keep formatting clean and concise.

### 5. Final Review

1. Review modified files for appropriate tone (concise, high-signal).
2. Present summary of changes to user.

## References

- [Content-Based Update Workflow](references/content-based-workflow.md) - For regenerating documentation from current codebase
- [Decomposition Guidelines](references/decomposition-guidelines.md) - When and how to split long CLAUDE.md files
