---
name: claude-md-updater
description: Update, manage, and maintain CLAUDE.md (and AGENTS.md/GEMINI.md) files based on git history and codebase changes between two commits. Use when asked to update documentation based on recent changes or analyze a diff to update AI agent instructions.
---
# Claude MD Updater

This skill guides you through the process of keeping `CLAUDE.md`, `AGENTS.md`, and `GEMINI.md` files up to date with codebase changes between two specific git commits.

## Prerequisites

If the user has not specified the two git commit hashes (or branch names/tags) to compare, you MUST ask the user to provide them before proceeding.

## Workflow

Follow these steps exactly to update the context documentation:

### 1. Analyze Changes

1.  Use the `run_shell_command` tool to get the list of changed files between the two commits:
    ```bash
    git diff --name-status <commit1> <commit2>
    ```
    Present this high-level list of changed files to the user if they requested to see it.
2.  For a deeper understanding of the changes, fetch the actual diffs using `run_shell_command`:
    ```bash
    git diff <commit1> <commit2>
    ```
    If the diff is too large, consider diffing specific important files or directories instead of the entire project to stay within your context limits.
3.  Identify the core architectural, structural, or stylistic changes made in these commits. Focus on new patterns, deprecated features, new dependencies, or updated instructions that should be documented. Ignore trivial changes like typo fixes.

### 2. Locate Existing Documentation

1.  Use the `glob` tool to find existing `CLAUDE.md`, `AGENTS.md`, and `GEMINI.md` files across the workspace (including subdirectories).
2.  Read the current contents of the relevant context files using the `read_file` tool to understand the existing instructions.

### 3. Update Strategy

Determine what needs to be added, updated, or deleted based on your analysis of the codebase changes:

*   **Additions**: Add new instructions, architectural decisions, or stylistic rules introduced in the commits.
*   **Updates**: Modify existing instructions that are no longer accurate or have evolved.
*   **Deletions**: Remove stale information that contradicts the new code, or is no longer relevant.
*   **File Creation/Deletion**: If a new significant subsystem was added, consider creating a specific `CLAUDE.md` in its subdirectory. If a subsystem was deleted, remove its corresponding documentation.

**Crucial Constraints:**
*   **Preserve Useful Info**: Do NOT delete existing information unless it is explicitly contradicted or made obsolete by the recent changes. Preserve the context for stable parts of the system.
*   **Remove Stale Info**: Actively prune outdated rules. Stale AI context is dangerous.
*   **Progressive Disclosure**: Actively break down long, top-level `CLAUDE.md` files. Distribute specific, localized instructions into lower-level `CLAUDE.md` files within the relevant subdirectories. Keep the root `CLAUDE.md` strictly for high-level, global principles.

### 4. Apply Changes

1.  Use the `write_file` tool to create new files or overwrite existing ones.
2.  Use the `replace` tool for targeted, surgical edits to existing files if the changes are minor.
3.  Ensure the formatting remains clean, concise, and uses Markdown effectively.

### 5. Final Review

1.  Briefly review the modified files to ensure the tone is appropriate (concise, high-signal, no chitchat).
2.  Present a brief summary of the documentation changes to the user, highlighting what was added, removed, or moved to a lower level.

## Decomposition Guidelines

When a CLAUDE.md file exceeds ~500 lines or covers multiple domains, suggest breaking it into subdirectory-level files. See [references/decomposition-guidelines.md](references/decomposition-guidelines.md) for detailed guidance on:
- Signs that decomposition is needed
- Identifying logical boundaries
- What goes in root vs subdirectory files
- Migration process and anti-patterns to avoid
