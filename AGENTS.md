<!--
SPDX-FileCopyrightText: Copyright © 2026 Caleb Cushing

SPDX-License-Identifier: CC-BY-NC-SA-4.0
-->

# AGENTS.md

This file contains essential information for AI coding agents working on this repository.

## Project Overview

This is **`github-workflows`**, a repository maintained by Caleb Cushing that provides **reusable GitHub Actions workflows** for CI/CD automation, code formatting, license compliance, and Java dependency updates. The repository also embeds a shared tooling subtree (`.share/`) that supplies development configurations, git hooks, and Node.js CLI packages.

### Technology Stack

- **Node.js**: 24.14.1 via asdf (`.tool-versions`)
  - Root package manager: Yarn 4.16.0 (Plug'n'Play mode)
  - `.share` package manager: Yarn 4.13.0 with workspaces in `node/packages/*`
  - Prettier with plugins for XML, Java, Properties, and TOML
  - ESLint 10 with strict TypeScript rules (`typescript-eslint`)
- **Python**: Managed by `uv`
  - Dependency management via `pyproject.toml` with `uv.lock`
  - REUSE tool for license compliance (dev dependency)
  - Python version is defined in `pyproject.toml`, not `.tool-versions`
- **TypeScript**: Used for Node.js CLI packages inside `.share/node/packages/`
- **Testing**: Vitest with coverage (thresholds at 28%)

## Build and Test Commands

### Essential Commands

```bash
# Initial setup (run after cloning or when lockfile changes)
yarn precontribute   # installs corepack and yarn dependencies in root + .share
yarn contribute      # syncs Python deps and enables git hooks

# Run tests (run from .share where the workspaces are defined)
cd .share && yarn test

# Run all checks in .share (eslint + typecheck + test concurrently)
cd .share && yarn check
```

### Dependency Management

```bash
# Install Node.js dependencies from lockfile (root)
yarn install --immutable

# Install Node.js dependencies from lockfile (.share)
cd .share && yarn install --immutable

# Sync Python dependencies
uv sync --frozen
```

## Code Style Guidelines

### Formatting

- **Prettier** is used for all file types with the following plugins:
  - `@prettier/plugin-xml` - XML files
  - `prettier-plugin-java` - Java files
  - `prettier-plugin-properties` - Properties files
  - `prettier-plugin-toml` - TOML files

- **Configuration**: `prettierrc.cjs` (also present in `.share/`)
  - `printWidth: 120`
  - `xmlWhitespaceSensitivity: "ignore"`

- **EditorConfig**: `.editorconfig`
  - UTF-8 encoding
  - LF line endings
  - 2-space indentation
  - Final newline required

### Linting

- **ESLint** is configured in `.share/eslint.config.cts` with strict TypeScript rules:
  - Explicit function return types and member accessibility
  - Consistent type imports/exports
  - Strict boolean expressions and switch exhaustiveness
  - Prefer readonly, promise-function-async
  - Relaxed rules for `node/packages/**/*.ts` due to Yarn PnP resolution behavior

### Git Hooks

Git hooks are located in `.share/git/hooks/`:

**Pre-commit hooks:**

- **pre-commit**: Runs `lint-staged` to apply REUSE license headers and format with Prettier (skipped in CI)
- **commit-msg**: Validates conventional commit format using `git-conventional-commits`

**Post-operation hooks:**

- **post-merge**: Auto-runs `yarn install --immutable` and/or `uv sync --frozen` when `yarn.lock` or `uv.lock` change after `git pull`
- **post-checkout**: Auto-runs `yarn install --immutable` and/or `uv sync --frozen` when lockfiles differ between branches

To enable hooks:

```bash
git config core.hooksPath .share/git/hooks
```

### lint-staged Configuration

`lintstagedrc.cjs` (root) and `.share/.lintstagedrc.cjs` define per-file-type commands:

- TypeScript/Java code files: REUSE annotate with GPL-3.0-or-later, then Prettier
- JSON files (except package.json): REUSE annotate with CC0-1.0, then Prettier
- package.json: REUSE annotate with MIT, then Prettier
- Shell scripts: REUSE annotate with MIT (python style), then Prettier
- Documentation (Markdown, AsciiDoc): REUSE annotate with CC-BY-NC-SA-4.0, then Prettier
- Config files (XML, YAML, TOML, JSON5, etc.): REUSE annotate with CC0-1.0, then Prettier
- JavaScript/CJS/YML files: REUSE annotate with MIT, then Prettier

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

All PRs must pass these GitHub Actions workflows.

**Root workflows** (`.github/workflows/`):

