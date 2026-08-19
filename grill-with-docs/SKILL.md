---
name: grill-with-docs
description: Grilling session that challenges your plan against the existing domain model, sharpens terminology, and updates documentation (CONTEXT.md, ADRs) inline as decisions crystallise. Use when user wants to stress-test a plan against their project's language and documented decisions.
---

<what-to-do>

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

## The round loop

Work the tree in **rounds**. Repeat until nothing is left open:

1. **Recompute the frontier.** The frontier is every open decision whose prerequisites are all settled — the questions you can ask *now* without guessing at answers you haven't heard yet.
2. **Hold back dependent questions — entirely.** A question whose answer depends on another question still open in this round is *not* on the frontier; it belongs to a later round. Never smuggle it in with a hedge like "[conditional on 1 = (i)]" or "(only if yes to 2)" — the moment you feel the need to write a condition next to a question, that question is cut from this round. It costs nothing: it will surface on the frontier next round once its prerequisite is answered.
3. **Look up facts yourself — and finish looking before you ask.** Finding *facts* is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, tools, etc.), dispatch a sub-agent to find it — don't ask the user for anything you could look up yourself. Research gates the round: while any lookup is still running, show the user *nothing* — not even the questions that don't depend on it. Wait for every sub-agent to report, fold the findings back in — a fact may answer a question outright (drop it) or change your recommended answer — and only then present the round. The *decisions* are the user's — put each to them.
4. **Ask the whole frontier in one round — only after all research has reported.** Number each question and give your recommended answer. Before sending, self-check the round: is any lookup still in flight? If so, the round is not ready — wait for it, because its result may answer or reshape a question. Then check every question: does it reference another question's number, or become moot under some answer to another question in this round? If yes, delete it — it goes to a later round.
5. **Wait for the user's answers.** Each answer reshapes the tree — settled decisions push the frontier outward and unblock the questions you held back.

A round looks like this:

> 1. Should failed deliveries be retried? (Recommended: yes — the consumer is idempotent.)
> 2. Where should delivery status live? (Recommended: the `deliveries` table — it already tracks attempts.)

Never like this:

> 3. [conditional on 1 = yes] How many retry attempts?

Question 3 depends on question 1, so it waits for the next round.

Equally broken is the partial round: "While I look into the codebase, here are the questions that don't depend on it…" — never do this. A round reaches the user only when every lookup has reported; facts in hand first, then all questions at once.

## When the loop ends

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on it until the user confirms you have reached a shared understanding.

</what-to-do>

<supporting-info>

## Domain awareness

At the start of the session, while exploring the codebase, also look for existing documentation — it is the reference you will grill against:

### File structure

Most repos have a single context:

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

If a `CONTEXT-MAP.md` exists at the root, the repo has multiple contexts. The map points to where each one lives:

```
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          ← system-wide decisions
├── src/
│   ├── ordering/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                 ← context-specific decisions
│   └── billing/
│       ├── CONTEXT.md
│       └── docs/adr/
```

Create files lazily — only when you have something to write. If no `CONTEXT.md` exists, create one when the first term is resolved. If no `docs/adr/` exists, create it when the first ADR is needed.

## During the session

Apply these moves throughout the rounds, whenever they fire — they are not extra rounds of their own:

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `CONTEXT.md`, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Prototype undecidable questions

When the user cannot settle a design question in the round and wants to see it implemented, invoke the `prototype` skill. It is a detour: the round loop suspends while the contenders are built and judged, and resumes with a recomputed frontier once the verdict settles the decision.

### Update CONTEXT.md inline

When a term is resolved, update `CONTEXT.md` right there. Don't batch these up — capture them as they happen. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

`CONTEXT.md` should be totally devoid of implementation details. Do not treat `CONTEXT.md` as a spec, a scratch pad, or a repository for implementation decisions. It is a glossary and nothing else.

### Offer ADRs sparingly

Only offer to create an ADR when all four are true:

1. Some part of the grilling session **is about implementation or architecture design**, if it's pure product level discussion please suggest /to-spec instead.
1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

</supporting-info>
