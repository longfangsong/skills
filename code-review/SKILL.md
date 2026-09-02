---
name: code-review
description: Review the changes since a fixed point (commit, branch, tag, or merge-base) against a fixed baseline. Use when the user wants a review of a branch, a PR, or work-in-progress changes.
---

Spawn three sub agents to run skills `code-review-arch`, `code-review-standard`, and `code-review-spec` in parallel, then combine their findings into a single report.

Please DO NOT read these skills by yourself; they are self-contained and will be run in their own agents. Just spawn them with necessary information (review from which fixed point) and combine their findings.
