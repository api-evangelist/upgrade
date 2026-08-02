---
name: Show Flex Pay monthly pricing with the Marketing Offers API
description: >-
  Generate Flex Pay (Upgrade) marketing offers to display monthly-payment
  pricing early in the shopping funnel, and handle offer-unavailability reason
  codes correctly.
api: openapi/upgrade-flexpay-openapi.yml
operations: [getOffers]
generated: '2026-07-21'
method: generated
---

# Flex Pay marketing offers

1. **Authenticate** with the OAuth 2.0 client-credentials flow (same token as the checkout APIs; Bearer header).
2. **Request offers** with `getOffers` (`POST /v1/marketing/offers` on the `partner.upgrade.com/api/flexpay` base), sending your `integrationId` (UP-code, e.g. `UP-12345678-9`) and an `orders[]` array of order details.
3. **Render pricing** from the returned offers ("from $X/mo"); pricing values in callback/component responses are in minor units (cents).
4. **Handle unavailability**: when an offer is unavailable the response carries numeric reason codes (0–14) — thresholds, missing trip dates/travelers, unsupported currency/country/language/product. Map each code to the remediation in `errors/upgrade-error-codes.yml`; on code 7 (timeout) or 8 (internal error) degrade gracefully and proceed without Flex Pay.

## Rules

- There are regulatory and compliance constraints on displaying loan offers — follow the compliance guidelines in the Flex Pay docs for wording and disclosure.
- Supported: USD/CAD, US/CA, en/fr. Do not show offers outside those bounds.
- Client-side, prefer the `<up-from-pricing>` web component (see `components/upgrade-components.yml`) which handles batching, caching, and compliance rendering.
