# Google Sheets Schema

This project uses a single Google Spreadsheet with two tabs: **Leads** and **Logs**.

---

## Sheet: Leads

**Purpose:** store unique onboarding submissions (deduped by `emailNormalized`).

### Columns

- `createdAt` — ISO timestamp (e.g. `2026-12-26T14:30:00.000Z`)
- `fullName`
- `email`
- `emailNormalized` — lowercase + trimmed
- `company`
- `planType` — `Free` | `Pro` | `Enterprise`
- `startDate` — `YYYY-MM-DD`
- `ownerEmail`
- `status` — `NEW` (can be extended later)

---

## Sheet: Logs

**Purpose:** append-only audit trail of every execution and route decision.

### Columns

- `timestamp` — ISO timestamp
- `routeName` — `NEW` | `DUPLICATE` | `INVALID`
- `routeIndex` — `0` invalid, `1` duplicate, `2` new
- `fullName`
- `email`
- `emailNormalized`
- `company`
- `planType`
- `startDate`
- `ownerEmail`
- `message` — human readable summary
- `errorsJson` — JSON string with validation errors (INVALID only)
- `existingRowJson` — JSON string with matched row (DUPLICATE only)
- `executionId` — n8n execution id
