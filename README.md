# agentic

A **self-evolving, fully agentic repository** powered by GitHub Copilot's coding agent.  
The repository continuously inspects itself, identifies improvements, and implements them — autonomously, without human interaction.

---

## What It Does

| Capability | Description |
|---|---|
| 🔍 **Self-inspection** | A scheduled workflow analyses the repository every 6 hours and creates GitHub Issues for anything that should be improved. |
| 🤖 **Autonomous implementation** | GitHub Copilot coding agent picks up those issues, opens pull requests with fixes, and closes the issues on completion. |
| ✅ **Auto-merge** | Copilot-authored PRs are automatically approved and merged once all CI checks pass. |
| 🚨 **CI monitoring** | Whenever a CI run fails, an issue is created automatically so Copilot can diagnose and fix the root cause. |
| 🔄 **Self-modifying** | The agent is instructed to improve the workflows, documentation, tests, and even these instructions themselves. |

---

## Workflows

### `self-evolve.yml` — Scheduled self-inspection
- **Trigger:** Every 6 hours (`cron: '0 */6 * * *'`) or manual via `workflow_dispatch`.
- **What it does:**  
  Checks the repository for common improvement opportunities (thin documentation, missing configuration files, TODO comments, insecure workflow permissions, missing lock files, etc.) and creates labelled GitHub Issues assigned to `@Copilot` for each finding.
- **Deduplication:** Issues are only created once — re-runs skip titles that already have an open issue.
- **Dry-run mode:** Trigger manually with `dry_run: true` to preview what issues would be created without actually creating them.

### `auto-merge.yml` — Autonomous PR merging
- **Trigger:** `pull_request` events (opened, synchronised, reopened, ready-for-review) and `check_suite` completion.
- **What it does:**  
  When a PR is opened by a Copilot bot account or carries the `agentic` label, the workflow:
  1. Approves the PR.
  2. Enables squash auto-merge — GitHub merges it automatically once all required status checks pass.
- **Merge conflict handling:** If a PR has merge conflicts, a comment is posted on the PR instructing the author to rebase.

### `ci-monitor.yml` — CI failure tracking
- **Trigger:** `workflow_run` completion events for any workflow in the repository.
- **On failure:** Creates an `agentic` + `ci-failure` labelled issue assigned to `@Copilot` with a link to the failing run.  
  If an issue for the same workflow/branch already exists, a comment with the new run URL is appended instead.
- **On recovery:** When the workflow turns green, any open failure issue for that workflow/branch is automatically closed.

---

## Agent Instructions

The Copilot coding agent's behaviour is controlled by [`.github/copilot-instructions.md`](.github/copilot-instructions.md).  
It defines:
- Core responsibilities and behaviour rules.
- Conventions for issues, pull requests, and workflow changes.
- Security and quality standards.
- The expected repository structure.

---

## Labels

| Label | Description |
|---|---|
| `agentic` | Applied to all issues and PRs created by the agentic system. |
| `ci-failure` | Applied to CI failure issues created by `ci-monitor.yml`. |

---

## Triggering Self-Evolution Manually

Go to **Actions → Self-Evolve → Run workflow** and click **Run workflow**.  
Set `dry_run` to `true` to preview what issues would be created without side effects.

---

## Architecture

```
Repository
│
├── .github/
│   ├── copilot-instructions.md      ← Agent behaviour rules (self-modifiable)
│   ├── ISSUE_TEMPLATE/
│   │   └── agentic-improvement.yml  ← Structured issue template
│   └── workflows/
│       ├── self-evolve.yml          ← Inspection + issue creation (scheduled)
│       ├── auto-merge.yml           ← PR approval + auto-merge
│       └── ci-monitor.yml           ← CI failure → issue → fix cycle
│
└── README.md                        ← This file
```

**Improvement cycle:**

```
self-evolve.yml inspects repo
       ↓
Creates GitHub Issue (labelled agentic, assigned @Copilot)
       ↓
Copilot coding agent opens a PR with the fix
       ↓
auto-merge.yml approves + enables auto-merge
       ↓
All CI checks pass → GitHub merges the PR automatically
       ↓
Copilot closes the issue
       ↓  (repeat every 6 hours)
```
