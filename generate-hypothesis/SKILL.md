---
name: generate-hypothesis
description: Generate a ranked list of hypotheses about a bug before testing any of them.
---

# Generate Hypotheses

Run the reproduction steps from `docs/bug/<bug_id>-<slug>/` once, then dig into the codebase to produce **3–5 ranked hypotheses** about the cause of the bug.

## Hypothesis format

Each hypothesis must be **falsifiable**:

> "If <X> is the cause, then <changing Y> will make the bug disappear / <changing Z> will make it worse."

If you cannot state a prediction, discard or sharpen the hypothesis.

## Review

Present the ranked list to the user for review. They may have domain knowledge that re-ranks hypotheses instantly ("we just deployed a change to #3") or know which ones are already ruled out.

Do not generate output until the user has agreed on the list.

## Output

Write each hypothesis to `docs/bug/<bug_id>-<slug>/hypotheses/<seq>-<slug>.md`:

- Hypothesis text
- Falsifiable prediction

Then instruct the user to run `/validate-hypothesis` to test each one.
