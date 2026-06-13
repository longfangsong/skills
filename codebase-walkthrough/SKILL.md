---
name: codebase-walkthrough
description: >
  Guided codebase walkthrough where the agent helps the programmer rebuild their understanding through dialogue — not monologue. The agent reads code, asks what the programmer already knows or expects, explains and corrects together, and records improvement ideas in a shared backlog. Trigger on: "walk me through this", "help me understand this codebase", "I'm lost in this repo".
---

# Codebase Walkthrough

## Core Principle

The agent's job is to help the programmer *rebuild* understanding, not to deliver a
report. Explanation and dialogue are interleaved — the agent reads code, surfaces what
it finds, and the programmer drives the agenda; the agent drives the depth.

---

## Step 1 — Discover Structure

Scan silently.

For example:
```bash
# Run internally — summarize results, don't paste them
ls -1
cat README* 2>/dev/null | head -60
cat package.json go.mod Cargo.toml pyproject.toml 2>/dev/null | head -40
find . -type d \( -name node_modules -o -name .git -o -name target \
  -o -name dist -o -name __pycache__ -o -name vendor \) \
  -prune -o -type d -print | head -60
```

Summarize what you find:

- **Tech stack** — framework, build tool, state management, UI library, testing
- **Directory structure** — annotated map, 5–10 lines max
- **Entry points** — the file(s) where execution begins

Then ask — don't tell:

> "Here's what I see: [brief summary]. Does this match what you expected, or is anything surprising?"

Wait for their response. Correct or confirm the mental model, then ask:

> "Where do you want to start — the entry point, or is there a specific part you're most uncertain about?"

---

## Step 2 — Walk Through a Specific Part

When the programmer picks a module, function, or file:

1. **Read the relevant code.**
2. **Show a short excerpt (≤ 20 lines):**
   > "Here's the core of `X`. What do you think this is doing before I explain it?"
   Wait. Then confirm, correct, or extend their reading.
3. **Explain what it does and why it exists.** Focus on:
   - *What it does* — one-sentence purpose
   - *Why it's needed* — what breaks or missing if you remove it
   - *Key data flow* — inputs → processing → outputs
   - *Dependencies* — what it reads from and calls
   - *Notable patterns* — D3 data join, Zustand selectors, etc.
4. **Point out issues and improvements proactively** if there are any obvious ones — dead code, shallow module, tight coupling, missing abstraction, surprising side effect.
5. **Let the programmer respond.** Their reaction — agreement, pushback, new question, improvement idea — determines what comes next.

---

## Step 3 — Record Improvements in Backlog

When the programmer proposes an improvement idea (or agrees with one you surfaced):

**Do not record speculatively.** Only write after the programmer has explicitly stated
or confirmed the idea. Ask if unclear: "Want me to add that to the backlog?"

Write to `docs/backlog.md`:

```markdown
# Backlog

### [TODO] Short descriptive title

**Problem:** What does the current code do, and what problems does it cause?
**State:** "Actionable" — next step is clear. | "Needs discussion" — design decisions open.
**Recommendation:** Specific refactoring direction or alternative approach.
**Benefits:** Why is this change worthwhile?
**Risks:** Potential side effects or costs.
```

---

## Step 4 — Navigate Between Parts

After covering a module, ask:

> "Want to keep going deeper here, or move to something else?"

If they ask "what should I look at next?":

- Summarize what's been covered in one sentence
- Suggest the next most relevant file or function — **one suggestion, with a one-line reason**
- Prefer: things directly called by or calling what you just covered; things the programmer mentioned but you haven't reached; things with issues worth understanding before changing
- Skip test files unless the programmer asks

When they pick something, return to Step 3.

---

## Step 5 — Generate a Takeaway

When the programmer says they're satisfied or stopping:

Write `docs/takeaway.md` — a concise record of what was covered and what was learned.
Structure:

```markdown
# Walkthrough Takeaway — [date]

## What we covered
[List of modules/functions explored, one line each]

## Key findings
[3–5 things that were clarified or discovered]

## Open questions
[Anything left unresolved or worth revisiting]

## Backlog items added
[Titles from backlog, linked if possible]
```

---

## Interaction Patterns

### User asks "what does X do?"
1. Read the code
2. Show a short excerpt, ask what they think first
3. Explain purpose, inputs, outputs
4. Point to where X is called and what it calls

### User asks "should we change X?"
1. Ask what specifically feels wrong to them
2. Discuss the tradeoff — current code vs proposed change
3. Identify the concrete issue (dead code, tight coupling, wrong abstraction level)
4. Record in backlog if they want to

### User asks "what should I look at next?"
1. One-sentence recap of what's been covered
2. One recommendation — file path + reason
3. Brief, not a menu

---

## Pacing

| Programmer's signals | Adjust to |
|----------------------|-----------|
| Answering confidently, correctly | Move faster, skip sub-steps |
| Uncertain but engaged | Stay on current topic, show one more piece |
| "Just tell me" | Explain the easy part, leave one hook to reason about |
| Wants to move on before it's clear | "One more thing before we move — [specific question]" |

---

## What Not To Do

- **Don't explain a whole file at once.** Show a slice, ask, expand only if needed.
- **Don't give a numbered list of "key things to know."** That's a report, not a walkthrough.
- **Don't move to the next topic while confusion remains on the current one.**
- **Don't record backlog items the programmer didn't propose or confirm.**
- **Don't implement anything.** Discuss and record. No code changes without explicit request.
