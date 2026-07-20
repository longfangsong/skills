---
name: code-review-standard
description: Review the changes since a fixed point (commit, branch, tag, or merge-base) against this repo's documented coding standards plus a fixed baseline of Fowler code smells. Use when the user wants a standards/conventions/code-smell review of a branch, a PR, or work-in-progress changes.
---

Review the diff between `HEAD` and a fixed point for one question only: **does the code conform to how this repo says code should be written?**.

## Process

### 1. Pin the fixed point

Whatever the user (or the calling skill) supplied is the fixed point — a commit SHA, branch name, tag, `main`, `HEAD~5`, etc. If none was supplied, ask for it.

Confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. Then capture:

- The diff: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base).
- The commit list: `git log <fixed-point>..HEAD --oneline`.

### 2. Identify the standards sources

Anything in the repo that documents how code should be written, such as `CODING_STANDARDS.md` or `CONTRIBUTING.md`.

On top of whatever the repo documents, always carry the **smell baseline** below — a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation — and, like any standard here, skip anything tooling already enforces.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name** — a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code** — the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy** — a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps** — the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession** — a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches** — the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery** — one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change** — one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Speculative Generality** — abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Message Chains** — long `a.b().c().d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man** — a class or function that mostly just delegates onward. → cut it, call the real target direct.
- **Refused Bequest** — a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

### 3. Review the diff

Read the diff hunk by hunk against the documented standards and the smell baseline.

### 4. Report

Report — per file/hunk where relevant:

- (a) every place the diff violates a documented standard: cite the standard (file + the rule); and
- (b) any baseline smell you spot: name it and quote the hunk.

Distinguish hard violations from judgement calls — documented-standard breaches can be hard, but baseline smells are always judgement calls, and a documented repo standard overrides the baseline. Skip anything tooling enforces. Under 400 words.
