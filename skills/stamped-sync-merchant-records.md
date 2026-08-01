---
name: Sync Stamped merchant records
description: Create and maintain Customer, Product and Order records in Stamped so Reviews and Loyalty engines fire correctly.
api: openapi/stamped-merchant-data-openapi.json
operations: [createCustomer, createProduct, createOrder, lookupCustomer, lookupOrder, filterCustomers]
---

# Sync Stamped merchant records

Use this skill on headless / custom-platform stores where Stamped is not
auto-synced by Shopify or BigCommerce.

## Auth
- Send the Private API Key in the `stamped-api-key` header on every request.
- Every path includes `{shopId}` (your ShopId / storeHash).
- Base URL: `https://stamped.io`

## Steps
1. **Ensure the customer exists.** Call `lookupCustomer`
   (`GET /api/v3/merchant/shops/{shopId}/customers/lookup`) by email or platform id.
   If absent, call `createCustomer`
   (`POST /api/v3/merchant/shops/{shopId}/customers`). Creating a customer can
   trigger the Loyalty "Create Account" earning rule.
2. **Ensure products exist.** For each purchased product, `lookupProduct` then
   `createProduct` (`POST /api/v3/merchant/shops/{shopId}/products`) if missing.
3. **Create the order.** Call `createOrder`
   (`POST /api/v3/merchant/shops/{shopId}/orders`) referencing the customer via
   `customerId` and line items via `productId`. A new order starts the Review
   Request schedule and is evaluated by the Loyalty engine.
4. **Verify.** Use `filterCustomers` / `lookupOrder` to confirm the records
   landed and are consistent with your platform (source of truth).

## Rules
- Records are shop-scoped; never cross shopIds.
- On Shopify/BigCommerce these records sync automatically — treat them as
  read-only and do not create duplicates.
- Errors return `{ "message": ... }`; handle 400/401/404/500 (see
  errors/stamped-problem-types.yml). No idempotency key exists, so guard against
  duplicate creates with a lookup-first pattern.
