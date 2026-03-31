# CLAUDE.md Decomposition Guidelines

When and how to break down a long CLAUDE.md into subdirectory-level files.

## Signs Decomposition Is Needed

| Sign | Threshold | Action |
|------|-----------|--------|
| File length | >500 lines | Consider decomposition |
| Multiple domains | Frontend + backend + DevOps | Split by domain |
| Monorepo structure | `packages/` with separate concerns | Create per-package CLAUDE.md |
| Frequent conflicts | Multiple contributors editing same file | Split ownership by area |

## Identifying Logical Boundaries

### Directory-Based Boundaries

```
project/
├── frontend/          → frontend/CLAUDE.md
│   ├── src/
│   └── package.json
├── backend/           → backend/CLAUDE.md
│   ├── src/
│   └── package.json
└── CLAUDE.md          → Root index + cross-cutting concerns
```

### Feature-Based Boundaries

```
project/
├── src/
│   ├── auth/          → src/auth/CLAUDE.md
│   ├── payments/      → src/payments/CLAUDE.md
│   └── api/           → src/api/CLAUDE.md
└── CLAUDE.md          → Root index
```

### Package-Based Boundaries (Monorepo)

```
project/
├── packages/
│   ├── core/          → packages/core/CLAUDE.md
│   ├── ui/            → packages/ui/CLAUDE.md
│   └── cli/           → packages/cli/CLAUDE.md
└── CLAUDE.md          → Root index + workspace commands
```

## What Goes Where

### Root CLAUDE.md (Index)

Keep at the root level:
- Project overview and purpose
- Quick start / getting started
- Workspace-level commands (install all, test all)
- Links to subdirectory CLAUDE.md files
- Cross-cutting concerns (linting, formatting)
- Shared environment variables
- Common gotchas affecting all areas

**Example root CLAUDE.md:**
```markdown
# Project Name

Brief description.

## Quick Start

\`\`\`bash
npm install
npm run dev
\`\`\`

## Architecture

This is a monorepo with three packages:
- [core](./packages/core/CLAUDE.md) - Shared utilities
- [api](./packages/api/CLAUDE.md) - REST API server
- [web](./packages/web/CLAUDE.md) - Frontend application

## Workspace Commands

| Command | Purpose |
|---------|---------|
| `npm run build` | Build all packages |
| `npm test` | Test all packages |
| `npm run lint` | Lint all packages |

## Cross-Cutting

- **Linting**: ESLint with config at `eslint.config.js`
- **Formatting**: Prettier, run `npm run format`
- **Node version**: 20.x (use `nvm use`)

See individual package CLAUDE.md files for package-specific commands.
```

### Subdirectory CLAUDE.md (Domain-Specific)

Include in subdirectory files:
- Package/module-specific commands
- Local architecture and structure
- Domain-specific gotchas
- Local environment requirements
- Testing patterns for that area

**Example packages/api/CLAUDE.md:**
```markdown
# API Package

REST API server using Express.

## Commands

| Command | Purpose |
|---------|---------|
| `npm run dev --workspace=packages/api` | Start dev server on :3001 |
| `npm test --workspace=packages/api` | Run API tests |
| `npm run db:migrate` | Run database migrations |

## Structure

\`\`\`
src/
├── routes/       # Express route handlers
├── middleware/   # Auth, logging, error handling
├── models/       # Database models
└── services/     # Business logic
\`\`\`

## Gotchas

- Database migrations must be run before starting the server
- Auth middleware requires `JWT_SECRET` env var
- Rate limiting is enabled in production mode
```

## Migration Process

When decomposing an existing CLAUDE.md:

### 1. Analyze Current Content

Group existing sections by domain:
- Mark each section with which package/area it belongs to
- Identify cross-cutting content
- Find orphaned content (references to deleted features)

### 2. Create Subdirectory Files

For each domain:
1. Create `CLAUDE.md` in the appropriate directory
2. Move domain-specific content from root
3. Add local context (commands, structure)

### 3. Update Root File

1. Remove moved content
2. Add links to new subdirectory files
3. Keep only cross-cutting concerns
4. Add architecture overview with links

### 4. Verify

- [ ] Root file links to all subdirectory files
- [ ] No duplicate content between files
- [ ] Each file is <300 lines
- [ ] Commands work from their respective directories

## Anti-Patterns to Avoid

### Don't Duplicate

Bad - Root CLAUDE.md:
```markdown
## API Commands
`npm run dev` - Start API server
```

Also Bad - packages/api/CLAUDE.md:
```markdown
## API Commands
`npm run dev` - Start API server
```

Good - Only in packages/api/CLAUDE.md, linked from root.

### Don't Over-Decompose

Bad:
```
src/
├── utils/
│   └── CLAUDE.md    # Too granular for a utils folder
└── types/
    └── CLAUDE.md    # Just type definitions, no need for doc
```

Good: Keep these in the parent or root CLAUDE.md.

### Don't Lose Context

Bad - After decomposition, root CLAUDE.md only has:
```markdown
See packages/CLAUDE.md files.
```

Good - Root still has overview:
```markdown
## Architecture
Three packages: core, api, web. See individual CLAUDE.md files for details.
```

## Decision Tree

```
Is CLAUDE.md > 500 lines?
├── No → Keep as single file
└── Yes → Does project have clear domain boundaries?
    ├── No → Consider rewriting for conciseness
    └── Yes → Decompose by domain
        ├── Monorepo? → packages/*/CLAUDE.md
        ├── Frontend/Backend? → frontend/CLAUDE.md, backend/CLAUDE.md
        └── Feature areas? → src/*/CLAUDE.md
```
