<!--
SPDX-FileCopyrightText: Copyright © 2026 Caleb Cushing

SPDX-License-Identifier: CC-BY-NC-SA-4.0
-->

# AGENTS.md

This file contains essential information for AI coding agents working on this repository.

## Project Overview

This is **`github-workflows`**, a repository maintained by Caleb Cushing that provides **reusable GitHub Actions workflows** for CI/CD automation, code formatting, license compliance, and Java dependency updates. Local development uses git hooks from `.config/git/hooks/`.

### Technology Stack

- **Node.js**: pinned in `.tool-versions` (asdf); package manager pinned in `package.json` (`packageManager`, pnpm)
  - Prettier with plugins for XML, Java, Properties, and TOML
- **Python**: managed by `uv`
  - Dependency management via `pyproject.toml` with `uv.lock`
  - REUSE tool for license compliance (dev dependency)
  - Python version is defined in `pyproject.toml`, not `.tool-versions`

## Build and Test Commands

### Essential Commands

```bash
# Initial setup (run after cloning or when the toolchain changes)
pnpm contribute      # heals via .config/git/hooks/heal.sh

# Run all lint checks (prettier + REUSE)
pnpm lint
```

`pnpm contribute`, direnv's `.envrc`, and the git hooks all heal through
`.config/git/hooks/heal.sh`. Without direnv or pnpm, run it directly with
`sh .config/git/hooks/heal.sh`.

### Dependency Management

`heal.sh` runs these when their lockfiles change — from the hooks on
checkout and merge, from `.envrc` on direnv entry:

```bash
# Install Node.js dependencies from lockfile
pnpm install --frozen-lockfile

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

- **Configuration**: `prettierrc.cjs`
  - `printWidth: 120`
  - `xmlWhitespaceSensitivity: "ignore"`

- **EditorConfig**: `.editorconfig`
  - UTF-8 encoding
  - LF line endings
  - 2-space indentation
  - Final newline required

### Git Hooks

Git hooks are located in `.config/git/hooks/`:

**Pre-commit hooks:**

- **pre-commit**: Runs `lint-staged` to apply REUSE license headers and format with Prettier (skipped in CI)
- **commit-msg**: Validates conventional commit format using `git-conventional-commits`

**Post-operation hooks:**

- **post-merge / post-checkout**: run `heal.sh` once when `.tool-versions`,
  `pnpm-lock.yaml`, or `uv.lock` changed between revisions

To enable hooks:

```bash
git config core.hooksPath .config/git/hooks
```

`heal.sh` sets this itself; direnv entry and `pnpm contribute` both run it.

### lint-staged Configuration

`lintstagedrc.cjs` (root) defines per-file-type commands:

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

All PRs must pass these GitHub Actions workflows (see `.github/workflows/` for runner and version details).

**Reusable workflows** (`workflow_call`, consumed by other repositories):

1. **prettier** (`prettier.yml`): Prettier formatting check (also runs on push)
2. **license** (`license.yml`): REUSE compliance verification using `uv run --frozen --group dev reuse lint` (also runs on push)
3. **ktlint** (`ktlint.yml`): Kotlin formatting check for Gradle scripts
4. **maven** (`maven.yml`): Maven build workflow
5. **pnpm** (`pnpm.yml`): pnpm check workflow (runs `pnpm run check`)
6. **update-java** (`update-java.yml`): Automated Java/Gradle dependency updates (creates PR and auto-merges)

**Regression smoke tests** (run on push, also `workflow_call`): `regression-devtool-{prettier,lint-staged,git-conventional-commits,pre-commit,reuse}.yml` are reusable — consumer repositories call them via their own `devtools-regression.yml` to install their project and verify their devtools under their pinned toolchain. The Node-based ones install Node.js via `asdf-vm/actions/install` to mirror local environments.

### Local Testing

```bash
# Check formatting
pnpm exec prettier --ignore-unknown --check '**'

# Fix formatting
pnpm exec prettier --ignore-unknown --write '**'

# Check REUSE compliance
reuse lint
```

## Security Considerations

### Dependency Management

- **Python**: Uses `uv` with `pyproject.toml` and `uv.lock` for reproducible builds
- **Node.js**: pnpm with lockfile (`pnpm-lock.yaml`)
- **Renovate**: Automated dependency updates configured in `.github/renovate.json5`
  - Automerge enabled for asdf, maven, npm, pyenv, pep621, and github-actions
  - `xenoterracide/**` GitHub Actions are pinned to commit SHAs for reproducibility

### Secrets and Environment

- `.envrc` and the git hooks heal the toolchain via `.config/git/hooks/heal.sh`
- Never commit secrets or `.env` files
- AI-generated PR messages avoid exposing sensitive diff content
- `update-java.yml` requires `GRADLE_ENCRYPTION_KEY` and `MERGE_PAT` secrets

## Project Structure

```
.
├── .config/
│   └── git/hooks/       # Git hooks (pre-commit, commit-msg, post-merge, post-checkout) and heal.sh
├── .github/
│   ├── workflows/       # Reusable GitHub workflows (prettier, license, ktlint, maven,
│   │                     pnpm, update-java) and regression-devtool-* smoke tests
│   └── renovate.json5   # Renovate configuration for this repository
├── LICENSES/            # SPDX license texts
├── .tool-versions       # asdf version definitions
├── git-conventional-commits.yaml
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

- Lockfiles: `*.lockfile`, `pnpm-lock.yaml`, `uv.lock`
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

- Ensure all files have proper SPDX license headers
- Follow conventional commit format for all commits
- Use `git config core.hooksPath .config/git/hooks` to enable git hooks
- PR descriptions become the squash merge commit message — format them accordingly
- AI attribution should be added as `Assisted-by: <AI_NAME>:<MODEL_VERSION>` trailers in commit messages
