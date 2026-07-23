---
name: Retrieve business account balances and transactions
description: List accessible business accounts, fetch balances/details for one account, and page through its transactions.
api: openapi/bmo-account-information-openapi.json
operations: ["Search for Accounts", "Get an Account", "Search for Account Transactions"]
---

# Retrieve business account balances and transactions

Read account, balance and transaction data for Online Banking for Business
customers via BMO's commercial Account Information API.

## Prerequisites
- Client API key (`x-api-key`) + OAuth 2.0 token with the `FinancialInformation` scope.
- Base URL (sandbox): `https://sandbox-open-api.bmo.com/open-banking/commercial-sb`.

## Steps
1. **List accounts** — `Search for Accounts` (`GET /accounts`), optionally filtered by
   `accountIds`, `startTime`, `endTime`, `resultType`; page with `offset` + `limit`.
2. **Get one account** — `Get an Account` (`GET /accounts/{accountId}`) for full details and balances
   (deposit / investment / loan / line-of-credit subtypes).
3. **List transactions** — `Search for Account Transactions`
   (`GET /accounts/{accountId}/transactions`) with `startTime`/`endTime` and `offset`/`limit` paging.

## Conventions & errors
- Offset/limit pagination; responses use a HATEOAS `PaginatedArray` + `PageMetadata` envelope.
- Include FAPI + `x-correlation-id` headers.
- Handle `401/403` (auth/entitlement), `404` (unknown account), `429` (throttle).
- See `conventions/bmo-conventions.yml` and `errors/bmo-problem-types.yml`.
