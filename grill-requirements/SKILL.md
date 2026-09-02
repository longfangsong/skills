---
name: grill-requirements
description: Requirements-only grilling session for aligning the user's understanding of what they need with the actual problem. Use when the user wants to align their understanding of requirements with the actual needs.
---

Run `grill-with-docs` skill, but keep the session strictly focused on **requirements**, not implementation:

- **Stay in requirements scope.** The grilling is about *what* the system should do and *why* — not *how* it does it. Any implementation detail the user raises (frameworks, data models, APIs, code structure) is out of scope. Call it out and steer them back to the requirement behind it.
- **End with `to-spec`.** When the shared understanding is locked in, suggest the user run `to-spec` skill to capture the requirements as a spec.
