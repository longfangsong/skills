---
name: codebase-walkthrough
disable-model-invocation: true
description: >
  Guided codebase walkthrough where the agent helps the programmer rebuild their understanding through dialogue — not monologue. Covers the whole codebase, or only what changed since a base (commit, branch, or tag) — e.g. catching up after time away. The agent reads code, asks what the programmer already knows or expects, explains and corrects together, and records improvement ideas in a shared backlog.
---

# Codebase Walkthrough

## Core Principle

The agent's job is to help the programmer *rebuild* understanding, not to deliver a
report. Explanation and dialogue are interleaved — the agent reads code, surfaces what
it finds, and the programmer drives the agenda; the agent drives the depth.

---

## Step 1 — Scope the Walkthrough

If the programmer already named a scope when invoking the skill, don't ask — confirm it in one line and move on. Otherwise, before reading anything, ask:

> "Do you want to walk through the whole codebase, or just what changed since a base — a commit, branch, or tag? (e.g. `main`, `v2.1`, or the last commit you remember working on)"

**Whole codebase** → continue to Step 2.

**From a base** → pin the base first:

- Confirm it resolves (`git rev-parse <base>`) and the diff is non-empty. If their description is fuzzy ("sometime in March", "before the big refactor"), find a candidate via `git log` and confirm it with them.
- Check for divergence: `git rev-list --left-right --count <base>...HEAD`. If the base side is non-zero, the base has commits of its own that `<base>..HEAD` won't show — ask whether they want to walk through their side, the base's side, or both.
- Capture the diff (`git diff <base>...HEAD` — three-dot, so the comparison is against the merge-base) and the commit list (`git log <base>..HEAD --oneline`).

From here every step is scoped to the change: discovery maps what changed, walkthroughs show diffs in context, and navigation works through the changed files. Untouched parts of the codebase come up only as background.

---

## Step 2 — Discover Structure

Scan silently.

Whole codebase — for example:

```bash
# Run internally — summarize results, don't paste them
ls -1
cat README* 2>/dev/null | head -60
cat package.json go.mod Cargo.toml pyproject.toml 2>/dev/null | head -40
find . -type d \( -name node_modules -o -name .git -o -name target \
  -o -name dist -o -name __pycache__ -o -name vendor \) \
  -prune -o -type d -print | head -60
```

From a base — scan the change instead:

```bash
# Run internally — summarize results, don't paste them
git log <base>..HEAD --oneline            # every commit since the base, merges included
git diff <base>...HEAD --stat             # where the churn landed
git diff <base>...HEAD --name-status -M   # adds, deletes, and renames — not just edits
git status --short                        # uncommitted and untracked work
```

Summarize what you find.

Whole codebase:

- **Tech stack** — framework, build tool, state management, UI library, testing
- **Directory structure** — annotated map, 5–10 lines max
- **Entry points** — the file(s) where execution begins

From a base:

- **Change map** — changed files clustered into 3–5 areas, one line each
- **Shape of the change** — new modules, moved/renamed files, dependency or config changes
- **Uncommitted work** — staged, unstaged, or untracked files, called out separately from the committed history

Then ask where does the user want to start.

---

## Step 3 — Walk Through a Specific Part

When the programmer picks a module, function, or file:

1. **Read the relevant code.** From a base: read the diff hunks *and* enough surrounding
   code to see the touched module whole — a hunk alone rarely explains itself.
2. **Explain what it does and why it exists.** Focus on:
   - *What it does* — one-sentence purpose
   - *Why it's needed* — what breaks or missing if you remove it
   - *Key data flow* — inputs → processing → outputs
   - *Dependencies* — what it reads from and calls
   - *Notable patterns* — recurring idioms of this codebase worth naming (a data-join, a selector pattern, dependency injection)
   From a base, add: *what the old code did*, and *what the change ripples into* — callers, config, data shape.
3. **Point out issues and improvements proactively** if there are any obvious ones — dead code, shallow module, tight coupling, missing abstraction, surprising side effect.
4. **Let the programmer respond.** Their reaction — agreement, pushback, new question, improvement idea — determines what comes next.

---

## Step 4 — Record Improvements in Backlog

When the programmer proposes an improvement idea (or agrees with one you surfaced):

**Do not record speculatively.** Only write after the programmer has explicitly stated
or confirmed the idea. Ask if unclear: "Want me to add that to the backlog?"

Append to `docs/backlog.md` (create it, with the `# Backlog` heading, if missing):

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

## Step 5 — Navigate Between Parts

After covering a module, ask:

> "Want to keep going deeper here, or move to something else?"

If they ask "what should I look at next?":

- Summarize what's been covered in one sentence. From a base, include coverage: "we've covered 5 of the 12 changed files."
- Suggest the next most relevant file or function — **one suggestion, with a one-line reason**
- Prefer: things directly called by or calling what you just covered; things the programmer mentioned but you haven't reached; things with issues worth understanding before changing. From a base: the largest change not yet covered.
- Skip test files unless the programmer asks

When they pick something, return to Step 3.

---

## Interaction Patterns

### User asks "what does X do?"
1. Read the code
2. Show a short excerpt, ask what they think first
3. Explain purpose, inputs, outputs
4. Point to where X is called and what it calls

### User asks "why was X changed?" (from a base)
1. Find the commits touching it: `git log <base>..HEAD --oneline -- <path>`
2. Read the messages and the diff; show the essential hunk
3. Explain the motivation the evidence supports — and say plainly when it isn't recoverable. Record that as an open question rather than guessing.

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

## What Not To Do

- **Don't explain a whole file at once.** Show a slice, ask, expand only if needed.
- **Don't dump the diff.** From a base, walk change by change with context — a pasted 500-line diff is a report, not a walkthrough.
- **Don't give a numbered list of "key things to know."** That's a report, not a walkthrough.
- **Don't move to the next topic while confusion remains on the current one.**
- **Don't record backlog items the programmer didn't propose or confirm.**
- **Don't implement anything.** Discuss and record. No code changes without explicit request.
