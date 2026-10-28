---
name: pr-description
description: Writes a pull request title and description from the actual diff: what changed, why, how it was tested, and what reviewers should look at. Use when opening or updating a PR, or when asked to describe a branch.
---

# Writing a PR description

Base every sentence on the diff and commits, never on guesses.

## Gather

1. Find the base branch (`git merge-base HEAD origin/main`, or the repo's default branch).
2. Read `git log --oneline <base>..HEAD` and `git diff <base>...HEAD --stat`, then the full diff of the files that matter.
3. Check for a linked issue or ticket in the branch name or commit messages.

## Write

**Title:** imperative, under 70 characters, says what the change does for the user or system. `Add partial-loading support to bill of lading`, not `Update files`.

**Body**, in this order, skipping any section that would be empty:

```markdown
## What
One or two sentences on the change and its user-visible effect.

## Why
The problem or ticket this solves.

## How
The key design decisions, and anything non-obvious a reviewer needs to know.
Mention migrations, new environment variables, or config changes explicitly.

## Testing
What you ran and what it covered: unit tests, E2E, manual steps.
Be honest about what was not tested.

## Review focus
The one or two places most likely to hide a bug.
```

## Rules

- Keep it short. A reviewer should understand the PR in under a minute.
- Name breaking changes, migrations and new secrets in **bold** so they can't be missed.
- No marketing language and no restating file names the diff already shows.
- If the diff mixes unrelated changes, say so and suggest splitting the PR.
