# claude-md-updater

Update `CLAUDE.md`, `AGENTS.md`, and `GEMINI.md` files based on git commit diffs.

## Claude Code Installation

```bash
# Add as marketplace
claude plugins marketplace add sg-c/claude-md-updater

# Install the plugin
claude plugins install claude-md-updater

# Reload plugins
/reload-plugins
```

## Gemini CLI Installation

```bash
# Add from Git repository
gemini extensions add github:sg-c/claude-md-updater gemini-extension
```

Or manually:
```bash
git clone https://github.com/sg-c/claude-md-updater.git
cd claude-md-updater/gemini-extension
gemini extensions link .
```

## Usage

The skill triggers when you ask to update documentation based on code changes:

- "Update CLAUDE.md from commits abc123 to def456"
- "Sync docs with changes between v1.0 and v2.0"
- "What changed between HEAD~10 and HEAD, and what docs need updating?"

## Features

- **Diff-driven updates**: Analyzes actual code changes between two commits
- **Multi-file support**: Works with CLAUDE.md, AGENTS.md, and GEMINI.md
- **Smart preservation**: Keeps useful info, removes stale content
- **Decomposition**: Suggests breaking down long files into subdirectory files

## Repository Structure

```
claude-md-updater/
├── .claude-plugin/              # Claude Code plugin metadata
│   ├── marketplace.json
│   └── plugin.json
├── plugins/
│   └── claude-md-updater/       # Claude Code skill
│       ├── .claude-plugin/
│       └── skills/
│           └── claude-md-updater/
│               ├── SKILL.md
│               └── references/
├── gemini-extension/            # Gemini CLI extension
│   ├── gemini-extension.json
│   ├── GEMINI.md
│   └── skills/
│       └── claude-md-updater/
│           ├── SKILL.md
│           └── references/
└── README.md
```

## License

MIT
