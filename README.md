# Shipkit

![Claude Code plugin](https://img.shields.io/badge/Claude%20Code-plugin-7E31F1)
![Agents: 3](https://img.shields.io/badge/agents-3-A855F7)
![Skills: 5](https://img.shields.io/badge/skills-5-A855F7)
![License: MIT](https://img.shields.io/badge/license-MIT-green)

**Ship production code with Claude Code.** A Claude Code plugin with the agents, skills and CI workflow I use to review, test and ship changes on a multi-tenant SaaS in production.

Install it in two commands, and Claude Code gains a code reviewer, a Playwright test writer, a codebase auditor, and playbooks for PRs, MCP servers and billing-safe code.

## Install

Inside Claude Code:

```text
/plugin marketplace add market0v0/shipkit
/plugin install shipkit@shipkit
```

Or from your terminal:

```bash
claude plugin marketplace add market0v0/shipkit
claude plugin install shipkit@shipkit
```

## What's inside

### Agents

Claude delegates to these automatically when a task fits, or you can ask for one by name.

| Agent | What it does |
| :- | :- |
| `code-reviewer` | Reviews a diff or branch for real bugs, ranked by severity, each with a concrete failure scenario and fix. No style nitpicks. |
| `e2e-test-writer` | Writes Playwright tests for a user flow with page objects and per-role auth, runs them three times to catch flakiness, and reports app bugs instead of hiding them. |
| `conformance-auditor` | Learns a codebase's own conventions, then finds every place that drifts from them, ranked by risk. |

### Skills

Claude loads these when the task matches, or you run them as slash commands.

| Skill | Use it for |
| :- | :- |
| `/shipkit:ship` | Pre-flight before a PR: runs checks, reviews the diff, applies the money checklist when relevant, drafts the PR description. |
| `/shipkit:pr-description` | A PR title and body written from the actual diff: what, why, how it was tested, what to review. |
| `/shipkit:playwright-e2e` | Conventions for E2E tests that don't flake: locators, auth states, idempotent seeding, verifying third-party payloads without live calls. |
| `/shipkit:mcp-server` | Designing and scaffolding MCP servers (FastMCP or TypeScript) that models use correctly and safely. |
| `/shipkit:money-safe` | Checklist for billing, payments, taxes and ERP syncs: integer minor units, idempotent webhooks, reconciliation. |

### CI template

[`templates/github-actions/ai-pr-review.yml`](templates/github-actions/ai-pr-review.yml) runs Claude Code on every pull request and posts inline review comments for real defects. Copy it into `.github/workflows/` and add an `ANTHROPIC_API_KEY` repository secret.

## What a run looks like

Illustrative output from `/shipkit:ship` on a billing change:

```text
> /shipkit:ship

Checks: typecheck ✓  lint ✓  tests 142 passed ✓
Review: 1 finding fixed
  src/billing/refund.ts:58  refund total used float math; now integer centavos
PR title: Add partial refunds for cancelled bookings
PR body: drafted below, ready to paste
```

## Why these exist

These come from shipping a production SaaS where a wrong total or a duplicated webhook costs real money. The reviewer only reports what it can defend with a failure scenario, the test writer verifies payloads to external systems without calling them, and the money checklist encodes the mistakes that are expensive to find in production.

## About the author

Built by **Mark Vincent Cueva**, Senior Software Engineer focused on AI automation: Claude Code workflows, MCP servers, ERP integrations and E2E testing.

- Portfolio: [github.com/market0v0/portfolio](https://github.com/market0v0/portfolio)
- LinkedIn: [mark-cueva-90a33424a](https://www.linkedin.com/in/mark-cueva-90a33424a/)
- Certifications: Anthropic Claude AI Fundamentals · Claude API & AI Integration

## License

MIT
