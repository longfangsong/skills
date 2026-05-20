# Changelog Generator

Generate structured CHANGELOG entries from git commit history.

## When to use

- After completing a set of features or fixes
- Before releasing a new version
- When asked to summarize recent changes

## Inputs

- `since` (optional): Git ref to start from (e.g. `v1.0.0`, `HEAD~10`)
- `language` (optional): Output language, defaults to user's language

## Workflow

1. Run `git log --oneline --since="{since}"` or `git log --oneline -20` to get recent commits
2. Group commits by type:
   - **Features** — new capabilities, `feat:` commits
   - **Bug Fixes** — fixes, `fix:` commits
   - **Improvements** — performance, refactoring, `refactor:` / `perf:` commits
   - **Docs** — documentation changes
   - **Chores** — dependency updates, config changes
3. Merge related commits into single entries
4. Describe changes in user language, not technical commit messages

## Rules

- Keep it simple and clear — this is for local models, no complex classification
- Use user language, not commit message jargon
- Merge related commits to avoid bloat
- If a commit type has no entries, skip that section entirely
- Output standard CHANGELOG format with date header

## Example Output

```
## [Unreleased] - 2026-05-20

### Features
- Added changelog generation skill

### Bug Fixes
- Fixed token passing in sandbox environment
