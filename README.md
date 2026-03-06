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

### `self-evolve.md` / `self-evolve.lock.yml` — Scheduled self-inspection (Agentic)
- **Trigger:** Every 6 hours (scattered cron) or manual via `workflow_dispatch`.
- **Engine:** GitHub Copilot (via [GitHub Agentic Workflows](https://github.com/github/gh-aw))
- **What it does:**  
  An AI agent triages open improvement issues, closing those whose conditions are resolved, then comprehensively inspects the repository — workflows, agents, documentation, code quality, and security — and creates labelled GitHub Issues assigned to `@Copilot` for each finding.
- **Deduplication:** The agent checks for existing open issues before creating duplicates.
- **Dry-run mode:** Trigger manually with `dry_run: true` to have the agent print findings without creating or closing any issues.

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

### `repository-quality-improver.md` — Daily AI quality analysis (Agentic)
- **Trigger:** Daily on weekdays (scattered cron) or manual via `workflow_dispatch`.
- **Engine:** GitHub Copilot (via [GitHub Agentic Workflows](https://github.com/github/gh-aw))
- **What it does:**  
  An AI agent selects a rotating focus area (code quality, documentation, testing, security, CI/CD, dependencies, etc.) and produces a single GitHub issue with targeted, actionable improvement tasks.

---

## Prerequisites

### `COPILOT_GITHUB_TOKEN` Secret

The agentic workflows (`self-evolve` and `repository-quality-improver`) require a
**`COPILOT_GITHUB_TOKEN`** repository secret to authenticate with the GitHub Copilot API.
Without a valid token the agent step will fail with:

```
Error: Authentication failed
Your GitHub token may be invalid, expired, or lacking the required permissions.
```

**How to create and configure the secret:**

1. Create a **GitHub Personal Access Token (Classic)** or **Fine-Grained PAT**:
   - **Classic PAT** (simplest): Go to **Settings → Developer settings → Personal access tokens → Tokens (classic)** and generate a new token.  
     No specific OAuth scopes are required — just having a Copilot-enabled account is sufficient.
   - **Fine-Grained PAT**: Go to **Settings → Developer settings → Personal access tokens → Fine-grained tokens** and create a new token.  
     Enable the **"Copilot Requests"** permission under *Account permissions*.
2. Ensure the GitHub account that owns the token has an active **GitHub Copilot** subscription (Individual, Business, or Enterprise).
3. Add the token as a repository secret named **`COPILOT_GITHUB_TOKEN`**:  
   Go to **Settings → Secrets and variables → Actions → New repository secret**.

**Important:** If the token expires or is rotated, update the secret before the next scheduled workflow run.

---

## GitHub Agentic Workflows (gh-aw)

The `self-evolve` and `repository-quality-improver` workflows are implemented using
[GitHub Agentic Workflows](https://github.com/github/gh-aw) — a system for writing
AI-powered automation in natural language Markdown and running it in GitHub Actions.

Each agentic workflow has two files:
- **`.md` source** — human-readable natural language instructions for the AI agent
- **`.lock.yml`** — compiled GitHub Actions YAML generated by `gh aw compile` (do not edit directly)

To add or update agentic workflows, install the CLI extension:

```bash
curl -sL https://raw.githubusercontent.com/github/gh-aw/main/install-gh-aw.sh | bash
gh aw compile   # recompile after editing .md files
```

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

## License

This project is licensed under the [MIT License](LICENSE).

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
│   ├── agents/
│   │   └── agentic-workflows.agent.md  ← gh-aw dispatcher agent (Copilot Chat)
│   ├── copilot-instructions.md         ← Agent behaviour rules (self-modifiable)
│   ├── ISSUE_TEMPLATE/
│   │   └── agentic-improvement.yml     ← Structured issue template
│   └── workflows/
│       ├── self-evolve.md              ← AI-powered self-inspection (gh-aw source)
│       ├── self-evolve.lock.yml        ← Compiled GitHub Actions YAML (do not edit)
│       ├── repository-quality-improver.md      ← Daily quality analysis (gh-aw)
│       ├── repository-quality-improver.lock.yml
│       ├── auto-merge.yml              ← PR approval + auto-merge
│       ├── ci-monitor.yml              ← CI failure → issue → fix cycle
│       └── copilot-setup-steps.yml     ← gh-aw environment setup for Copilot
│
├── .gitattributes                      ← Marks .lock.yml as generated
├── LICENSE                             ← MIT License
└── README.md                           ← This file
```

**Improvement cycle:**

```
self-evolve (AI agent) inspects repo
       ↓
Creates GitHub Issue (labelled agentic, assigned @Copilot)
       ↓
Copilot coding agent opens a PR with the fix
       ↓
auto-merge.yml approves + enables auto-merge
       ↓
All CI checks pass → GitHub merges the PR automatically
       ↓
self-evolve agent closes the issue on next run
       ↓  (repeat every 6 hours)
```
