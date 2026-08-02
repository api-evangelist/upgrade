---
name: Direct Settle transactions (authorize, capture, void, refund)
description: >-
  Operate the Flex Pay (Upgrade) Transactions API for the Direct Settle
  disbursement model: authorize against an order, capture within the 7-day
  window, void unfulfillable amounts, and refund captured amounts — always with
  the required Idempotency-Key header.
api: openapi/upgrade-flexpay-openapi.yml
operations: [authorizeTransaction, getTransaction, captureTransaction, voidTransaction, refundTransaction]
generated: '2026-07-21'
method: generated
---

# Flex Pay Direct Settle transaction lifecycle

1. **Authorize** with `authorizeTransaction` (`POST /v1/transactions`) using the `order_id` from checkout. Omit `amount` to authorize the full remaining approved amount; if you send `amount`, `currency` (ISO 4217) is required. The response is a Transaction with `id`, `original_amount`, `remaining_amount`, `captured_amount`, and `authorization_expiration`.
2. **Capture within 7 days** with `captureTransaction` (`POST /v1/transactions/{transactionId}/capture`) — fully (amount = `remaining_amount`) or partially. After `authorization_expiration`, no new captures are accepted and the remainder is automatically voided.
3. **Void what you will not fulfill** with `voidTransaction` (`POST /v1/transactions/{transactionId}/void`). Voided amounts are permanently deducted from `remaining_amount` and cannot be captured later.
4. **Refund captured amounts** with `refundTransaction` (`POST /v1/transactions/{transactionId}/refund`), up to `captured_amount`; refunds are netted against your upcoming payout during disbursement.
5. **Reconcile** with `getTransaction` (`GET /v1/transactions/{transactionId}`) and your own `merchant_reference_id`.

## Rules

- **Every write requires a unique `Idempotency-Key` header** — reuse the same key only to retry the identical request.
- Most orders need exactly one transaction authorized for the full order amount.
- Direct Settle requires special configuration by your Flex Pay account team; the default disbursement model is the virtual card (see the checkout skill).
