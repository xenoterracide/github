<!--
SPDX-FileCopyrightText: Copyright © 2015-2026 Caleb Cushing

SPDX-License-Identifier: CC-BY-NC-SA-4.0
-->

# github-workflows

<<<<<<< before updating
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
=======
github-workflows is a config project.
>>>>>>> after updating

## Use

<<<<<<< before updating
This project is managed with [copier](https://copier.readthedocs.io). To
refresh from the template:
=======
Copy the configuration files into your project and adjust the values.

## Install

Copy the configuration files you need into your project.

## Development

- [CONTRIBUTING.md](CONTRIBUTING.md) — human contributor guide
- [AGENTS.md](AGENTS.md) — guide for AI agents
>>>>>>> after updating

## License

<<<<<<< before updating
Renovate opens an update PR whenever
[base-template](https://github.com/xenoterracide/base-template) tags a
release. Merge it like any other dependency update.

## AI Agent Skills

This repository uses skills from
[xenoterracide/agent-skills](https://github.com/xenoterracide/agent-skills), an
agentic skills plugin providing AI coding agent capabilities for Java, Gradle,
GitHub, and development workflows.

## License

This repository uses [REUSE](https://reuse.software/) for license compliance.
See individual files for their SPDX license identifiers.
=======
Project code: [GPL-3.0-or-later](https://www.gnu.org/licenses/gpl-3.0.txt).
Documentation: [CC-BY-NC-SA-4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).
Copyright © 2015-2026 Caleb Cushing. Full texts live in [LICENSES](LICENSES).
Each file carries its own SPDX header. A file may use a different license.
>>>>>>> after updating
