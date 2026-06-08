---
name: validate-hypothesis
description: Validate a hypothesis about a bug.
---

# Validate Hypothesis

Test a hypothesis from `docs/bug/<bug_id>-<slug>/hypotheses/<seq>-<slug>.md`.

## Process

1. Add instrumentation (logs) to the relevant source files
2. Run the reproduction loop from `docs/bug/<bug_id>-<slug>/reproduction.md`
3. Check if the observed behavior matches the hypothesis prediction

## Tagging

Tag every debug log with the bug ID: `[DEBUG-<bug_id>]`

This enables one-command cleanup later: `grep -r "\[DEBUG-<bug_id>\]"` → remove. Untagged logs survive; tagged logs die.

## Outcome

### Hypothesis invalid

Remove all `[DEBUG-<bug_id>]` instrumentation. Ask for the user to move to the next hypothesis.

### Hypothesis valid

Write the full trace to `docs/bug/<bug_id>-<slug>/trace.md`:

- What happened, in order
- Related log output
- Root cause analysis

Then instruct the user to run `/fix`.
