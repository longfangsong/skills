---
name: code-review-arch
description: Review the changes since a fixed point (commit, branch, tag, or merge-base) against a fixed baseline of module-depth checks — shallowed interfaces, pass-throughs, speculative seams, lost locality — informed by the domain language in CONTEXT.md and the decisions in docs/adr/. Use when the user wants an architecture/module-design review of a branch, a PR, or work-in-progress changes.
---

Review the diff between `HEAD` and a fixed point for one question only: **does the change leave the modules it touches deeper — or shallower — than it found them?**

## Glossary

Use these terms exactly in every finding. Consistent language is the point — don't drift into "component," "service," "API," or "boundary." Full definitions in [LANGUAGE.md](LANGUAGE.md).

- **Module** — anything with an interface and an implementation (function, class, package, slice).
- **Interface** — everything a caller must know to use the module: types, invariants, error modes, ordering, config. Not just the type signature.
- **Depth** — leverage at the interface: a lot of behaviour behind a small interface. **Deep** = high leverage. **Shallow** = interface nearly as complex as the implementation.
- **Seam** — where an interface lives; a place behaviour can be altered without editing in place. (Use this, not "boundary.")
- **Adapter** — a concrete thing satisfying an interface at a seam.
- **Leverage** — what callers get from depth.
- **Locality** — what maintainers get from depth: change, bugs, knowledge concentrated in one place.
- **Deletion test** — imagine deleting the module: if complexity vanishes, it was a pass-through; if it reappears across N callers, it was earning its keep.

## Process

### 1. Pin the fixed point

Whatever the user (or the calling skill) supplied is the fixed point — a commit SHA, branch name, tag, `main`, `HEAD~5`, etc. If none was supplied, ask for it.

Confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. Then capture:

- The diff: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base).
- The commit list: `git log <fixed-point>..HEAD --oneline`.

### 2. Identify the architecture sources

Two per-repo sources shape the review:

- `CONTEXT.md` — the project's domain glossary: the terms modules should be named and organised around, and the _Avoid_ terms code shouldn't use.
- `docs/adr/` — decisions already made. Read the ones touching the changed area.

On top of whatever the repo documents, always carry the **depth baseline** below — a fixed set of checks that applies even when a repo documents nothing. Two rules bind it:

- **ADRs override.** Where a documented decision endorses a shape the baseline would flag, suppress the finding.
- **Always a judgement call.** Each check is a labelled heuristic ("possible shallowing"), never a hard violation.

Each check reads *what it is* → *how to fix*; match it against the diff:

- **Shallowed interface** — a touched module's interface grew (params, flags, error modes, ordering rules callers must now know) faster than the behaviour behind it. → pull the new complexity behind the interface; make the common case need none of it.
- **Pass-through** — a new module that mostly forwards to another and fails the deletion test. → inline it; let callers hit the real module.
- **Speculative seam** — a new interface/port with a single adapter and no concrete second one in sight. One adapter = hypothetical seam; two = real. → collapse to a direct call until something actually varies.
- **Leaked implementation** — internals now visible through the interface: callers must know call order, internal types, or flags that map to internal branches. → absorb the protocol into the module; widen behaviour, not the surface.
- **Lost locality** — one logical change scattered across many modules in this diff, or the same knowledge now duplicated at several call sites. → gather it behind one seam so the next change lands in one place.
- **Testing past the interface** — new tests assert on internal state, or mock a dependency that has a local stand-in or belongs behind a port (see the dependency categories in [DEEPENING.md](DEEPENING.md)). The interface is the test surface. → retarget the tests at the interface; if that's impossible, the module is the wrong shape — say so.
- **Domain drift** — new names that use a CONTEXT.md _Avoid_ term or invent a synonym for a defined term. → rename to the glossary term.

### 3. Review the diff

Walk the diff module by module. A hunk alone can't tell you whether complexity concentrated or just moved — read enough surrounding code to see each touched interface whole and to apply the deletion test.

### 4. Report

Report:

- (a) every place the diff contradicts an ADR: cite the decision; if the friction is real enough that the ADR itself deserves reopening, say so explicitly; and
- (b) every baseline finding: name the check, quote the hunk, and state the cost in terms of **leverage** or **locality**.

Use CONTEXT.md vocabulary for the domain and the glossary above for the architecture. Review the change, not the codebase — pre-existing shallowness is out of scope unless the diff makes it worse. Under 400 words.
