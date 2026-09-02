---
name: code-review-simplify
description: Review the changes since a fixed point (commit, branch, tag, or merge-base) for one question only: could this be dramatically simpler without changing behavior? Focus on "code judo" — restructurings that delete whole categories of complexity (spurious branches, file bloat, redundant layers, sequential orchestration). Use when the user wants a simplification / maintainability review of a branch, PR, or work-in-progress changes.
---

# Code Review: Simplify

Review the diff between `HEAD` and a fixed point for one question only:

**Could this be dramatically simpler without changing behavior?**

The other reviewers ask different questions and will catch overlapping problems from other angles:

- `code-review-arch` asks whether the change leaves the modules it touches deeper or shallower.
- `code-review-standard` asks whether the change conforms to this repo's coding norms.
- `code-review-spec` asks whether the change matches its spec.

This review asks only whether the implementation carries more complexity than the problem needs — and whether that complexity can be *deleted*, not just rearranged. When a finding is really an architecture-deepness or standards problem that the other reviews own, report it only if fixing it is also the simpler path.

## The move: code judo

The single highest-leverage habit this review should push: **look for the restructure that makes complexity disappear.**

Not "this could be a bit cleaner." Look for a reframing where whole branches, helpers, modes, conditionals, or layers stop being needed at all — and where the result feels inevitable in hindsight. When two working implementations both satisfy the spec, prefer the one that deletes moving pieces over the one that spreads the same complexity more neatly.

Deletion beats rearrangement. A refactor that moves complexity around but leaves the reader holding the same number of concepts is not a win.

## Process

### 1. Pin the fixed point

Whatever the user (or the calling skill) supplied is the fixed point — a commit SHA, branch name, tag, `main`, `HEAD~5`, etc. If none was supplied, ask for it.

Confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. Then capture:

- The diff: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base).
- The commit list: `git log <fixed-point>..HEAD --oneline`.

### 2. The simplification baseline

Apply the code-judo habit against these triggers. Each reads *what to look for* → *the simpler move*; match against the diff. Each is a labelled heuristic, never a hard violation — and skip anything the other reviewers already own that this review wouldn't also simplify.

- **Spurious branches** — new ad-hoc conditionals, one-off branches, or feature flags bolted into unrelated flows. → do these branches signal a missing model or helper that, once added, delete them? Prefer the branchless default over a cascade of exceptions.
- **File bloat** — a file the diff pushes toward or past ~1000 lines, or any file grown too large to scan in one glance. → the number is a heuristic, not a law; the smell is "too big to hold." Extract helpers, subcomponents, or modules instead of letting it sprawl.
- **Redundant layers** — thin abstractions, identity wrappers, or pass-through helpers that add indirection without buying clarity. → run the deletion test: delete it. If complexity just vanishes, it was a pass-through; if it reappears across N callers, it earns its keep.
- **Repeated special-cases** — the same one-off branch or a set of booleans/nullable modes complicating existing control flow in several places. → repeated conditionals signal a missing model; replace the chain with a typed model or dispatcher, or collapse to one clearer flow.
- **Sequential orchestration** — independent work serialized for no reason, or related updates that can leave state half-applied. → can the flow run in parallel or land atomically with less structure? (Don't over-index on micro-optimizations; flag only when the cleaner structure is obvious.)
- **Leaked complexity** — feature logic living in a shared path, or details leaking through an API, so an unrelated caller must now understand more than it should. → move the logic to the layer that already owns the concept, and reuse the existing canonical helper instead of a near-duplicate.

### 3. Review the diff

Walk the diff, looking for where it adds complexity the problem doesn't need. Read enough around each hunk to see whether a cohesive change has been scattered, whether a helper is earning its keep, and whether a simpler reframing exists — a hunk alone can't tell you if complexity was deleted or just moved.

### 4. Report

Report per file/hunk where relevant, each with the simpler alternative named explicitly:

- the **code-judo move** — the restructure that would delete the most complexity, stated as a concrete reframe, not "maybe clean this up"; and
- any baseline finding above: name it, quote the hunk, and give the simpler version.

Prioritize, and do not flood the review with low-value nits when there are larger structural wins:

1. Missed opportunities to delete whole categories of complexity
2. Spaghetti / branching complexity increases
3. File-size and decomposition concerns
4. Redundant layers, wrappers, and leaked-complexity problems
5. Sequential or non-atomic orchestration that a simpler structure would fix

State the simpler alternative for each. If the diff is already simple and direct, say so plainly — the point of this review is that there is nothing to simplify, not that there must be.
