---
name: money-safe
description: Checklist for code that touches money, invoices, payments, taxes or ERP syncs: integer minor units, rounding, idempotent webhooks, reconciliation and audit trails. Use when writing or reviewing billing, payment or accounting logic.
---

# Money-safe code

Bugs in billing code cost real money and trust. Check every item below.

## Amounts

- Store and compute in **integer minor units** (centavos, cents). Never floats. Convert only at the display edge.
- Pick one rounding rule (usually half-up per line, then sum) and use it everywhere. Document it next to the code.
- Taxes: compute VAT and withholding on the right base, in the right order, and test the exempt and zero-rated paths.
- Totals must reconcile: `sum(lines) + tax - withholding - discounts = total`. Assert it in code, not just in tests.

## Payments and webhooks

- Webhooks are delivered **more than once** and **out of order**. Make handlers idempotent with a unique key (provider event ID) and a unique index.
- Verify the provider's signature before trusting the payload.
- Record the raw payload, then process it, so a failed processing step can be replayed.
- Never mark something paid from a client-side redirect; only from a verified server-side event.

## ERP and external syncs

- Map statuses explicitly; an unknown status from the other side is an error, not a default.
- Send amounts in the unit the other system expects, and test the boundary with a real payload sample.
- Make every sync retry-safe: look up by your external ID before creating.
- Keep a sync log (request, response, status) per record for support and reconciliation.

## Numbers and identifiers

- Generate document numbers (invoices, bills of lading) under a lock or a database sequence. Never `max + 1` without one.
- Voids and refunds are new records that reference the original. Never edit or delete posted amounts.

## Tests to write

- Rounding at .5 boundaries, large totals, zero and negative amounts
- Duplicate and out-of-order webhook delivery
- Partial payments, overpayments and refunds
- A reconciliation test that rebuilds totals from line items
