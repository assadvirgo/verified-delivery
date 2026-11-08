---
name: run-task
description: This skill should be used when the user asks to "run the next task", "build T-0042", "start a slice", "plan and ship this feature", "act as the lead", or runs /run-task. It takes one vertical slice through the verified-delivery pipeline - plan, contract, parallel build with tests, integrate, cold review, merge, checklist and in test - with the main session as lead delegating to the role agents.
metadata:
  version: "0.1.0"
---

# Run one slice as the lead

Act as the **lead**: plan, delegate, integrate and merge. Do not write feature code beyond one-line fixes. Argument: a task id (`T-0042`), or a plain description of a new slice.

## 0. Start of session

1. Read the project guide (`CLAUDE.md`). If it has no team map, stop and suggest /setup-team.
2. Process recorded checklist results first, as /process-checks describes. If any fix task is open, build it before any new slice.
3. Pick the task: the one named, or the first fix task, or the next `todo` slice on the board.

## 1. Plan (under an hour)

Write `docs/tasks/<ID>.md` from `templates/task.md`, and add or update its board row (stage `building` when work starts):

- Goal in one or two sentences: what a person can do when it is merged.
- The designs it builds, if any.
- Five to ten acceptance checks in plain, observable words.
- Demo data the slice adds to the seed, by name.
- Contract notes: schemas, operations, permissions, tables.

Ask product-analyst for a short spec section only when the slice touches a high-risk area from the guide or introduces a domain concept later slices build on.

Ask the owner only about product behaviour or cost. Decide everything else, record it under Decisions, and proceed.

## 2. Contract first

Write or have backend-engineer write the shared schemas in the contract location from the team map, before either side builds. Commit them on `task/<ID>`.

## 3. Build and test, in parallel

Start backend-engineer on `task/<ID>-api` and frontend-engineer on `task/<ID>-web` at the same time, each in its own worktree. Limits:

- At most two agents writing code at once, on disjoint paths.
- One slice in build at a time, unless slices share no files and no tables.
- Platform-only tasks (pipeline, containers, infrastructure, tooling) go to platform-engineer alone and skip step 4's integration of two branches.

Every delegation message includes: the task file path, the branch, the paths the role owns, the test commands, and **every standing rule added during this session** (a running agent will not have re-read its rules file). Use `references/delegation-message.md`.

Each role must return the handoff report from the guide. Paste each one under Handoffs in the task file. A `needs-decision` report: decide if it is within the lead's remit, otherwise ask the owner. A contract change request: decide, update the contract, and tell both roles.

## 4. Integrate

Merge both branches into `task/<ID>` with a hand-written conventional message (for example `chore(web): integrate T-0042 shell`), never the default "Merge branch" text. Run the full local pipeline: lint, typecheck, every test suite, licence check. Failures go back to the owning role.

## 5. Review

Send code-reviewer the full diff of `task/<ID>` against the main branch. It has not seen the work. Blocking findings go back to step 3 for the owning role, then re-review. Non-blocking findings go under Follow-ups.

For a slice in a high-risk area, have qa-engineer run the hardening pass within the slice before review.

## 6. Merge

1. Open the pull request with a description of the change only. Wait for green CI. Merge.
2. Have qa-engineer (or the builder) write the 15-minute checklist from `templates/checklist.md` and add its register row with status `awaiting`. If this slice changed screens covered by another waiting checklist, update that checklist too.
3. Update the board (stage `in test`), the changelog, and "What's new for users" in the task file.
4. Record any new standing rule with /add-rule before moving on.

## 7. Report to the owner

Keep it short: what now works, the checklist waiting for a person, follow-ups, any decisions you took, and the ids of standing rules added.

A slice is `done` only after a person passes its checklist. Merging does not make it done.

## Hardening at milestone end

When the last slice of a milestone merges, have qa-engineer run a hardening pass across the whole milestone before any real user or pilot touches it.
