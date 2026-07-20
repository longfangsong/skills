---
name: code-review-spec
description: Review the changes since a fixed point (commit, branch, tag, or merge-base) against what the originating issue/PRD/spec asked for — missing requirements, scope creep, and wrong implementations. Use when the user wants to check that a branch, PR, or work-in-progress change matches its spec.
---

Review the diff between `HEAD` and a fixed point for one question only: **does the code faithfully implement the originating issue / PRD / spec?**.

## Process

### 1. Pin the fixed point

Whatever the user (or the calling skill) supplied is the fixed point — a commit SHA, branch name, tag, `main`, `HEAD~5`, etc. If none was supplied, ask for it.

Confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. Then capture:

- The diff: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base).
- The commit list: `git log <fixed-point>..HEAD --oneline`.

### 2. Identify the spec source

Look for a PRD/spec file under `docs/`, `specs/`, or `.scratch/` matching the branch name or feature.

DO NOT view the `tasks` files, spec checking should not consider implementation details in the `tasks` files.

### 3. Review the diff against the spec

Read the spec, then walk the diff requirement by requirement.

### 4. Report

Report:

- (a) requirements the spec asked for that are missing or partial;
- (b) behaviour in the diff that wasn't asked for (scope creep);
- (c) requirements that look implemented but where the implementation looks wrong.

Quote the spec line for each finding. Under 400 words.
