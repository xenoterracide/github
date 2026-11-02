<!--
SPDX-FileCopyrightText: Copyright © 2026 Caleb Cushing

SPDX-License-Identifier: CC-BY-NC-SA-4.0
-->

# github-workflows

Reusable GitHub Actions workflows for CI/CD automation.

## Available Workflows

| Workflow        | File              | Purpose                                    |
| --------------- | ----------------- | ------------------------------------------ |
| **Ktlint**      | `ktlint.yml`      | Kotlin formatting check for Gradle scripts |
| **License**     | `license.yml`     | REUSE compliance verification              |
| **Maven**       | `maven.yml`       | Maven build                                |
| **Prettier**    | `prettier.yml`    | Code formatting check (Prettier)           |
| **Update Java** | `update-java.yml` | Automated Java/Gradle dependency updates   |
| **Pnpm**        | `pnpm.yml`        | pnpm check (runs `pnpm run check`)         |

`regression-devtool-*.yml` are smoke tests that install the calling project and verify its devtools; five repositories call them via `devtools-regression.yml`, and they also run on push here.

## Usage

To use these workflows in your repository, reference them with `uses`:

```yaml
jobs:
  format:
    uses: xenoterracide/github/.github/workflows/prettier.yml@develop
```

## Development

### Setup

```bash
# Enable git hooks for formatting and license compliance
git config core.hooksPath .config/git/hooks

# Install dependencies
pnpm install
uv sync --frozen
```

### Testing

```bash
# Run all lint checks (prettier + REUSE)
pnpm lint

# Check formatting
pnpm exec prettier --ignore-unknown --check '**'

# Fix formatting
pnpm exec prettier --ignore-unknown --write '**'

# Check REUSE compliance
reuse lint
```

## AI Agent Skills

This repository uses skills from [xenoterracide/agent-skills](https://github.com/xenoterracide/agent-skills), a Kimi Code plugin providing AI coding agent capabilities for Java, Gradle, GitHub, and development workflows.

## License

This repository uses [REUSE](https://reuse.software/) for license compliance.
See individual files for their SPDX license identifiers.
