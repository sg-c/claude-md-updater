# claude-md-updater

A Claude Code plugin that updates `CLAUDE.md`, `AGENTS.md`, and `GEMINI.md` files based on git commit diffs.

## Installation

```bash
claude plugins install github:sg-c/claude-md-updater
```

Or manually:
1. Clone this repo to your Claude plugins directory
2. Restart Claude Code

## Usage

The skill triggers when you ask to update documentation based on code changes:

- "Update CLAUDE.md from commits abc123 to def456"
- "Sync docs with changes between v1.0 and v2.0"
- "What changed between HEAD~10 and HEAD, and what docs need updating?"

## Features

- **Diff-driven updates**: Analyzes actual code changes between two commits
- **Multi-file support**: Works with CLAUDE.md, AGENTS.md, and GEMINI.md
- **Smart preservation**: Keeps useful info, removes stale content
- **Decomposition**: Suggests breaking down long CLAUDE.md files into subdirectory files

## Workflow

1. Specify two commit hashes (or tags/branches)
2. See list of changed files
3. Analyze which changes affect documentation
4. Propose targeted updates (add/modify/remove)
5. Apply with approval

## License

MIT
