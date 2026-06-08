---
name: decompose-bug
description: Break a multi-cause bug into independent sub-bugs. Use after reproduce-bug, or from validate-hypothesis when a confirmed cause only partially explains the symptoms.
---

# Decompose A Bug

Determine whether all symptoms of a bug share a single root cause, or cluster into **independent** problems that should be tracked and fixed separately.

## When to decompose

Decompose when **all** of the following hold:

- The reproduction triggers **two or more distinct symptoms** (different error messages, different wrong outputs, failures in different modules/layers).
- The symptoms **do not depend on each other** — symptom B is not caused by symptom A breaking a precondition. If B only appears because A failed first, they are **chained**, not independent. Fix A, re-run, and see if B survives.
- Each symptom can be **reproduced in isolation** (or plausibly can be with a narrower reproduction).

If any condition fails, the bug is single-cause. Skip decomposition and proceed to `/generate-hypothesis` on the original bug.

## Symptom clustering

Group symptoms by any of these signals:

- **Code path** — different functions, files, or modules
- **Layer** — frontend vs backend, database vs application logic, network vs local computation
- **Trigger** — different inputs, different user actions, different edge cases

Symptoms that share the same code path and layer are likely a single bug. Symptoms that span paths or layers are candidates for decomposition.

## Process

1. Read `docs/bug/<bug_id>-<slug>/reproduction.md` and list every distinct symptom.
2. For each symptom, identify the likely code path or layer responsible.
3. Cluster symptoms that share a code path / layer into the same sub-bug.
4. For each cluster, verify it can be reproduced in isolation (narrow the reproduction input, comment out unrelated steps, etc.).
5. If decomposition is warranted, create the new top-level bug folders and delete the original. If not, skip to output.

## Output

### Single-cause bug (no decomposition)

Instruct the user to run `/generate-hypothesis`.

### Multi-cause bug (decomposed)

Create a **top-level** bug folder for each independent sub-bug, using the next available sequential IDs:

```
docs/bug/0002-cart-validation/
  reproduction.md
docs/bug/0003-tax-calculation/
  reproduction.md
docs/bug/0004-api-timeout/
  reproduction.md
```

Each folder contains:

- `reproduction.md` — narrowed reproduction targeting **only** this sub-bug's symptom(s)

Then **delete** the original bug folder (`docs/bug/<bug_id>-<slug>/`).

Instruct the user to run `/generate-hypothesis` for each sub-bug.

## Rules

- **Causal independence is mandatory** — if symptom B only manifests because symptom A broke something first, do not decompose.
- **Minimal reproductions** — each new bug's reproduction should be narrower than the original. Remove steps unrelated to its symptom.
- **No decomposition is valid** — if everything points to one root cause, the pass-through path is the correct outcome.
