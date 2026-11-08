---
name: setup-team
description: This skill should be used when the user asks to "set up the team", "set up verified delivery", "bootstrap the agent team in this repo", "add the lead and roles to this project", or runs /setup-team. It installs the team workflow into a repository - project guide with non-negotiable rules and team map, standing-rules files, task board, templates, verification register, wording and identity git hooks, and the licence allowlist.
---

# Set up the verified-delivery team in a repository

Install the workflow so the main session can act as the lead and delegate to the plugin's six roles. Everything the team learns later lives in the repository, not in the plugin.

Plugin files referenced below live under `${CLAUDE_PLUGIN_ROOT}/skills/setup-team/`.

## 1. Inspect the repository

- Confirm it is a git repository; if not, ask before running `git init`.
- Detect the stack: package manager and lockfile, languages, test runners, existing CI, monorepo layout.
- Read any existing `CLAUDE.md`, `.claude/` folder, `docs/` and `templates/`. Never overwrite them: merge, and show the owner what changes.
- Infer the folders each role will own from the layout (for example `apps/api` → backend, `apps/web` → frontend, `infra/` and `.github/` → platform).

## 2. Ask the owner what cannot be detected

Use AskUserQuestion, at most four questions per call, and skip anything already known:

1. One line on what the product is and who uses it.
2. The owner's git identity for every commit (name and email). Agents never commit under a tool identity.
3. Starting non-negotiable rules. Offer the common set from `references/rule-library.md` that fits the project type (for example multi-tenant SaaS, payments, public API) and let the owner choose.
4. Which areas count as high risk (default: money, ledgers, permissions, tenancy). Slices touching them get a short spec section and an in-slice hardening pass.
5. How often people run the waiting checklists (default: at least weekly, or at each demo).
6. Licence allowlist (default in `references/licences.md`).

## 3. Write the files

Create or merge, filling every `<placeholder>` from steps 1 and 2:

| Target in the repository | Source |
| --- | --- |
| `CLAUDE.md` (add a section, or create) | `references/project-guide-template.md` |
| `.claude/team-rules/<role>.md` for the six roles | `references/team-rules-file.md`, one per role with its prefix |
| `.claude/ROLE_CHANGELOG.md` | `references/templates/role-changelog.md` |
| `.claude/settings.json` (merge) | set `"attribution": {"commit": "", "pr": ""}` |
| `.claude/git-hooks/commit-msg`, `pre-push`, `wording-patterns.txt` | write from `references/git-hooks.md` and `references/wording-patterns.txt`; `chmod +x` the two hooks |
| `docs/tasks/board.md` | `references/templates/board.md` |
| `templates/task.md`, `templates/checklist.md`, `templates/spec.md` | `references/templates/` |
| `docs/verification/README.md` | `references/templates/verification-register.md` |
| `docs/verification/ENVIRONMENT.md` | a stub: where to test, accounts, demo data, reset |
| `docs/adr/NNNN-team-workflow.md` | a short decision record that the project adopts this workflow, next free number |

Role prefixes: `PA` product-analyst, `BE` backend-engineer, `FE` frontend-engineer, `QA` qa-engineer, `PE` platform-engineer, `CR` code-reviewer, `ALL` the project guide.

## 4. Wire the safeguards

- Run `git config core.hooksPath .claude/git-hooks`, and add that line to the guide's Commands section for anyone who clones later.
- With the owner's confirmation, set the repository-local identity: `git config user.name` and `git config user.email`.
- Pick the licence check for the stack from `references/licences.md` and record its command in the guide. If the project has no tooling for it yet, add a task to the board for platform-engineer.
- If the project has a commit-message linter, keep it: the hook runs the wording check first.

## 5. Check the setup

- Test the hook with a throwaway message file: one containing a forbidden word must be refused, a clean one accepted. Do not create a real commit for this.
- Confirm the plugin's agents are available and that each `.claude/team-rules/*.md` exists.
- Commit the setup as the owner with a message like `chore(team): add delivery workflow, roles and safeguards`.

## 6. Report to the owner

In plain words: what was installed, the rules chosen, the hook test result, and the next step: describe the first slice and run /run-task.
