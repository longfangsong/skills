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

## Outcome

### Hypothesis invalid

Remove all `[DEBUG-<bug_id>]` instrumentation when the hypothesis is NOT valid. Ask for the user to move to the next hypothesis.

Please note you should do this ONLY WHEN the hypothesis is **invalid**: instrumentation can be useful in the later fix stage.

### Hypothesis valid

DO NOT remove `[DEBUG-<bug_id>]` instrumentation when the hypothesis is valid.

Write the full trace to `docs/bug/<bug_id>-<slug>/trace.md`:

- What happened, in order
- Related log output
- Root cause analysis

**Before**  suggest the user to run `/fix`, check: do **all** symptoms from the reproduction now pass? If some symptoms are fixed but others remain, the bug likely has multiple independent causes. Run `/decompose-bug` to split the remaining symptoms into a new child bug, then proceed.

If all symptoms are explained, instruct the user to run `/fix`.
