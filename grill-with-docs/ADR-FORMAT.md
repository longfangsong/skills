# ADR Format

ADRs live in `docs/adr/` and use sequential numbering: `0001-slug.md`, `0002-slug.md`, etc.

Create the `docs/adr/` directory lazily — only when the first ADR is needed.

## Template

```md
# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

That's it. An ADR can be a single paragraph. The value is in recording *that* a decision was made and *why* — not in filling out sections.

## Optional sections

Only include these when they add genuine value. Most ADRs won't need them.

- **Status** (`proposed | accepted`, plus absorption notes like "Revisions of DATE are absorbed into the text" or "Absorbed the retired ADR-NNNN")
- **Considered Options / Rejected alternatives** — only alternatives still proposable against the *current* design, ≤2 lines each, with the reason that killed them
- **Consequences** — only non-obvious downstream effects that carry force
- **Retired designs (do not re-litigate)** — when an ADR absorbs history: one line per dead mechanism and why it died

## Living documents

An ADR states the **current truth** of its decision; git history is the archive.

- When a decision is revised, rewrite the text and note the absorption in Status. Never stack revision blockquotes or amendment appendices — a reader must not have to apply patches in their head.
- When a decision is replaced wholesale, delete the file and retire its number: add a row to a tombstone table in `docs/adr/README.md` (number → what it was → where the content went). Never reuse a retired number.
- When several ADRs are deltas on one subsystem and only the last design is alive, consolidate: rewrite the survivor as a self-standing document (keep the most-cited number), absorb the older ADRs' surviving clauses, and carry a "Retired designs" ledger so dead mechanisms are not re-proposed.
- When numbers retire, re-point code-comment citations by content (which clause the comment meant), not by blind replace.

## Style

- **Simple English**: short sentences, active voice, one idea per sentence. Long narrative paragraphs invite TL;DR.
- **Aim for ≤100 lines** while keeping every decision, its load-bearing rationale, and the rejected alternatives. Compress narrative, never content.
- **Describe principles, not code specifics.** No member listings, file layouts, or call signatures unless the decision *is* that element; one illustrative example is fine. Code specifics rot and are found by grep.
- **Record reversal conditions explicitly.** An accepted risk states what future fact would reverse the decision ("revisit when…"; "if X ever appears, Y wins back").

## Numbering

Scan `docs/adr/` for the highest existing number and increment by one. Check the tombstone table too — retired numbers stay retired.

## When to offer an ADR

All three of these must be true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will look at the code and wonder "why on earth did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If a decision is easy to reverse, skip it — you'll just reverse it. If it's not surprising, nobody will wonder why. If there was no real alternative, there's nothing to record beyond "we did the obvious thing."

### What qualifies

- **Architectural shape.** "We're using a monorepo." "The write model is event-sourced, the read model is projected into Postgres."
- **Integration patterns between contexts.** "Ordering and Billing communicate via domain events, not synchronous HTTP."
- **Technology choices that carry lock-in.** Database, message bus, auth provider, deployment target. Not every library — just the ones that would take a quarter to swap out.
- **Boundary and scope decisions.** "Customer data is owned by the Customer context; other contexts reference it by ID only." The explicit no-s are as valuable as the yes-s.
- **Deliberate deviations from the obvious path.** "We're using manual SQL instead of an ORM because X." Anything where a reasonable reader would assume the opposite. These stop the next engineer from "fixing" something that was deliberate.
- **Constraints not visible in the code.** "We can't use AWS because of compliance requirements." "Response times must be under 200ms because of the partner API contract."
- **Rejected alternatives when the rejection is non-obvious.** If you considered GraphQL and picked REST for subtle reasons, record it — otherwise someone will suggest GraphQL again in six months.

### Wrong homes (record it, just not here)

The ADR keeps the *choice* and points to these homes for the moved content:

- **Domain knowledge / process understanding** → the domain glossary (CONTEXT.md). "This step runs once per session" is a domain fact, not an architecture decision.
- **Wire / transport behavior contracts** → the owning module's README, pinned by its tests.
- **Module layout conventions** → the module's README.
- **Deferred work and transition constraints** → the backlog, each entry with its revisit trigger.
