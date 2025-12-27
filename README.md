# Client Onboarding Automation (n8n)

![n8n](https://img.shields.io/badge/n8n-automation-orange)
![Status](https://img.shields.io/badge/status-portfolio--ready-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)

# Client Onboarding Automation (n8n) — Portfolio

Production-style onboarding automation built with **n8n + Google Sheets + Gmail**.

It receives onboarding submissions via a **Webhook**, performs **API-key authentication**, normalizes & validates the payload, checks for duplicates in Google Sheets, logs every execution, sends internal/external email notifications, and returns proper HTTP status codes.

---

## What it does

Automates client onboarding intake via webhook, validates payload, detects duplicates, logs to Google Sheets, and sends internal/external notifications.

---

## Tech stack

- n8n (Webhook, Code, Switch/IF)
- Google Sheets
- Gmail (SMTP via n8n Gmail node)
- Postman (testing)

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

## Routes

- **NEW**: New lead → append to Leads sheet → internal + external email
- **DUPLICATE**: Duplicate lead → log duplicate → internal + external email
- **INVALID**: Invalid payload → log invalid → internal email always; external email only if email is valid

---

## Google Sheets schema

See: `docs/schema.md`

---

## How to test (Postman)

1. Import the Postman collection from `/postman`
2. Run:
   - NEW payload
   - DUPLICATE payload
   - INVALID payload

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

See `/screenshots`

- `01-workflow-overview.png`
- `02-new-201.png`
- `03-duplicate-409.png`
- `04-invalid-400.png`
- `05-unauthorized-401.png`

---

## Project highlights (why this is production-style)

- **Defensive automation**: validation + safe branching to avoid failures (e.g., no external email when address is invalid).
- **Observability**: every run is logged in an append-only **Logs** sheet with route + executionId + errors.
- **API-first**: standardized JSON responses with proper HTTP status codes (201/409/400/401).
- **Idempotency / dedupe**: prevents duplicate onboarding via `emailNormalized`.

---

## Tradeoffs & decisions

- **Google Sheets as storage** was chosen for speed and transparency during early-stage operations (easy to audit and demo).
- **Gmail for notifications** keeps the MVP simple; in production at scale, this can be replaced with a dedicated email service.
- **API key auth** is a minimal security layer suitable for demos; production can add HMAC signatures, IP allowlists, and rate limiting.

---

## V2 improvements (roadmap)

- Add **rate limiting** + basic bot detection (anti-spam).
- Add **retries + dead-letter logging** for external integrations (email/sheets) and alerting on failures.
- Move persistence from Sheets to a database (e.g., Postgres/Supabase) and keep Sheets only for reporting.
- Add an automated **test harness** (collection runner) to validate all routes on every change.

## Privacy

This repo uses placeholders like `your-email@example.com` and does not include secrets or real credentials.

## Quick demo (60 seconds)

1. Send `postman/payload_new.json` → expect **201**
2. Send it again (`payload_duplicate.json`) → expect **409**
3. Send `postman/payload_invalid.json` → expect **400**
4. Remove `x-api-key` header → expect **401**
