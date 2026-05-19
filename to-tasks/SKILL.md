---
name: to-tasks
description: Break a plan, spec, or PRD into independently-grabbable tasks using tracer-bullet vertical slices, one document per slice. Use when user wants to convert a plan into tasks, create implementation tickets, or break down work into tasks.
---

# To Tasks

Break a plan into independently-grabbable tasks using vertical slices (tracer bullets).

## Process

### 1. Gather context

Work from whatever is already in the conversation. If the user passes a task/prd reference (number, URL, or path), read its full body.

### 2. Explore the codebase

Explore the codebase to understand the current state. Use the project's domain glossary in task titles and descriptions. Respect ADRs in the area you're touching.

### 3. Draft vertical slices

Break the plan into **tracer bullet** tasks — each a thin vertical slice cutting through ALL layers end-to-end (schema, API, UI, tests). Never produce horizontal slices.

- **AFK**: can be implemented and merged without human interaction.
- **HITL**: requires human interaction (architectural decision, design review).

Prefer AFK over HITL where possible.

<vertical-slice-rules>
- Each slice delivers a narrow but **complete** path through every layer
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
</vertical-slice-rules>

### 4. Present for review

Show the proposed breakdown as a numbered list. For each slice, show:

- **Title** — short descriptive name
- **Type** — HITL / AFK
- **Blocked by** — which other slices must complete first (or "None")
- **User stories covered** — which user stories this addresses (if source material has them)

Ask the user:

- Granularity: too coarse, too fine, or just right?
- Dependencies: correct? any missing blockers?
- Merge or split any slices?
- HITL/AFK assignments correct?

Iterate until approved.

### 5. Publish tasks

For each approved slice, write a task doc in the project docs path (`docs/` by default). These tasks are ready for AFK agents.

Publish in **dependency order** (blockers first) so you can reference real task identifiers in the "Blocked by" field.

<task-template>
## Parent

Reference to the parent task (omit if none).

## What to build

Concise description of the vertical slice. Describe end-to-end behavior, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose (state machine, reducer, schema, type shape), inline it and note briefly that it came from a prototype. Trim to the decision-rich parts only.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

Reference to the blocking task (or "None - can start immediately").

</task-template>

Do NOT close or modify any parent task.
