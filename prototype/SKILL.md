---
name: prototype
description: Build a throwaway prototype to answer a design question. Use when the user cannot settle a design question — typically during a grilling session — and wants to decide by seeing how it would look implemented.
---

# Prototype

A prototype is **throwaway code that answers a single design question**. The question decides the shape. Only the **verdict** — the question, the user's chosen answer, and any decision-rich snippet — survives. The code never graduates to production.

Fired during a grilling session, this is a **detour**: the round loop suspends, and resumes with a recomputed frontier once the verdict settles the decision. The skill also works standalone.

## The loop

1. **Frame.** State the design question in one sentence. A choice question ("A or B?") lists its contenders — build every viable one side by side unless the user narrows it. A feasibility question ("can this work at all?") gets one build.
2. **Pick the shape** from the catalog below.
3. **Prepare the location** (see Location).
4. **Brief and dispatch.** Write one brief per contender (template below) and launch one sub-agent per contender to build it. Never write prototype code yourself — not on the first build, not on iterations; delegation is what keeps prototype code out of this conversation.
5. **Present.** Relay each builder's how-to-view instructions and handoff-doc path. The user views and judges — the decision is theirs, never yours.
6. **Iterate.** Small steer → amend that contender's brief and dispatch a fresh sub-agent. Exploration that outgrows the detour → the user continues from the handoff doc in a new agent and returns with a decision.
7. **Close with the verdict** (template below). Then record the decision by the grill-with-docs rules: CONTEXT.md for terminology; an ADR only when its three criteria hold — the snippet is inlined there.
8. **Clean up — ask first.** After the verdict is recorded, ask once: "Delete the prototypes?" On yes: `git worktree remove --force "$dir" && git branch -D "prototype/$slug"` for worktrees; delete temp dirs. Never delete before the verdict is recorded.

## Shape catalog

| The question is about… | Build… |
| --- | --- |
| UI / UX — "how would this screen feel?" | if the project already has a working frontend, a new page/component copy-edited from an existing one; otherwise a static clickable HTML mock. No backend, hardcoded data either way |
| API ergonomics — "is this interface nice to use?" | type signatures plus example *calling* code — the caller's view is the point |
| Feasibility — "can this work at all?" | the smallest runnable script against real data |
| Data model — "what shape should the data take?" | the schema plus the representative queries against it |

Nothing fits? Invent the smallest artifact the user can look at and judge.

## Location

- **User-facing question built as a standalone mock**: a directory under the OS temp / scratchpad dir.
- **Anything that needs the real project to build or run** — code and architecture questions, and UI prototypes riding the project's own frontend: a throwaway git worktree on a temp branch, seeded with the user's dirty changes as the first commit — the question usually grew out of them. Never touch the user's checkout:

```bash
slug=<kebab-slug>                 # e.g. undo-snapshots-a
dir="$(mktemp -d)/$slug"
git worktree add -b "prototype/$slug" "$dir" HEAD
# Seed dirty state (skip these three lines if `git status --porcelain` prints nothing):
git diff --binary HEAD | git -C "$dir" apply -
git ls-files --others --exclude-standard -z | tar --null -T - -cf - | tar -xf - -C "$dir"
git -C "$dir" add -A && git -C "$dir" commit -m "WIP: dirty state from the main checkout"
```

One worktree or temp dir per contender.

## Throwaway discipline

These rules go into every brief, and you enforce them on what comes back:

- Hardcode inputs. No error handling, no edge cases, no tests.
- Build only enough to judge the question; stop the moment it is judgeable.
- Never commit to a real branch, never merge, never promote prototype code. Only decision-rich snippets get quoted — into the verdict, and from there into specs, tasks, or ADRs.

## Brief template

The builder gets no conversation context — the brief must stand alone.

```markdown
# Prototype brief: <slug>

**Design question:** <one sentence>
**Contender:** <the option this build represents — "sole" for feasibility>
**Shape:** <from the catalog>
**Build in:** <absolute path>

**Build:** <concretely what to make — screens, functions, data — a few bullets>

**Rules:** throwaway code — hardcode inputs, no error handling, no tests; stop
as soon as the question is judgeable; touch nothing outside the build directory.

**Return:** the files you created, step-by-step view/run instructions, and the
path of your handoff document.

**Finish** by running the `handoff` skill — if you cannot invoke skills, write
the handoff document yourself following that skill's instructions — so the user
can continue this prototype in a new agent.
```

## Verdict template

Paste into the conversation when the user decides. To the grilling loop, the **Answer** line is just the round's answer — the settled decision the frontier recomputes from, same as any prose answer. The snippet stands in for a link to the prototype (which is about to be deleted): it is the extract that justified the answer, and `to-spec` and `to-tasks` pick it up from here.

```markdown
## Verdict: <slug>

**Design question:** <one sentence>
**Answer:** <what the user chose, and their reason in one sentence>
**Decision-rich snippet:** <the state machine / reducer / schema / type shape
that encodes the decision, trimmed to the decision-rich parts — omit if none>
```
