<!--
SPDX-FileCopyrightText: Copyright © 2026 Caleb Cushing

SPDX-License-Identifier: CC-BY-NC-4.0
-->

# AGENTS.md

This file contains essential information for AI coding agents working on this repository.

## Project Overview

This is `template-main`, a multi-language template repository maintained by Caleb Cushing. It serves as a foundation for Java-based projects with integrated Node.js tooling, Python utilities, and comprehensive CI/CD automation.

### Technology Stack

- **Java**: Gradle build system (Kotlin DSL)
- **Node.js**: 24.14.0 with Yarn 4.12.0 (Plug'n'Play mode)
- **Python**: 3.14.3 for tooling (REUSE compliance)
- **Version Management**: asdf (`.tool-versions`)

## Build and Test Commands

### Essential Commands

```bash
# Run all tests (MUST pass before merging)
yarn test

# Build the project
make build

# Setup development environment
yarn contribute
```

### Dependency Management

```bash
# Update Node.js dependencies
yarn up

# Install Python dependencies
pip install -r requirements.txt --require-hashes
```

### Merge Workflow

```bash
# Full automated merge workflow (fetch, push, create/update PR, watch CI, merge)
make merge

# Individual steps
make merge-head    # Fetch and merge origin/HEAD
make push          # Push current branch
make create-pr     # Create or update PR with AI-generated message
```

### AI-Powered PR Messages

The project supports multiple AI engines for generating PR messages:

```bash
# Using Kimi (default)
yarn merge:kimi

# Using Copilot
yarn merge:copilot

# Using Junie
yarn merge:junie
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

### Pre-Commit Hooks

Git hooks are located in `.share/git/hooks/`:

- **pre-commit**: Runs `lint-staged` (skipped in CI)
- **commit-msg**: Validates conventional commit format

To enable hooks:

```bash
git config core.hooksPath .share/git/hooks
```

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

All PRs must pass these GitHub Actions workflows:

1. **format** (`.github/workflows/format.yml`): Prettier formatting check
2. **license** (`.github/workflows/license.yml`): REUSE compliance verification

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

- **Python**: `requirements.txt` uses hash verification (`--require-hashes`)
- **Node.js**: Yarn PnP with lockfile (`yarn.lock`)
- **Renovate**: Automated dependency updates (`.github/renovate.json5`)

### Secrets and Environment

- `.envrc` configures asdf and adds `.share/bin` to PATH
- Never commit secrets or `.env` files
- AI-generated PR messages avoid exposing sensitive diff content

## Project Structure

```
.
├── .ai/skills/           # AI agent skills (commit messages, Java, GitHub, shell)
├── .github/workflows/    # CI/CD workflows
├── .share/               # Shared tooling
│   ├── bin/             # Custom scripts (pr-message.sh)
│   ├── git/hooks/       # Git hooks
│   └── node/merge/      # Merge automation (TypeScript)
├── LICENSES/            # SPDX license texts
├── .tool-versions       # asdf version definitions
├── git-conventional-commits.yaml  # Commit convention config
├── renovate.json5       # Renovate bot configuration
└── REUSE.toml           # REUSE compliance configuration
```

## Licensing

This project uses REUSE specification for licensing:

- **Source Code**: GPL-3.0-or-later (Java, TypeScript, Shell)
- **Configuration**: CC0-1.0 (public domain)
- **Scripts**: MIT
- **Documentation**: CC-BY-NC-4.0

All files MUST include SPDX headers. Use `reuse annotate` to add license headers:

```bash
# For code files
reuse annotate --copyright 'Caleb Cushing' --license 'GPL-3.0-or-later' <file>

# For configuration files
reuse annotate --copyright 'Caleb Cushing' --license 'CC0-1.0' --fallback-dot-license <file>
```

## AI Skills

The `.ai/skills/` directory contains specialized instructions for AI agents:

- **commit-or-pr-message**: Conventional commit format for PRs
- **java**: Java coding preferences (prefer `var`, package-private visibility)
- **github**: GitHub CLI usage patterns
- **shell-script**: POSIX-compliant shell scripting with shellcheck

## Important Notes

- Always run `yarn test` before merging
- Ensure all files have proper SPDX license headers
- Follow conventional commit format for all commits
- The `make merge` command handles the full PR lifecycle
