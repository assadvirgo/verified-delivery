# Project guide: <project name>

<One line: what the product is and who uses it.>

## Non-negotiable rules

<Numbered rules chosen at setup from the rule library, plus the two below, which every project using this workflow keeps.>

<N>. **A person verifies every feature.** Every slice produces a short manual checklist. A slice is not `done`, and nothing reaches production or real users, until a person has run its checklist and marked it passed. This is in addition to automated tests, never instead of them.
<N+1>. **Authorship wording.** Commit messages, pull requests, code comments, docs and changelogs describe the change only. They never name or allude to the tools used to write them and carry no generated trailers or footers. Every commit is authored and committed as `<owner name> <<owner email>>`. Only this guide and the `.claude/` folder are exempt from the wording check.
<N+2>. **Licences.** Dependencies must be under a licence on the allowlist: <list>. Anything else needs the owner's written approval before it is added.

High-risk areas: <money, ledgers, permissions, tenancy>. A slice touching one gets a short spec section from product-analyst and a hardening pass by qa-engineer within the slice.

## Team map

The main session is the **lead**. The lead plans, delegates, integrates and merges. It does not write feature code beyond one-line fixes.

| Role | Owns | Never touches |
| --- | --- | --- |
| product-analyst | `docs/specs/`, `docs/adr/` | source code |
| backend-engineer | <server paths, contract location, migrations> | <frontend and infra paths> |
| frontend-engineer | <client paths, shared UI> | <server paths, migrations> |
| qa-engineer | <e2e tests, test fixtures>, `docs/verification/`, missing tests anywhere | production code (reports defects) |
| platform-engineer | <infra, CI, container files, tooling config> | feature code |
| code-reviewer | nothing (read-only) | everything |

Contract location: <for example packages/schemas>. Demo seed: <command and location, or "none yet">.

## Task pipeline

Each task is a vertical slice: one flow working end to end, sized to land in one to three days, with an id (`<prefix>-0042`), a row in `docs/tasks/board.md` and a file `docs/tasks/<prefix>-0042.md` from `templates/task.md`.

1. **Plan** (under an hour): goal, designs, five to ten acceptance checks, contract notes. A short spec section only for high-risk areas or domain concepts later slices build on.
2. **Build and test**: contract first, then backend and frontend in parallel in their own worktrees, with unit, database and end-to-end tests for the main path and the most important refusal.
3. **Integrate**: the lead merges both branches into `task/<id>` and runs the full pipeline locally.
4. **Review**: code-reviewer reviews the full diff cold. Blocking findings go back to step 2.
5. **Merge**: green CI, merge, update board and changelog, add the checklist and its register row, set the task to `in test`.
6. **Batched human check**: people run waiting checklists <cadence>. Passed sets `done`; failed becomes fix tasks (`<id>-F1`) that go before new slices.
7. **Hardening**: qa-engineer within high-risk slices and for everything at the end of each milestone.

### Limits

- Fix tasks go before new slices.
- At most two agents write code at the same time, only on disjoint paths.
- An agent that needs something outside its paths stops and reports.
- At the start of every session, the lead processes recorded checklist results first.
- When a later slice changes screens covered by a waiting checklist, update that checklist in the same slice.

### Handoff report

Every role ends with this; the lead pastes it into the task file.

```
Task: <id>
Status: done | blocked | needs-decision
Branch: <branch>
Changed: <paths>
Tests: <added or updated, command run, result>
Contract changes: <none, or the exact change requested>
Open points: <questions, risks, follow-ups>
```

## Standing rules

Rules that apply to every role. Role-specific rules live in `.claude/team-rules/<role>.md`. Append only; never delete or renumber; mark old ones `(superseded by X)` or `(retired YYYY-MM-DD, reason)`. Use /add-rule.

<!-- ALL-001 onward. Format: - ALL-001 (date, task, source): rule -->

## Commands

- First time after cloning: `git config core.hooksPath .claude/git-hooks`
- Install: `<command>`  Run locally: `<command>`
- Lint: `<command>`  Typecheck: `<command>`
- Tests: unit `<command>`, database `<command>`, end-to-end `<command>`
- Licence check: `<command>`

## Definition of done

Acceptance checks covered by tests; lint, typecheck and every suite green; no dependency outside the allowlist; reviewer has no blocking findings; board and changelog updated; checklist written, run by a person and marked passed.
