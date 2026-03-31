# Claude MD Updater

This extension helps keep project documentation (CLAUDE.md, AGENTS.md, GEMINI.md) synchronized with codebase changes.

## When to Use

Activate the `claude-md-updater` skill when:
- User asks to update documentation based on recent code changes
- User wants to sync docs between two commits
- User mentions "update CLAUDE.md from commits" or "sync docs with changes"

## Quick Start

1. Ask user for two commit hashes (or references like `HEAD~5` and `HEAD`)
2. Run `git diff --name-status <commit1> <commit2>` to see changed files
3. Analyze which changes affect documentation
4. Propose targeted updates (add/modify/remove)
5. Apply with user approval

## Key Principles

- **Preserve useful info** - Don't delete unless contradicted by changes
- **Remove stale info** - Actively prune outdated references
- **Progressive disclosure** - Break down long CLAUDE.md into subdirectory files
- **Be concise** - High-signal, no verbose explanations

See the `claude-md-updater` skill for detailed workflow.
