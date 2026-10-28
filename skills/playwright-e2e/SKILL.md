---
name: playwright-e2e
description: Conventions for reliable Playwright end-to-end tests: page objects, per-role auth states, idempotent seeding, mocking third-party calls and verifying payloads, and running suites in CI. Use when writing, fixing or reviewing Playwright tests.
---

# Playwright E2E conventions

## Structure

```text
e2e/
  pages/            # one page object per screen
  fixtures/         # test.extend() fixtures: seeded data, api client
  .auth/            # saved storage states, one per role (gitignored)
  auth.setup.ts     # logs each role in once and saves its state
  flows/*.spec.ts   # tests, named after the user flow
```

## Rules that prevent flaky tests

- **Locators:** `getByRole`, `getByLabel`, `getByTestId`. Never CSS classes, `nth()` or text that changes with copy edits.
- **Waiting:** web-first assertions (`await expect(locator).toBeVisible()`). Never `waitForTimeout`.
- **Auth once:** a setup project logs in each role and saves `storageState`; tests declare the role with `test.use({ storageState: 'e2e/.auth/admin.json' })`.
- **Seed through the API**, not the UI, and make seeds idempotent (upsert by a stable key) so reruns and parallel workers don't collide.
- **Assert outcomes:** the saved record, the new total, the status change. "No error appeared" is not an assertion.

## Third-party integrations

Don't call real ERPs, payment gateways or email providers from E2E tests. Intercept the outbound call and assert on what you would have sent:

```ts
await page.route('**/restlet/**', async (route) => {
  const body = route.request().postDataJSON()
  expect(body.items).toHaveLength(2)
  expect(body.total).toBe(125050) // centavos, never floats
  await route.fulfill({ json: { success: true, id: 'SO-TEST-1' } })
})
```

For server-side calls, point the integration's base URL at a local stub through an environment variable in the test config.

## CI

- Run on every PR against a preview deployment, plus a nightly full run.
- `retries: 2` in CI only, `trace: 'on-first-retry'`, upload the HTML report as an artifact.
- A test that only passes on retry is a bug to fix, not a pass.
