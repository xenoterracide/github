<!--
SPDX-FileCopyrightText: Copyright © 2026 Caleb Cushing

SPDX-License-Identifier: CC-BY-NC-SA-4.0
-->

# github-workflows

Reusable GitHub Actions workflows for CI/CD automation.

## Available Workflows

| Workflow          | File                | Purpose                                  |
| ----------------- | ------------------- | ---------------------------------------- |
| **Prettier**      | `prettier.yml`      | Code formatting check (Prettier)         |
| **License**       | `license.yml`       | REUSE compliance verification            |
| **Tool Versions** | `tool-versions.yml` | Smoke test asdf-managed CLI tools        |
| **Update Java**   | `update-java.yml`   | Automated Java/Gradle dependency updates |

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
git config core.hooksPath .share/git/hooks

# Install dependencies
yarn install
uv sync --frozen
```

### Testing

```bash
# Run all checks
yarn test

# Check formatting
yarn exec prettier --ignore-unknown --check '**'

# Fix formatting
yarn exec prettier --ignore-unknown --write '**'

# Check REUSE compliance
reuse lint
```

## License

This repository uses [REUSE](https://reuse.software/) for license compliance.
See individual files for their SPDX license identifiers.
