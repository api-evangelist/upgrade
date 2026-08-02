---
name: Create, confirm, and get paid for a Flex Pay order
description: >-
  Operate the Flex Pay (Upgrade) Checkout Orders API correctly: create an
  order, let the customer complete the hosted application, confirm the booking,
  and retrieve the single-use virtual card for merchant payout.
api: openapi/upgrade-flexpay-openapi.yml
operations: [createOrder, getOrder, confirmOrder, getOrderCard]
generated: '2026-07-21'
method: generated
---

# Flex Pay checkout: order to payout

1. **Authenticate.** POST to `https://partner.upgrade.com/api/auth/v1/oauth/token?grant_type=client_credentials` with your Basic-encoded client ID/secret (pre-production host: `partner.credify.tech`). Cache the access token and refresh before its ~30-minute expiry. Send it as `Authorization: Bearer {token}` on every call. Your server IPs must be on the Flex Pay allowlist.
2. **Create the order** with `createOrder` (`POST /v1/orders`), sending the order details (`order_items`, `billingContact`, `addOns`). Amounts and dates must satisfy offer constraints — unavailability comes back as reason codes (see `errors/upgrade-error-codes.yml`).
3. **Verify state when in doubt** with `getOrder` (`GET /v1/orders/{orderId}`).
4. **Confirm the booking** with `confirmOrder` (`PUT /v1/orders/{orderId}/confirmation`), passing your `confirmationId`. Expect `204 No Content`.
5. **Retrieve the virtual card** with `getOrderCard` (`GET /v1/orders/{orderId}/card`) and charge it like any other card to complete payment. The card is single-use.

## Rules

- Currencies are ISO 4217, USD or CAD only; countries US/CA; languages en/fr.
- In pre-production, use the published test profiles exactly as listed in `sandbox/upgrade-sandbox.yml` (approved vs adverse-action personas differ only by birth date).
- Never log or store the virtual card details beyond the charge.
