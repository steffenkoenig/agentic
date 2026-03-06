# Contributing to agentic

Thank you for your interest in contributing! This document explains how the project works and how you can participate — whether you are a human collaborator or an autonomous agent.

---

## How This Repository Works

`agentic` is a **self-evolving repository**. A scheduled AI agent (powered by [GitHub Agentic Workflows](https://github.com/github/gh-aw)) continuously inspects the repository, opens improvement issues, and implements fixes — all without human interaction. The improvement cycle looks like this:

```
self-evolve (AI agent) inspects repo
       ↓
Creates GitHub Issue  (labelled `agentic`, assigned @Copilot)
       ↓
Copilot coding agent opens a PR with the fix
       ↓
auto-merge.yml approves + enables auto-merge
       ↓
All CI checks pass → GitHub merges the PR automatically
       ↓
self-evolve agent closes the issue on the next run
       ↓  (repeat every 6 hours)
```

---

## Reporting Bugs

1. Search [existing issues](https://github.com/steffenkoenig/agentic/issues) to avoid duplicates.
2. Open a new issue and describe:
   - What you expected to happen.
   - What actually happened (include relevant log lines or screenshots).
   - Steps to reproduce the issue.
3. Add the relevant label (e.g. `ci-failure`, `agentic`) if you have permission to do so.

---

## Proposing Improvements

Use the **[Agentic Improvement issue template](ISSUE_TEMPLATE/agentic-improvement.yml)** for any improvement that falls into one of these categories:

| Category | Examples |
|---|---|
| `documentation` | Missing docs, outdated README sections |
| `testing` | New test coverage, flaky tests |
| `workflow` | New or updated GitHub Actions workflows |
| `code quality` | Refactoring, style, naming |
| `dependency` | Updating or adding packages |
| `security` | Vulnerability fixes, permission hardening |
| `performance` | Faster CI, reduced resource usage |
| `other` | Anything else |

The template requires a **Description** and **Acceptance Criteria** checklist — fill both in so the agent (or a human reviewer) can determine when the issue is done.

---

## Submitting Pull Requests

1. **Fork** the repository and create a feature branch off `main`.
2. Keep each PR **focused** — one concern per PR.
3. Fill in the [pull request template](pull_request_template.md).
4. Ensure all CI checks pass before requesting a review.
5. Resolve merge conflicts by rebasing on `main`:
   ```bash
   git fetch origin
   git rebase origin/main
   ```
6. PRs labelled `agentic` or authored by a Copilot bot are auto-merged once checks pass; human PRs follow the normal review process.

### Coding Conventions

- Make **minimal, targeted** changes. Do not refactor unrelated code.
- Do not leave `TODO` or `FIXME` comments — fix the issue now or open a tracking issue.
- Update documentation whenever you change a public interface or behaviour.
- Never commit secrets, tokens, or credentials.
- Use minimal permissions in workflow `permissions:` blocks.

---

## Agentic Workflow Development

### Editing Agentic Workflows

The `self-evolve` and `repository-quality-improver` workflows are written in natural-language Markdown and compiled to GitHub Actions YAML using the `gh aw` CLI.

**Never edit `.lock.yml` files directly.** Always edit the `.md` source and recompile:

```bash
# Install the gh-aw CLI extension (one-time setup)
curl -sL https://raw.githubusercontent.com/github/gh-aw/main/install-gh-aw.sh | bash

# Edit the source file, then recompile
gh aw compile
```

Commit both the updated `.md` and the regenerated `.lock.yml` together.

### Running Workflows Locally

GitHub Actions workflows cannot be run directly on your local machine without additional tooling. The recommended approach is:

1. **Dry-run mode (recommended):** Trigger `self-evolve` manually from the Actions tab with `dry_run: true`. The agent will print its findings without creating or closing any issues.

2. **`act` (local runner):** Install [act](https://github.com/nektos/act) to run standard GitHub Actions workflows locally:
   ```bash
   act -W .github/workflows/auto-merge.yml
   ```
   Note: Agentic (gh-aw) workflows require a valid `COPILOT_GITHUB_TOKEN` and a Copilot-enabled account; they cannot be fully replicated offline.

3. **YAML validation:** Validate workflow YAML syntax before committing:
   ```bash
   # Using the GitHub CLI
   gh workflow list
   # Or use a YAML linter, e.g. yamllint
   yamllint .github/workflows/
   ```

### Required Secret

The agentic workflows require a `COPILOT_GITHUB_TOKEN` repository secret (a Personal Access Token from a GitHub account with an active Copilot subscription). See the [README](../README.md#prerequisites) for setup instructions.

---

## Agent Behaviour Rules

The Copilot coding agent's behaviour is governed by [`.github/copilot-instructions.md`](copilot-instructions.md). If you believe the agent is behaving incorrectly or missing an important pattern, open an issue or a PR to update those instructions.

---

## Labels

| Label | Description |
|---|---|
| `agentic` | All issues/PRs created by the agentic system |
| `ci-failure` | CI failure issues created by `ci-monitor.yml` |

---

## Questions

Open a [GitHub Discussion](https://github.com/steffenkoenig/agentic/discussions) or file an issue if you have questions not covered here.
