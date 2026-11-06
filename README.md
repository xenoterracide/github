<!--
SPDX-FileCopyrightText: Copyright © 2026 Caleb Cushing

SPDX-License-Identifier: CC-BY-NC-SA-4.0
-->

# github-workflows

Reusable GitHub Actions workflows for CI/CD automation.

## Available Workflows

| Workflow        | File              | Purpose                                    |
| --------------- | ----------------- | ------------------------------------------ |
| **Copier**      | `copier.yml`      | Render and verify a copier template        |
| **Ktlint**      | `ktlint.yml`      | Kotlin formatting check for Gradle scripts |
| **License**     | `license.yml`     | REUSE compliance verification              |
| **Maven**       | `maven.yml`       | Maven build                                |
| **Prettier**    | `prettier.yml`    | Code formatting check (Prettier)           |
| **Pnpm**        | `pnpm.yml`        | pnpm check (runs `pnpm run check`)         |
| **Update Java** | `update-java.yml` | Automated Java/Gradle dependency updates   |

`regression-devtool-*.yml` are smoke tests that install the calling project and
verify their devtools; five repositories call them via `devtools-regression.yml`,
and they also run on push here.

## Usage

To use these workflows in your repository, reference them with `uses`:

```yaml
jobs:
  format:
    uses: xenoterracide/github/.github/workflows/prettier.yml@develop
```

## Getting started

- `pnpm contribute` installs tools and dependencies, and heals the toolchain
  through `.config/git/hooks/heal.sh`.
- `direnv allow` (if you use direnv) loads the environment.

This project is managed with [copier](https://copier.readthedocs.io). To
refresh from the template:

    copier update --trust

## AI Agent Skills

This repository uses skills from
[xenoterracide/agent-skills](https://github.com/xenoterracide/agent-skills), an
agentic skills plugin providing AI coding agent capabilities for Java, Gradle,
GitHub, and development workflows.

## License

This repository uses [REUSE](https://reuse.software/) for license compliance.
See individual files for their SPDX license identifiers.
