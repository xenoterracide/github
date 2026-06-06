<!--
SPDX-FileCopyrightText: Copyright © 2026 Caleb Cushing

SPDX-License-Identifier: CC-BY-NC-SA-4.0
-->

# AGENTS.md

This file contains essential information for AI coding agents working on this repository.

## Project Overview

This is **`github-workflows`**, a repository maintained by Caleb Cushing that provides **reusable GitHub Actions workflows** for CI/CD automation. The repository also embeds a **`.share/` directory** containing shared tooling, git hooks, AI agent skills, and Node.js utility packages (merge automation and secrets sync) that are synchronized from the `template-main` / `share` repository.

### Technology Stack

- **Node.js**: 24.14.1 with Yarn 4.16.0 (Plug'n'Play mode)
  - Additional workspaces and TypeScript tooling live in `.share/node/packages/`
- **Python**: 3.12+ managed by `uv` (not asdf)
  - Dependency management via `pyproject.toml` with `uv`
  - REUSE tool for license compliance (dev dependency)
- **TypeScript**: Executed via `tsx` for CLI tooling in `.share/node/packages/`
- **Version Management**: asdf (`.tool-versions`) for Node.js

## Project Structure

```
.
├── .github/
│   ├── workflows/       # Reusable GitHub workflows (main deliverable)
│   │   ├── prettier.yml    # Prettier formatting check
│   │   ├── license.yml     # REUSE compliance verification
│   │   ├── node-cli.yml    # Smoke test yarn-managed Node.js CLI tools
│   │   ├── yarn.yml        # Yarn integrity check
│   │   ├── update-java.yml # Automated Java/Gradle dependency updates
│   │   ├── ktlint.yml      # Kotlin linting
│   │   └── maven.yml       # Maven build verification
│   └── renovate.json5   # Renovate configuration
├── .share/              # Shared tooling repository (template-main/share)
│   ├── .agents/
│   │   ├── mcp/         # MCP (Model Context Protocol) config
│   │   └── skills/      # AI agent skills (commit-message, github, java, etc.)
│   ├── git/hooks/       # Git hooks (pre-commit, commit-msg, post-checkout, post-merge)
│   ├── node/packages/   # Yarn workspaces
│   │   ├── merge/       # AI-assisted PR merge tool (TypeScript/clipanion)
│   │   └── secrets-sync/# GitHub secrets sync CLI tool
│   ├── package.json     # Share-level Node.js config with workspaces
│   ├── vitest.config.ts # Test configuration for workspaces
│   └── eslint.config.cts# ESLint configuration for TypeScript
├── .agents/             # AI agent configuration (subtree/submodule from .share)
├── package.json         # Root Node.js configuration
├── pyproject.toml       # Python project configuration
├── uv.lock              # Python dependency lockfile
├── yarn.lock            # Node.js dependency lockfile
├── .tool-versions       # asdf version definitions (Node.js)
├── git-conventional-commits.yaml  # Commit convention config
├── renovate.json5       # Renovate bot configuration (root)
├── REUSE.toml           # REUSE compliance configuration
├── .lintstagedrc.cjs    # lint-staged per-file-type commands
├── .prettierrc.cjs      # Prettier configuration
└── .editorconfig        # EditorConfig formatting rules
```

## Build and Test Commands

### Essential Commands

```bash
# Initial setup (run after cloning or when lockfile changes)
yarn contribute

# Run all tests (MUST pass before merging)
yarn test
```

### Setup Details

```bash
# Full initial setup
yarn precontribute   # Installs corepack, yarn, and dependencies
yarn contribute      # Syncs Python venv and configures git hooks
```

The `yarn contribute` script at the root does the following:

- Runs `uv sync --frozen`
- Configures `git config core.hooksPath .share/git/hooks`

### Dependency Management

```bash
# Install dependencies from lockfile (use after pulling changes that updated yarn.lock)
yarn install --immutable

# Update Node.js dependencies
yarn up

# Sync Python dependencies (uses uv)
uv sync --frozen
```

### Linting Commands

```bash
# Run all linters
yarn lint

# Individual linters
yarn lint:prettier   # Prettier formatting check
yarn lint:reuse      # REUSE license compliance check
```

### Merge Automation

```bash
# AI-assisted PR merge workflows (runs from .share/)
yarn merge:kimi      # Uses Kimi CLI
```

## Code Style Guidelines

### Formatting

- **Prettier** is used for all file types with the following plugins:
  - `@prettier/plugin-xml` - XML files
  - `prettier-plugin-java` - Java files
  - `prettier-plugin-properties` - Properties files
  - `prettier-plugin-toml` - TOML files

- **Configuration**: `.prettierrc.cjs`
  - `printWidth: 120`
  - `xmlWhitespaceSensitivity: "ignore"`

- **EditorConfig**: `.editorconfig`
  - UTF-8 encoding
  - LF line endings
  - 2-space indentation
  - Final newline required

### Git Hooks

Git hooks are located in `.share/git/hooks/`:

**Pre-commit hooks:**

- **pre-commit**: Runs `lint-staged` to apply REUSE license headers and format with Prettier (skipped in CI)
- **commit-msg**: Validates conventional commit format using `git-conventional-commits`

**Post-operation hooks:**

- **post-merge**: Auto-runs `yarn install --immutable` and/or `uv sync --frozen` when lockfiles change after `git pull`
- **post-checkout**: Auto-runs `yarn install --immutable` and/or `uv sync --frozen` when lockfiles differ between branches

To enable hooks:

```bash
git config core.hooksPath .share/git/hooks
```

### lint-staged Configuration

`.lintstagedrc.cjs` defines per-file-type commands:

