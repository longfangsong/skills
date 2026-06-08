---
title: Fix
description: Fix a bug validated by the validate-hypothesis skill.
---

# Fix

## Process

1. Read `docs/bug/<bug_id>-<slug>/trace.md` to understand the root cause
2. Add a regression test (or locate an existing one) **before** making any code changes
3. Confirm the test fails
4. Apply the fix
5. Confirm the regression test passes

## Cleanup

Remove all `[DEBUG-<bug_id>]` instrumentation (`grep -r "\[DEBUG-<bug_id>\]"`).

## Architecture review

After the fix, ask: **what would have prevented this bug?**

If the answer involves an architectural change (no good test seam, tangled callers, hidden coupling), run `/improve-codebase-architecture` with the specifics. Make this recommendation **after** the fix is in — you now have more context than when you started.
