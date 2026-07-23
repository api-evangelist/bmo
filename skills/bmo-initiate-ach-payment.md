---
name: Initiate an ACH credit payment and track status
description: Look up the caller's ACH company entitlements, submit a U.S. ACH credit transfer, then poll payment status (and/or receive a push notification).
api: openapi/bmo-ach-payments-openapi.json
operations: [ACHCompanyIDList, CreditPaymentInitiation, PaymentStatus]
---

# Initiate an ACH credit payment and track status

Submit a U.S. ACH credit transfer through BMO's commercial ACH Payments API and
follow its status to completion.

## Prerequisites
- Client API key (`x-api-key`) + OAuth 2.0 token with `bmo.tppach.payment-initiation.create`
  (and `...read` for status). Sandbox seeding uses the `SandboxDataManagement` scope.
- Base URL (sandbox): `https://sandbox-open-api.bmo.com/open-banking/commercial-sb`.

## Steps
1. **Resolve company entitlements** — `ACHCompanyIDList`
   (`POST /tpp/ach/customer-access-entitlement/commercial-user-access-arrangement/get`)
   to list the ACH company IDs the user may originate from.
2. **Initiate the credit transfer** — `CreditPaymentInitiation`
   (`POST /tpp/ach/payment-initiation/customer-credit-transfer-initiation`) with the
   ISO 20022 pain.001 payload. Set `x-retry-flag` appropriately; capture the returned payment id.
3. **Track status** — `PaymentStatus`
   (`POST /tpp/ach/payment-initiation/get-transaction-status/get`) to poll the current status,
   OR subscribe to the Push Notification API to receive pain.002 status updates at your endpoint.

## Conventions & errors
- No Idempotency-Key contract: on a `504`/timeout, query `PaymentStatus` before resubmitting.
- Include FAPI + `x-correlation-id` headers; honor `429` throttling.
- See `conventions/bmo-conventions.yml`, `errors/bmo-problem-types.yml`, and
  `asyncapi/bmo-push-notification-webhooks.yml`.
