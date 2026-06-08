---
name: reproduce-bug
description: First step in the bug diagnosis loop, reproduce the bug. Use when the user reports a bug or says something is broken/throwing/failing.
---

# Reproduce A Bug

Build a reliable reproduction loop. Spend the most effort here.

## Checklist

Confirm:

- [ ] Reproduction triggers the **exact** failure the user described
- [ ] Failure is reproducible across multiple runs (or reproducible at a high enough rate for non-deterministic bugs)
- [ ] Exact symptom captured (error message, wrong output, slow timing)

## Reproduction strategies (try in order)

1. **Failing test** — unit, integration, or e2e at the seam closest to the bug
2. **CLI invocation** — fixture input, diff stdout against known-good
3. **Script** — shell/python/NodeJS (use Playwright for frontend)

## Sharpen the loop

Once you have a working loop, improve it:

- **Faster?** Cache setup, skip unrelated init, narrow scope
- **Sharper?** Assert on the specific symptom, not "didn't crash"
- **More deterministic?** Pin time, seed RNG, isolate filesystem, freeze network

## If you cannot reproduce

Stop. List what you tried. Ask the user for:

- Access to a reproducing environment, or
- A captured artifact (HAR file, log dump, core dump, screen recording with timestamps), or
- Permission to add temporary production instrumentation

## Output

**After** the reproduction loop is set and you cannot improve it any more, create `docs/bug/<bug_id>-<slug>/` (sequential numbering, e.g. `0001-slug`).

Contents:

- `reproduction.md` — bug description and reproduction steps
- `*.sh` / `*.py` / `*.test.*` — reproduction scripts (or inline commands in the markdown)

Then instruct the user to run `/decompose-bug` or `/generate-hypothesis` if the user thinks the bug has a single cause.
