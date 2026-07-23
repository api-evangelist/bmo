---
name: Validate a U.S. account before a transfer
description: Fetch a client data encryption key, encrypt the sensitive owner/account fields, and validate U.S. account ownership and risk level before setting up a payment.
api: openapi/bmo-account-validation-openapi.json
operations: [post-retrievecryptoinstruction, ValidateAccounts]
---

# Validate a U.S. account before a transfer

Use BMO's commercial Account Validation (US) API to confirm account ownership and
get a risk-level score before initiating a transfer. Sensitive fields must be
encrypted first with a client data encryption key.

## Prerequisites
- Registered commercial app on developer.bmo.com with a client API key (`x-api-key`).
- OAuth 2.0 access token with the `AccountValidation` scope (authorization-code flow at `/oauth20/token`).
- Base URL (sandbox): `https://sandbox-open-api.bmo.com/open-banking/commercial-sb`.

## Steps
1. **Get an encryption key** — `post-retrievecryptoinstruction`
   (`POST /issued-device-administration/client-data-encryption-key/get`) to retrieve
   the current crypto instruction / key referenced by the `x-crypto-key` header.
2. **Encrypt sensitive fields** — encrypt account numbers, owner identifiers and tax IDs
   per the returned instruction; send the key reference in `x-crypto-key`.
3. **Validate the account** — `ValidateAccounts` (`POST /accounts/validate/get`) with the
   encrypted owner and account details. The response returns ownership match and a risk level
   (checked against Early Warning Services' national database).
4. **Decide** — proceed to payment initiation only when ownership/risk are acceptable.

## Conventions & errors
- Include FAPI headers (`x-fapi-interaction-id`, `x-fapi-financial-id`) and `x-correlation-id`.
- Handle `400` (validation), `401/403` (auth/entitlement), `429` (throttle), `5xx` (retry with backoff).
- See `conventions/bmo-conventions.yml` and `errors/bmo-problem-types.yml`.
