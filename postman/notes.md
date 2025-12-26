# Postman Testing Notes

## Endpoint

Use your n8n **Production Webhook URL**.

## Required headers

- `Content-Type: application/json`
- `x-api-key: PORTFOLIO_ONBOARDING_KEY_2025`

## Payload files

Located in `postman/payloads/`:

- `new.json` → expects **201 Created**
- `duplicate.json` → expects **409 Conflict** (run `new.json` once first to create the lead)
- `invalid.json` → expects **400 Bad Request**
- (Unauthorized test) send any payload **without** `x-api-key` → expects **401 Unauthorized**

## Expected responses

- **201 NEW**
  - `{ ok: true, route: "NEW", ... }`
- **409 DUPLICATE**
  - `{ ok: false, route: "DUPLICATE", ... }`
- **400 INVALID**
  - `{ ok: false, route: "INVALID", errors: [...] }`
- **401 UNAUTHORIZED**
  - `{ ok: false, route: "UNAUTHORIZED", ... }`