- Code files (TypeScript, Java): REUSE annotate with GPL-3.0-or-later, then Prettier
- JSON files (except package.json): REUSE annotate with CC0-1.0, then Prettier
- package.json: REUSE annotate with MIT, then Prettier
- Shell scripts: REUSE annotate with MIT (python style), then Prettier
- Documentation (Markdown, AsciiDoc): REUSE annotate with CC-BY-NC-SA-4.0, then Prettier
- Config files (XML, YAML, TOML, etc.): REUSE annotate with CC0-1.0, then Prettier

### Conventional Commits

All commits MUST follow the Conventional Commits specification (`git-conventional-commits.yaml`):

**Allowed Types**:

- `ci` - CI/CD changes
- `feat` - New features
- `fix` - Bug fixes
- `perf` - Performance improvements
- `refactor` - Code refactoring
- `style` - Code style changes
- `test` - Test changes
- `build` - Build system changes
- `ops` - Operations
- `docs` - Documentation
- `chore` - Maintenance
- `merge` - Merge commits
- `revert` - Reverts

**Format**:

```
<type>(<scope>): <summary>

<body>
```

## Testing Instructions

### CI Workflows

All PRs must pass these GitHub Actions workflows (defined in `.github/workflows/`):

1. **prettier** (`prettier.yml`): Prettier formatting check
   - Runs on Ubuntu 24.04 with Node.js 24.14.1
   - Executes `yarn exec prettier --ignore-unknown --check '**'`

2. **license** (`license.yml`): REUSE compliance verification
   - Runs on Ubuntu 24.04 with Python 3 and uv
   - Executes `uv run --frozen --group dev reuse lint`

3. **node-cli** (`node-cli.yml`): Smoke test for yarn-managed Node.js CLI tools
   - Runs on Ubuntu 24.04 with asdf
   - Verifies `git-conventional-commits`, `lint-staged`, and `prettier` executables

### Additional Reusable Workflows

These workflows are triggered via `workflow_call` (typically by consuming repositories):

- **yarn** (`yarn.yml`): Yarn integrity check (`yarn install --immutable`, `yarn check`)
- **update-java** (`update-java.yml`): Automated Java dependency updates (Gradle wrapper, lockfiles)
- **ktlint** (`ktlint.yml`): Kotlin linting for Gradle Kotlin DSL files
- **maven** (`maven.yml`): Maven build verification (`./mvnw verify`)

### Local Testing

```bash
# Check formatting
yarn exec prettier --ignore-unknown --check '**'

# Fix formatting
yarn exec prettier --ignore-unknown --write '**'

# Check REUSE compliance
reuse lint

# Run workspace tests (from .share/)
cd .share && yarn test
```

## Security Considerations

### Dependency Management

- **Python**: Uses `uv` with `pyproject.toml` and `uv.lock` for reproducible builds
- **Node.js**: Yarn PnP with lockfile (`yarn.lock`)
- **Renovate**: Automated dependency updates configured in `.github/renovate.json5`

**Renovate Configuration**:

- Auto-merge enabled for asdf, maven, npm, pyenv, pep621, and github-actions
- GitHub Actions from `xenoterracide/**` are pinned to commit SHAs for reproducibility
- `.share/**` and `.agents/**` are excluded from Renovate updates
- PR body template excludes the `controls` section to avoid config noise in squash commits

### Secrets and Environment

- `.envrc` configures asdf and adds `.share/bin` to PATH
- Never commit secrets or `.env` files
- AI-generated PR messages avoid exposing sensitive diff content
- Git hooks check `[ -n "$CI" ]` and exit early in CI environments

## Licensing

This project uses REUSE specification for licensing:

| File Type                      | License          | Examples                           |
| ------------------------------ | ---------------- | ---------------------------------- |
| Source Code (TypeScript, Java) | GPL-3.0-or-later | `.ts`, `.java`                     |
| Scripts (JS, CJS, YML)         | MIT              | `.cjs`, `.js`, `.yml`              |
| package.json                   | MIT              | `package.json`                     |
| JSON files (non-package)       | CC0-1.0          | `.json`                            |
| Configuration                  | CC0-1.0          | `.xml`, `.yaml`, `.toml`, `.json5` |
| Documentation                  | CC-BY-NC-SA-4.0  | `.md`, `.adoc`                     |
| Skills                         | CC-BY-NC-SA-4.0  | `.agents/skills/**/*.md`           |

**Exceptions** (defined in `REUSE.toml`):

- Lockfiles: `*.lockfile`, `yarn.lock`, `uv.lock`
- Version files: `.tool-versions`, `.python-version`

All files MUST include SPDX headers. Use `reuse annotate` to add license headers:

```bash
# For code files (GPL-3.0-or-later)
reuse annotate --copyright 'Caleb Cushing' --license 'GPL-3.0-or-later' <file>

# For scripts (MIT)
reuse annotate --copyright 'Caleb Cushing' --license 'MIT' --fallback-dot-license <file>

# For configuration files (CC0-1.0)
reuse annotate --copyright 'Caleb Cushing' --license 'CC0-1.0' --fallback-dot-license <file>

# For documentation (CC-BY-NC-SA-4.0)
reuse annotate --copyright 'Caleb Cushing' --license 'CC-BY-NC-SA-4.0' <file>
```

## Important Notes

- Always run `yarn test` before merging
- Ensure all files have proper SPDX license headers
- Follow conventional commit format for all commits
- Use `git config core.hooksPath .share/git/hooks` to enable git hooks
- PR descriptions become the squash merge commit message - format them accordingly
- AI attribution should be added as `Co-authored-by` trailers in commit messages
- The `.share/` directory contains a separate shared tooling repository; changes there may need to be synced upstream to `template-main`
