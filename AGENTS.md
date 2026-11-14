<!--
SPDX-FileCopyrightText: Copyright © 2026 Caleb Cushing

SPDX-License-Identifier: CC-BY-NC-SA-4.0
-->

# github-workflows

## Build and Test

- `npm run contribute` — bootstrap tooling (run once after clone)
- `pnpm check` — run all checks
- `pnpm lint:prettier` — prettier check
- `pnpm lint:reuse` — REUSE compliance check

## Source of Truth

- Template: `copier.yml` in [xenoterracide/base-template](https://github.com/xenoterracide/base-template)
- Answers: `.copier-answers.yml` in this repo
- Updates: `uv run --group dev copier update --trust` refreshes from template
- Hooks heal the toolchain: `.envrc`, `contribute`, and the post hooks call
  `.config/git/hooks/heal.sh`. A failed heal never breaks a checkout.

## Reusable Workflows

- This repo's product: `.github/workflows/*.yml` are `workflow_call` workflows
  consumed by other repositories; changes affect every consumer.
- `regression-devtool-*.yml` are smoke tests that install the calling project
  and verify its devtools under its pinned toolchain.
- `update-java.yml` requires `GRADLE_ENCRYPTION_KEY` and `MERGE_PAT` secrets.

## Conventions

- Read `.editorconfig` before editing. Every text file ends with a newline.
