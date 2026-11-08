---
name: add-rule
description: This skill should be used when the owner, a review finding or a defect produces a rule, convention or skill the team must keep - for example "remember this for the backend", "make that a standing rule", "add a rule that...", "the team should always...", or /add-rule. It records the rule with a traceable id, logs it, commits it and tells the owner.
---

# Record a standing rule

The repository's rules files are the team's permanent memory. Record a rule immediately, before continuing the current task.

## 1. Decide the scope

| The rule applies to | Write it in | Prefix |
| --- | --- | --- |
| one role | `.claude/team-rules/<role>.md` | `PA`, `BE`, `FE`, `QA`, `PE`, `CR` |
| every role | the `Standing rules` section of `CLAUDE.md` | `ALL` |
| the product itself (a design or policy decision) | a decision record in `docs/adr/`, plus a one-line `ALL` rule pointing to it | `ALL` |

An instruction for the current task only goes in the task file instead. If it is unclear whether an instruction is standing or one-off, ask the owner.

An exception to a non-negotiable rule needs the owner's explicit approval and a decision record before it is written as a rule.

## 2. Write one entry

Find the next free number for the prefix in `.claude/ROLE_CHANGELOG.md`. Append under the file's standing rules:

```
- BE-007 (2026-11-03, T-0107, owner): Lock the drawer session row before inserting a cash movement.
```

Date, task id (or `none`), source (`owner`, `review finding`, `defect`, `lead`, `analyst`), then the rule as one clear, checkable sentence.

## 3. Skills

A repeatable procedure longer than a few lines becomes a skill: create `.claude/skills/<name>/SKILL.md`, list `<name>` under "Skills for this role" in the role's rules file, and add a standing rule saying when to use it.

## 4. Log it

Append a row to `.claude/ROLE_CHANGELOG.md`: date, id, role, task, source, short summary.

## 5. Commit on its own

Commit only those files, straight to the main branch, as the owner, with a message naming the id:

```
chore(team): add BE-007 drawer locking (T-0107)
```

## 6. Never delete or renumber

When a rule no longer applies, mark it in place: `(superseded by BE-012)` or `(retired YYYY-MM-DD, reason)`, and log the change.

## 7. Pass it on and report

- Include the new rule in every delegation message for the rest of this session.
- Tell the owner in the next report which ids were added.

To trace a rule later: `git log -S "BE-007" -- .claude CLAUDE.md`.
