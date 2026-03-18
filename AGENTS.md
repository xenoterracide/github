<!--
SPDX-FileCopyrightText: Copyright © 2026 Caleb Cushing

SPDX-License-Identifier: CC-BY-NC-SA-4.0
-->

# AGENTS.md

This file contains essential information for AI coding agents working on this repository.

## Project Overview

This is **`github-workflows`**, a repository maintained by Caleb Cushing that provides **reusable GitHub workflows** (and potentially actions in the future) that can be shared across multiple projects. The repository also demonstrates best practices for CI/CD automation, code formatting, and license compliance.

### Technology Stack

- **Node.js**: 24.14.0 with Yarn 4.12.0 (Plug'n'Play mode)
  - Workspace configuration in `.share/node/`
  - Prettier with plugins for multiple file types
- **Python**: Managed by `uv` (not asdf)
  - Dependency management via `pyproject.toml` with `uv`
  - REUSE tool for license compliance
  - Python version is defined in `pyproject.toml`, not `.tool-versions`
- **Version Management**: asdf (`.tool-versions`) for Node.js and other tools

## Build and Test Commands

### Essential Commands

```bash
# Setup development environment (installs Python deps, configures git hooks)
yarn contribute

# Run all tests (MUST pass before merging)
yarn test
```

### Dependency Management

```bash
# Update Node.js dependencies
yarn up

# Sync Python dependencies (uses uv)
uv sync --frozen
```

## Code Style Guidelines

### Formatting

- **Prettier** is used for all file types with the following plugins:
  - `@prettier/plugin-xml` - XML files
  - `prettier-plugin-java` - Java files
  - `prettier-plugin-properties` - Properties files
  - `prettier-plugin-toml` - TOML files

- **Configuration**: `prettierrc.cjs`
  - `printWidth: 120`
  - `xmlWhitespaceSensitivity: "ignore"`

- **EditorConfig**: `.editorconfig`
  - UTF-8 encoding
  - LF line endings
  - 2-space indentation
  - Final newline required

### Pre-Commit Hooks

Git hooks are located in `.share/git/hooks/`:

- **pre-commit**: Runs `lint-staged` to apply REUSE license headers and format with Prettier (skipped in CI)
- **commit-msg**: Validates conventional commit format using `git-conventional-commits`

To enable hooks:

```bash
git config core.hooksPath .share/git/hooks
```

### lint-staged Configuration

`lintstagedrc.cjs` defines per-file-type commands:

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
   - Runs on Ubuntu 24.04 with Node.js 24
   - Executes `yarn exec prettier --ignore-unknown --check '**'`

2. **license** (`license.yml`): REUSE compliance verification
   - Runs on Ubuntu 24.04 with Python 3 and uv
   - Executes `reuse lint`

3. **update-java** (`update-java.yml`): Automated Java dependency updates
   - Triggered via workflow_call (typically by Renovate)
   - Updates Gradle wrapper and lockfiles
   - Creates PR with updates and auto-merges

### Local Testing

```bash
# Check formatting
yarn exec prettier --ignore-unknown --check '**'

# Fix formatting
yarn exec prettier --ignore-unknown --write '**'

# Check REUSE compliance
reuse lint
```

## Security Considerations

### Dependency Management

- **Python**: Uses `uv` with `pyproject.toml` and `uv.lock` for reproducible builds
- **Node.js**: Yarn PnP with lockfile (`yarn.lock`)
- **Renovate**: Automated dependency updates configured in `.github/renovate.json5`

**Renovate Schedule**:

- Gradle major updates: Daily at 04:00 UTC
- Gradle plugins: Weekly Wednesday at 05:00 UTC
- GitHub Actions: Automatic with automerge
- npm/asdf devDependencies: Weekly Wednesday at 04:00 UTC

### Secrets and Environment

- `.envrc` configures asdf and adds `.share/bin` to PATH
- Never commit secrets or `.env` files
- AI-generated PR messages avoid exposing sensitive diff content

## Project Structure

```
.
├── .github/
│   ├── workflows/       # **Reusable GitHub workflows** - main deliverable
│   │                     (format, license, update-java)
│   └── renovate.json5   # Renovate configuration for GitHub
├── .agents/              # AI agent configuration and skills
│   ├── mcp/             # MCP (Model Context Protocol) config
│   └── skills/          # AI skills for commit messages, Java, GitHub, etc.
├── .share/              # Shared tooling and scripts
│   ├── bin/             # Custom scripts
│   ├── git/hooks/       # Git hooks (pre-commit, commit-msg)
│   └── node/            # Node.js workspaces
│       └── merge/       # Merge automation (TypeScript)
├── LICENSES/            # SPDX license texts
├── .tool-versions       # asdf version definitions
├── git-conventional-commits.yaml  # Commit convention config
├── renovate.json5       # Renovate bot configuration (root)
├── REUSE.toml           # REUSE compliance configuration
├── pyproject.toml       # Python project configuration
└── uv.lock              # Python dependency lockfile
```

## AI Skills

The `.agents/skills/` directory contains specialized instructions for AI agents:

- **commit-message**: Conventional commit format for PRs and commits
- **github**: GitHub CLI usage patterns
- **java**: Java coding preferences (prefer `var`, immutability, package-private visibility)
- **shell-script**: POSIX-compliant shell scripting with shellcheck
- **pull-request**: Workflow for creating/updating PRs, handling review comments
- **use-case-creator**: Cockburn format use case specifications with semantic anchors

Skills use YAML frontmatter with metadata including allowed tools and licensing.

## Licensing

This project uses REUSE specification for licensing:

| File Type                             | License          | Examples                                |
| ------------------------------------- | ---------------- | --------------------------------------- |
| Source Code (Java, TypeScript, Shell) | GPL-3.0-or-later | `.ts`, `.java`, `.sh`                   |
| Scripts (JS, CJS)                     | MIT              | `*.cjs`                                 |
| Configuration                         | CC0-1.0          | `*.json`, `*.yaml`, `*.toml`, `*.json5` |
| Documentation                         | CC-BY-NC-SA-4.0  | `*.md`, `*.adoc`                        |
| Skills                                | CC-BY-NC-SA-4.0  | `.agents/skills/**/*.md`                |

**Exceptions** (defined in `REUSE.toml`):

- Lockfiles: `*.lockfile`, `yarn.lock`, `uv.lock`
- Version files: `.tool-versions`

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
