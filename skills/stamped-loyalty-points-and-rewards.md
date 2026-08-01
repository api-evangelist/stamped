---
name: Adjust Stamped loyalty points and redeem rewards
description: Adjust a customer's loyalty points, trigger activities, redeem rewards and audit loyalty program reporting.
api: openapi/stamped-loyalty-operations-openapi.json
operations: [adjust-points, loyalty-trigger-activity, loyalty-redeem-reward, loyalty-void-coupon, loyalty-reports-rewards, loyalty-reports-activities]
---

# Adjust Stamped loyalty points and redeem rewards

Manual loyalty operations for a Stamped Loyalty 2.0 store (Shopify, BigCommerce
or Custom Platforms).

## Auth
- `stamped-api-key` header with the Private API Key.
- Every path includes `{shopId}`; customer-scoped calls also take `{customerId}`.
- Base URL: `https://stamped.io`

## Steps
1. **Adjust points.** `adjust-points`
   (`POST /api/v3/loyalty/shops/{shopId}/customers/{customerId}/adjust-points`)
   to grant or deduct points for a customer.
2. **Trigger an activity / custom activity.** `loyalty-trigger-activity`
   (`POST .../customers/{customerId}/trigger-activity`) to fire a configured
   earning rule, or `loyalty-custom-activity`
   (`POST /api/v3/loyalty/shops/{shopId}/activities`) for an ad-hoc one.
3. **Redeem a reward.** `loyalty-redeem-reward`
   (`POST .../customers/{customerId}/redeem-reward/{rewardId}`). To reverse a
   coupon, call `loyalty-void-coupon` (`.../void-coupon/{couponCode}`).
4. **Audit.** `loyalty-reports-rewards`
   (`GET /api/v3/loyalty/shops/{shopId}/rewards`) and `loyalty-reports-activities`
   (`GET /api/v3/loyalty/shops/{shopId}/activities`) support page/limit pagination
   (`page`, `limit`, `morePages`) for program reporting.

## Rules
- Loyalty flow depends on healthy Merchant order records (see
  stamped-sync-merchant-records). Ensure the order exists before expecting
  points to accrue.
- Errors return `{ "message": ... }`; handle 400/404/500.
