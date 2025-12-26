# Client Onboarding Automation (n8n) — Portfolio

Production-style onboarding automation built with **n8n + Google Sheets + Gmail**.

It receives onboarding submissions via a **Webhook**, performs **API-key authentication**, normalizes & validates the payload, checks for duplicates in Google Sheets, logs every execution, sends internal/external email notifications, and returns proper HTTP status codes.

---

## Features

- **Webhook API** protected with an **API key** (`x-api-key`)
- **Normalization & validation** (email, required fields, planType, date)
- **Duplicate detection** (Google Sheets lookup by `emailNormalized`)
- **Routing with proper HTTP responses**
  - NEW → `201 Created`
  - DUPLICATE → `409 Conflict`
  - INVALID → `400 Bad Request`
  - UNAUTHORIZED → `401 Unauthorized`
- **Audit logging** to Google Sheets (append-only Logs tab)
- **Email notifications** (internal + external) using Gmail

---

## High-level architecture

Webhook → Auth Guard → Normalize/Validate → Sheets Lookup → Route Switch →  
(NEW/DUPLICATE/INVALID) → Sheets Log → Gmail Notifications → Respond to Webhook

---

## Google Sheets schema

See: `docs/schema.md`

---

## Postman testing

### Headers

- `Content-Type: application/json`
- `x-api-key: PORTFOLIO_ONBOARDING_KEY_2025`

### Payloads

Located in `postman/payloads/`:

- `new.json` → expects **201**
- `duplicate.json` → expects **409** (run `new.json` once first)
- `invalid.json` → expects **400**
- Unauthorized test: omit `x-api-key` → expects **401**

See also: `postman/notes.md`

---

## Screenshots

Add these files under `screenshots/` (recommended):

- `01-workflow-overview.png`
- `02-new-201.png`
- `03-duplicate-409.png`
- `04-invalid-400.png`
- `05-unauthorized-401.png`
