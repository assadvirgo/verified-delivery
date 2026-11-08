# verified-delivery

**Your AI dev team, with a definition of done.**

A lead agent and six specialist roles that ship software in small vertical slices, with a person's sign-off before anything is called done.

## What it gives you

**Roles (agents)**

| Role | Does |
| --- | --- |
| product-analyst | Short spec sections and decision records for high-risk slices; no code |
| backend-engineer | Server code, contract schemas, migrations, with unit, database and API tests |
| frontend-engineer | Screens from the designs and the shared contract, with component tests |
| qa-engineer | The 15-minute checklist for people; hardening passes; reports defects, never fixes code |
| platform-engineer | Pipelines, containers, infrastructure, tooling; stops for approval on cost |
| code-reviewer | Cold, read-only review of the full diff; returns approve or blocking findings |

The main session is the **lead**: it plans, delegates, integrates and merges.

**Skills**

| Skill | Use it to |
| --- | --- |
| /setup-team | Install the workflow in a repository: project guide, rules, board, templates, check register, git hooks, licence allowlist |
| /run-task | Take one slice through plan → contract → parallel build → integrate → review → merge → checklist → in test |
| /add-rule | Record a standing rule (`BE-007`) with its changelog row and its own commit |
| /process-checks | Read people's checklist results, mark slices done, open fix tasks that go first |

## The pipeline

1. **Plan** in under an hour: goal, designs, five to ten acceptance checks, contract notes.
2. **Build and test** in parallel from a shared contract, each role in its own worktree.
3. **Integrate** and run the full pipeline locally.
4. **Review** cold; blocking findings go back to build.
5. **Merge** on green CI; a short checklist joins the register; the slice is `in test`.
6. **People check** waiting slices in batches; passed means `done`, failed means fix tasks first.
7. **Harden** risky slices within the slice, and everything at the end of each milestone.

## Safeguards

- **Human sign-off gate**: nothing is done, or released, until a person passes its checklist.
- **Standing rules**: numbered, append-only rules per role and for everyone, each committed on its own and traceable with `git log -S`.
- **Authorship wording**: commit and push hooks refuse messages, added lines and author or committer identities that name the tools used to write the code; commits use the owner's identity; attribution text is turned off.
- **Licence allowlist**: permissive licences only for anything that ships, enforced in review and, once wired, in CI.

## Where things live

The plugin holds the process. Everything a project learns lives in its own repository:

- `CLAUDE.md`: non-negotiable rules, team map, commands, rules for everyone
- `.claude/team-rules/<role>.md`: each role's standing rules
- `.claude/ROLE_CHANGELOG.md`, `.claude/git-hooks/`
- `docs/tasks/`, `docs/specs/`, `docs/adr/`, `docs/verification/`, `templates/`

## Getting started

1. Install the plugin:

   ```
   /plugin install verified-delivery --marketplace assadvirgo/verified-delivery
   ```

2. In your repository, run /setup-team and answer a few questions.
3. Describe the first slice and run /run-task.

## Requirements

Git and Bash for the hooks. Agent worktrees need a git repository with at least one commit.
