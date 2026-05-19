---
name: to-prd
description: Produce a Product Requirements Document from the current conversation context. Use when the user wants a PRD for a feature, project, or initiative discussed in the current session.
---

This skill takes the conversation context and the codebase and produces a structured PRD. Do not interview the user — synthesize what you already know.

## Process

### 1. Understand the codebase

Explore the repo if you haven't already. Use the project's domain glossary throughout the PRD, and respect any existing ADRs that apply to the area you're working in.

### 2. Design the module structure

Sketch out the major modules you'd build or modify to deliver the feature. Look for opportunities to create **deep modules** — modules that encapsulate complex functionality behind a simple, stable, testable interface. (This is the opposite of a shallow module, which exposes implementation details and changes frequently.)

Before writing the PRD, **check with the user** that:

- The proposed module structure matches their expectations
- They want tests written for which modules

### 3. Write and save the PRD

Use the template below. Save the result to `docs/prd/` and use sequential numbering: `0001-slug.md`, `0002-slug.md`, etc.

## PRD Template

### Problem Statement

What problem is the user facing, described from the user's perspective — not the technical system.

### Solution

How the problem is solved, also from the user's perspective.

### User Stories

A comprehensive, numbered list of user stories covering every aspect of the feature. Format:

> As an <actor>, I want <feature>, so that <benefit>.

Write enough stories that someone could read the list and understand the full scope of the feature.

### Implementation Decisions

Concrete decisions made during planning. Include:

- Modules to build or modify, and their interfaces
- Architectural choices and their rationale
- Schema changes, API contracts, or type shape changes
- Specific interactions between modules

**Do not** include file paths or code snippets — they age poorly.

**Exception:** if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), include the relevant parts and note its origin. Trim to what's decision-critical — not a working demo.

### Testing Decisions

- What makes a good test for this feature (focus on external behaviour, not implementation details)
- Which modules will have tests
- Any relevant patterns or prior art already in the codebase

### Out of Scope

What is explicitly _not_ covered by this PRD.

### Further Notes

Anything else worth recording — open questions, known risks, dependencies, or things to revisit.
