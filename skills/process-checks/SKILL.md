---
name: process-checks
description: This skill should be used at the start of every lead session, and when the user says "testers have finished", "process the checklist results", "what's waiting for testing", "prepare this week's test session", or runs /process-checks. It reads the verification register and checklist files, marks passed slices done, turns failures into fix tasks that go first, and lists what is waiting for people.
---

# Process human checklist results

A slice becomes `done` only when a person passes its checklist. This skill keeps the board, the register and the checklists in step.

## 1. Read

- `docs/verification/README.md` (the register) and every checklist it lists that is not `passed` or `not applicable`.
- `docs/tasks/board.md`.
- Pull the main branch first if testers commit their results.

For each checklist, take its result from the sign-off table and the Pass column, not only from the register row: testers sometimes update one and not the other.

## 2. Act on each result

**Passed**: set the register row to `passed` with tester, date and build; set the task's stage and its Check line to `passed`/`done` in the task file and the board.

**Failed**, or any line marked fail, or any row under "Problems found":

1. Create one fix task per distinct problem: `<ID>-F1`, `<ID>-F2`..., each from `templates/task.md`, with the tester's words, the failing check number, severity and the build tested.
2. Add each to the "Fix tasks" table at the top of the board. Fix tasks are built before any new slice.
3. Set the original task back to `in test` with Check `failed`, and link its fix tasks under Defects.
4. When a fix merges, set the affected checklist rows to `retest` in the register rather than writing a new checklist.

**Problem with the checklist itself** (a step that could not be followed, wrong wording): fix the checklist, and set it back to `awaiting`.

**In progress or partial**: leave it, and note which lines remain.

Commit the updates as the owner, for example `docs(tasks): record checklist results for T-0042 and T-0043`.

## 3. Prepare the next session

List every checklist with status `awaiting` or `retest`, with its time estimate and the demo data it needs, grouped so testers can run them in one sitting. Note any that cover screens changed since they were written: those must be updated before the session.

## 4. Report to the owner

- Done since last time.
- Fix tasks opened, with severity, in the order they will be built.
- Waiting for people: count and total time.
- Anything that blocks a release: a slice is never released to production or real users until its checklist has passed.