1. **prettier** (`prettier.yml`): Prettier formatting check on Ubuntu 24.04 with Node.js 24.14.1
2. **license** (`license.yml`): REUSE compliance verification using `uv run --frozen --group dev reuse lint`
3. **node-cli** (`node-cli.yml`): Smoke test for the asdf Node.js toolchain. Verifies that updates to the Node.js binary do not break developer tools installed via Yarn (`prettier`, `lint-staged`, `git-conventional-commits`).
4. **ktlint** (`ktlint.yml`): Kotlin formatting check for Gradle scripts
5. **maven** (`maven.yml`): Reusable Maven build workflow
6. **yarn** (`yarn.yml`): Reusable Yarn check workflow (runs `yarn check`)
7. **update-java** (`update-java.yml`): Reusable workflow for automated Java/Gradle dependency updates (creates PR and auto-merges)

**`.share` workflows** (`.share/.github/workflows/`):

1. **devtools** (`devtools.yml`): Calls root `license.yml`, `prettier.yml`, and `node-cli.yml`
2. **test** (`test.yml`): Calls root `yarn.yml` to run checks against `.share`

### Local Testing

```bash
# Run tests with coverage (from .share)
cd .share && yarn test

# Run full check suite (eslint + tsc + vitest)
cd .share && yarn check

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
- **Node.js**: Yarn PnP with lockfiles (`yarn.lock` in root and `.share/`)
- **Renovate**: Automated dependency updates configured in `.github/renovate.json5`
  - Automerge enabled for asdf, maven, npm, pyenv, pep621, and github-actions
  - `xenoterracide/**` GitHub Actions are pinned to commit SHAs for reproducibility
  - `.share/**` and `.agents/**` paths are excluded from Renovate updates (managed via git subtree)

### Secrets and Environment

- `.envrc` configures asdf and adds `.share/bin` to PATH
- Never commit secrets or `.env` files
- AI-generated PR messages avoid exposing sensitive diff content
- `update-java.yml` requires `GRADLE_ENCRYPTION_KEY` and `MERGE_PAT` secrets

## Project Structure

```
.
├── .github/
│   ├── workflows/       # Reusable GitHub workflows (prettier, license, node-cli,
│   │                     ktlint, maven, yarn, update-java)
│   └── renovate.json5   # Renovate configuration for this repository
├── .share/              # Shared tooling subtree (from xenoterracide/subtree-ai)
│   ├── .github/
│   │   └── workflows/   # .share CI (devtools.yml, test.yml)
│   ├── git/hooks/       # Git hooks (pre-commit, commit-msg, post-merge, post-checkout)
│   ├── node/
│   │   └── packages/    # Node.js workspaces
│   │       ├── merge/       # PR merge automation for AI agents (kimi, junie, copilot)
│   │       └── secrets-sync/  # GitHub secrets sync CLI
│   ├── eslint.config.cts
│   ├── vitest.config.ts
│   └── package.json     # Workspace root (@xenoterracide/share)
├── LICENSES/            # SPDX license texts
├── .tool-versions       # asdf version definitions
├── git-conventional-commits.yaml
├── renovate.json5       # Root Renovate configuration
├── REUSE.toml           # REUSE compliance configuration
├── pyproject.toml       # Python project configuration
└── uv.lock              # Python dependency lockfile
```

## Licensing

This project uses REUSE specification for licensing:

| File Type                      | License          | Examples                            |
| ------------------------------ | ---------------- | ----------------------------------- |
| Source Code (TypeScript, Java) | GPL-3.0-or-later | `.ts`, `.java`                      |
| Scripts (JS, CJS, YML, Shell)  | MIT              | `.cjs`, `.js`, `.yml`, `.sh`        |
| Configuration                  | CC0-1.0          | `.json`, `.yaml`, `.toml`, `.json5` |
| Documentation                  | CC-BY-NC-SA-4.0  | `.md`, `.adoc`                      |

**Exceptions** (defined in `REUSE.toml`):

- Lockfiles: `*.lockfile`, `yarn.lock`, `uv.lock`
- Version files: `.tool-versions`, `.python-version`
- Gradle wrapper: `gradle/wrapper/*` (Apache-2.0)

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

- Run `cd .share && yarn test` before merging (root `yarn test` does not execute workspace tests)
- Ensure all files have proper SPDX license headers
- Follow conventional commit format for all commits
- Use `git config core.hooksPath .share/git/hooks` to enable git hooks
- PR descriptions become the squash merge commit message — format them accordingly
- AI attribution should be added as `Co-authored-by` trailers in commit messages
