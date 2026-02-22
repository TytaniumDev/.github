# TytaniumDev/.github

Shared GitHub configuration, reusable workflows, and workflow templates for all TytaniumDev repositories.

## Reusable Workflows

All workflows use `workflow_call` and are called from consumer repos via thin caller files.

| Workflow | Purpose | Inputs/Secrets |
|----------|---------|----------------|
| `lint.yml` | Run `./scripts/lint.sh` | None |
| `build.yml` | Run `./scripts/build.sh` | None |
| `test.yml` | Run `./scripts/test.sh` | None |
| `automerge-label.yml` | Add "automerge" label to PRs | None |
| `claude.yml` | Run Claude Code on `@claude` mentions | `CLAUDE_CODE_OAUTH_TOKEN` (secret) |
| `claude-code-review.yml` | Automated PR code review | `CLAUDE_CODE_OAUTH_TOKEN` (secret) |
| `workflow-lint.yml` | Check workflows for unsafe secret inlining | None |

### CI Workflows (lint, build, test)

These are language-agnostic. Each consumer repo provides its own `scripts/lint.sh`, `scripts/build.sh`, and `scripts/test.sh` that handle:

1. Installing the toolchain (Node.js, Python, uv, Swift, etc.)
2. Installing dependencies
3. Running the actual commands
4. Exiting non-zero on failure (`set -euo pipefail`)

This design works for any language without changing the shared workflows.

## Workflow Templates

Templates appear in every org repo's **Actions > New workflow** picker. Available templates:

| Template | Description |
|----------|-------------|
| CI | Standard lint + build + test pipeline |
| Auto Label PRs | Add "automerge" label to PRs |
| Claude Code | `@claude` mention integration |
| Claude Code Review | Automated PR review |
| Workflow Lint | Secret safety linter for workflow files |

## Scripts

- `scripts/check-workflow-secrets.py` — Checks workflow YAML files for unsafe inlining of multi-line secrets in `run:` blocks. Used by the `workflow-lint.yml` reusable workflow.

## Settings

`settings.yml` enforces org-wide repository settings via [probot/settings](https://github.com/probot/settings):

- Branch protection on `main`: requires 1 approval, code owner review, and passing `CI / Lint`, `CI / Build`, `CI / Test` checks
- Auto-merge enabled, auto-delete head branches

## Onboarding a New Repo

1. Go to **Actions > New workflow** and pick the templates you need
2. Create `scripts/lint.sh`, `scripts/build.sh`, and `scripts/test.sh` for your language/toolchain
3. Make scripts executable: `chmod +x scripts/*.sh`
4. Push — CI will run via the shared workflows

## Check Name Convention

All repos produce identical check names:

| Check | GitHub Status Name |
|-------|--------------------|
| Lint | `CI / Lint` |
| Build | `CI / Build` |
| Test | `CI / Test` |

This is achieved by the caller `ci.yml` using `name: CI` for each job, and the shared workflows using `Lint`, `Build`, `Test` as their job names.
