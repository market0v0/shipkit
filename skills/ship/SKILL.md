---
name: ship
description: Pre-flight check before opening a PR: runs the project's typecheck, lint and tests, reviews the diff for bugs with the code-reviewer agent, then drafts the PR description. Use when the user says they're ready to ship, open a PR, or wrap up a change.
---

# Ship a change

Run these steps in order and stop at the first real failure.

1. **Know the change.** `git status` and `git diff --stat` against the base branch. If there are no changes, say so and stop.
2. **Run the project's checks.** Find them in `package.json` scripts, `Makefile`, `pyproject.toml` or CI config. Typically typecheck, lint, then the relevant tests. Run only what exists; never invent commands.
3. **Fix what you broke.** If a check fails because of this change, fix it and rerun. If it was already failing on the base branch, report it rather than fixing unrelated code.
4. **Review.** Delegate to the `code-reviewer` agent for the diff. Fix confirmed findings, or list them for the user if they need a decision.
5. **Money paths.** If the diff touches billing, payments, taxes or an ERP sync, apply the `money-safe` checklist.
6. **Describe.** Use the `pr-description` skill to draft the title and body.

## Report

A short summary: checks run and their results, review findings fixed or outstanding, and the drafted PR title and description, ready to paste. Do not push or open the PR unless the user asks.
