# Content-Based Update Workflow

Use this workflow when:
- Starting commit is lost or unknown
- Diff between commits is too large (>1000 files or >50k lines)
- User explicitly requests "regenerate" or "from scratch"

## Overview

Content-based mode analyzes current file contents to generate documentation from scratch. It uses a **bottom-up approach**: process leaf directories first, then aggregate upward level by level.

## Processing Algorithm

### Phase 1: Analyze Directory Structure

1. **Scan directory tree**:
   ```bash
   # Find all directories containing code files
   find . -type f \( -name "*.py" -o -name "*.ts" -o -name "*.js" -o -name "*.go" -o -name "*.rs" -o -name "*.java" \) | xargs -I {} dirname {} | sort -u
   ```

2. **Identify directory levels**:
   - **Leaf directories**: No subdirectories with code files
   - **Intermediate directories**: Have code files AND child directories with code
   - **Root directory**: Project root

3. **Check .gitignore**: Exclude matched paths from analysis

### Phase 2: Process Leaf Directories (Parallel)

#### Directory Batching Rules

| Directory Size | Strategy |
|----------------|----------|
| >20 code files OR >2000 LOC | Dedicated subagent |
| <=20 code files AND <=2000 LOC | Batch with nearby small directories (max 5 dirs per subagent) |
| Config-only directory | Include with nearest code directory |

#### Subagent Prompt Template

```
You are analyzing directory: {DIR_PATH}

## Context
This is part of a bottom-up CLAUDE.md generation process. You are processing a leaf directory.

## Tasks

1. **Inventory**: List all code and config files in this directory (non-recursive)

2. **Analyze Each File**:
   - Main classes/functions and their purposes
   - Key patterns (design patterns, architectural patterns)
   - Dependencies (what it imports)
   - Exports (public API)

3. **Identify Cross-File Patterns**: What patterns emerge across files in this directory?

4. **Handle Existing CLAUDE.md** (if present):
   - Read current content
   - Preserve accurate sections
   - Update partially stale sections
   - Remove completely stale sections
   - Add missing information
   - Preserve user notes (marked with "NOTE:" or similar)

5. **Generate CLAUDE.md**:
   - Purpose of this module/package
   - Key abstractions and their relationships
   - Important patterns and conventions
   - Local commands (test, build, run)
   - Gotchas specific to this area
   - Links to related CLAUDE.md files (if any)

## Output

Write the CLAUDE.md to {DIR_PATH}/CLAUDE.md

## Constraints

- Keep it concise (<200 lines preferred)
- Use imperative mood
- No chitchat or filler text
- Focus on non-obvious information
```

#### Launching Subagents

Use the Agent tool with subagent_type="general-purpose" for each batch:

```xml
<parameter name="Agent">
<param name="description">Analyze {DIR_PATH} for CLAUDE.md</param>
<param name="prompt">[template above with DIR_PATH filled in]</param>
<param name="run_in_background">true</param>
</parameter>
```

**Critical**: Launch all leaf-directory subagents in a SINGLE message to maximize parallelism.

### Phase 3: Process Intermediate Directories (Level by Level)

Wait for all child directory subagents to complete before processing each intermediate directory.

#### Intermediate Directory Prompt Template

```
You are analyzing directory: {DIR_PATH}

## Context
This is part of a bottom-up CLAUDE.md generation process. You are processing an intermediate directory.
Child directories have already been processed and have CLAUDE.md files.

## Child Directories
{LIST_OF_CHILD_DIRS with brief summaries from their CLAUDE.md}

## Tasks

1. **Read Child CLAUDE.md Files**: Understand what each child module does

2. **Analyze Local Code**: Files directly in this directory (not in subdirs)

3. **Identify Cross-Cutting Patterns**: What patterns span multiple child modules?

4. **Handle Existing CLAUDE.md** (if present):
   - Read current content
   - Preserve accurate sections
   - Update partially stale sections
   - Remove completely stale sections
   - Add missing information

5. **Generate CLAUDE.md**:
   - Purpose of this subsystem
   - Architecture overview with links to children
   - Cross-cutting patterns and conventions
   - Commands for this level
   - Gotchas affecting multiple child modules

## Output

Write the CLAUDE.md to {DIR_PATH}/CLAUDE.md

## Constraints

- Keep it concise (<300 lines preferred)
- Link to child CLAUDE.md files, don't duplicate their content
- Focus on aggregation and cross-cutting concerns
```

