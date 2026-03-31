# claude-md-updater for Gemini CLI

A Gemini CLI extension that updates `CLAUDE.md`, `AGENTS.md`, and `GEMINI.md` files based on git commit diffs.

## Installation

### From Git Repository

```bash
gemini extensions add github:sg-c/claude-md-updater gemini-extension
```

### Manual Installation

1. Clone this repository
2. Link the extension:
   ```bash
   cd gemini-extension
   gemini extensions link .
   ```

## Usage

The skill activates when you ask to update documentation based on code changes:

- "Update CLAUDE.md from commits abc123 to def456"
- "Sync docs with changes between v1.0 and v2.0"
- "What changed between HEAD~10 and HEAD, and what docs need updating?"

## Features

- **Diff-driven updates**: Analyzes actual code changes between two commits
- **Multi-file support**: Works with CLAUDE.md, AGENTS.md, and GEMINI.md
- **Smart preservation**: Keeps useful info, removes stale content
- **Decomposition**: Suggests breaking down long files into subdirectory files

## Structure

```
gemini-extension/
├── gemini-extension.json    # Extension manifest
├── GEMINI.md                # Context file (loaded at session start)
└── skills/
    └── claude-md-updater/
        ├── SKILL.md         # Main skill instructions
        └── references/
            └── decomposition-guidelines.md
```
