---
name: tdd-task
description: Run the TDD red-green-refactor loop against a task document generated with `to-tasks`. Reads the task doc, creates a dedicated git branch, plans, then iterates RED→GREEN until all acceptance criteria are satisfied. Use when user provides a task doc from `to-tasks` and wants it implemented autonomously via TDD.
---

# TDD Task

Run the TDD red-green-refactor loop until a `to-tasks` task document is fully accomplished — autonomously, without requiring user approval.

## Philosophy

**Core principle**: Tests should verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't.

**Good tests** are integration-style: they exercise real code paths through public APIs. They describe _what_ the system does, not _how_ it does it. A good test reads like a specification — "user can checkout with valid cart" tells you exactly what capability exists. These tests survive refactors because they don't care about internal structure.

**Bad tests** are coupled to implementation. They mock internal collaborators, test private methods, or verify through external means (like querying a database directly instead of using the interface). The warning sign: your test breaks when you refactor, but behavior hasn't changed. If you rename an internal function and tests fail, those tests were testing implementation, not behavior.

## Prerequisites

This skill expects a task document produced by the `to-tasks` skill. If no task doc is provided, ask the user for the path or let them pick from `docs/`.

## Workflow

### 0. Read & Understand the Task

Read the task document. Extract:

- **What to build** — the end-to-end behavior description
- **Acceptance criteria** — the checklist of verifiable outcomes
- **Blocked by** — any dependencies; confirm they are satisfied before starting
- **Parent** — any parent task for context

### 1. Ensure the Git working tree is clean

Before making any code changes:

If the current Git repository has uncommitted changes, create a commit with the message "before XXX".

### 2. Plan (Autonomous)

Before writing any code:

- [ ] Explore the codebase to understand current state
- [ ] Use the project's domain glossary — test names and interfaces must match the project's language
- [ ] Respect ADRs in the area being touched
- [ ] Map each acceptance criterion to a test (or group of tests)
- [ ] Order tests so earlier ones exercise simpler paths (tracer-bullet style)
- [ ] Design interfaces for testability and [deep modules](deep-modules.md)
- [ ] Design interfaces for [testability](interface-design.md)

No user approval needed — proceed directly into the loop.

### 3. Execute the TDD Loop

Iterate until **all** acceptance criteria are satisfied:

```
RED:   Write the next test → verify it fails
GREEN: Write minimal code to pass → verify it passes
```

Rules:

- One test (or one small group of tightly related tests) at a time
- Only enough code to pass the current test
- Never refactor while RED — get to GREEN first
- Tests verify behavior through public interfaces, not implementation details
- No speculative features
- Do **NOT** include the task name or id in test name

Each acceptance criterion should be marked `[x]` as its corresponding test(s) pass.

### 4. Refactor

After all tests pass and all acceptance criteria are checked:

- [ ] Extract duplication
- [ ] Deepen modules (move complexity behind simple interfaces)
- [ ] Apply SOLID principles where natural
- [ ] Run tests after every refactor step
- [ ] Ensure no acceptance criterion regressed

### 5. Commit

After completing **ALL** the changes, create a commit with a clear and meaningful commit message.

### 6. Report

Present the final state:

- All acceptance criteria checked off
- What was built (end-to-end summary)
- Any open questions or follow-ups

## Checklist Per Cycle

```
[ ] Test describes behavior, not implementation
[ ] Test uses public interface only
[ ] Test would survive internal refactor
[ ] Code is minimal for this test
[ ] No speculative features added
```

## Loop Termination

The loop ends when **every** acceptance criterion in the task document has a passing test. Do not stop early. Do not add acceptance criteria mid-stream.