### Phase 4: Process Root Directory

The root CLAUDE.md should be the index and entry point:

```markdown
# {Project Name}

Brief description.

## Quick Start

```bash
[install command]
[run command]
```

## Architecture

[Overview paragraph]

| Package/Module | Purpose | Link |
|----------------|---------|------|
| [module1] | [brief description] | [path/to/CLAUDE.md] |
| [module2] | [brief description] | [path/to/CLAUDE.md] |

## Workspace Commands

| Command | Purpose |
|---------|---------|
| `npm run build` | Build all |
| `npm test` | Test all |

## Cross-Cutting Concerns

- **Linting**: [tool and config]
- **Formatting**: [tool and config]
- **Node/Python version**: [version]

## Environment

| Variable | Purpose | Required |
|----------|---------|----------|
| `API_KEY` | [what for] | Yes |

See individual CLAUDE.md files for module-specific details.
```

## Files to Analyze

### Include
- Source code files (all languages)
- Configuration files (JSON, YAML, TOML, INI, etc.)
- Build files (Makefile, build.gradle, Cargo.toml, pyproject.toml, etc.)
- Documentation files (README, CHANGELOG) - for context only

### Exclude
- Files matched by `.gitignore` patterns
- Generated files (`dist/`, `build/`, `__pycache__/`, `node_modules/`, `.venv/`)
- Binary files
- Lock files (`package-lock.json`, `poetry.lock`, `Cargo.lock`) - reference only

## Pattern Summarization Guidelines

### Patterns Worth Documenting

| Pattern Type | Examples | When to Document |
|--------------|----------|------------------|
| Design Patterns | Factory, Singleton, Observer, Strategy | When used consistently across the module |
| Architectural Patterns | Repository, MVC, Clean Architecture | When the module follows a specific architecture |
| Coding Conventions | Naming, file organization, import order | When non-standard or project-specific |
| Error Handling | Exception types, error codes, retry logic | When there's a consistent approach |
| Testing Patterns | Test structure, mocking approach, fixtures | When tests have specific conventions |
| Configuration Patterns | Env vars, config files, feature flags | When there's a specific configuration system |

### Patterns NOT Worth Documenting

- Standard language idioms (unless project-specific)
- Obvious patterns from the file structure
- Temporary or transitional patterns

## Handling Existing CLAUDE.md Files

### Process

1. **Read and parse** existing content structure
2. **Classify each section**:
   - **Still accurate**: Preserve as-is
   - **Partially stale**: Update with new information
   - **Completely stale**: Remove
   - **Missing**: Add based on codebase analysis
3. **Preserve**:
   - User-specific notes (marked with "NOTE:", "IMPORTANT:", or similar)
   - Custom conventions that don't contradict code
   - Historical context that remains relevant

### Example

Existing:
```markdown
## Commands
- `npm test` - Run tests
- `npm run build` - Build for production

## NOTE: Remember to update .env file before deploying
```

After analysis (if tests changed to vitest):
```markdown
## Commands
- `npm test` - Run tests with Vitest
- `npm run build` - Build for production
- `npm run test:coverage` - Run tests with coverage

## NOTE: Remember to update .env file before deploying
```

## Anti-Patterns to Avoid

### Don't Over-Document

Bad:
```markdown
## Functions

### `getUser(id: string): User`
Returns a user by ID.

### `getOrder(id: string): Order`
Returns an order by ID.
```

Good:
```markdown
## API

Standard CRUD operations for User and Order entities. See source for details.
```

### Don't Lose Parallelism

Bad: Processing directories sequentially when they could be parallel.

Good: Launch all independent directory analyses in a single message.

### Don't Skip Levels

Bad: Processing root before children are done.

Good: Wait for child completions, then process parent level.
