# Data Bridge (Lite)

**Webhook -> Google Sheets, with validation, deduplication, retries and email alerts.**

Stop copying data by hand. Send any record (an order, a lead, a form entry) to a
webhook and it lands, validated and deduplicated, in a Google Sheet, with an email
notification to your team. Failures alert you instead of disappearing silently.

This is the *Lite* version of a "Data Bridge": a generic, broadly useful pattern.
The full service wires the same robust pattern into your actual accounting / CRM /
ERP with monitoring and a custom UI. See [From Lite to full](#from-lite-to-full).

## Why this is different

Most templates stop at "trigger -> write row". The parts that make automation
survive production are exactly the ones usually missing, and they are built in here:

- **Validation** before anything is written, so no garbage lands in your sheet.
- **Deduplication / idempotency**: append-or-update on the `ID` column means a
  source firing twice never creates a duplicate row.
- **Retry with backoff** on every writing/sending node.
- **Dedicated error path**: a separate error trigger emails you on any failure.

## How it works

```text
Webhook (POST)
   -> Normalize fields (map payload -> standard schema)
   -> Validate record (required fields, email format, amount >= 0)
        |- valid   -> Upsert row in Google Sheets (dedupe on ID) -> Notify team -> Respond 200 {status: ok}
        |- invalid -> Notify: needs review                        -> Respond 200 {status: review}

On workflow error (separate trigger) -> Send error alert email
```

## Webhook payload

Send a JSON body via `POST`. Field names are an example; remap them in the
**Normalize fields** node to match your source.

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | yes | Unique key, used for deduplication |
| `email` | string | yes | Must be a valid email format |
| `amount` | number | no | Defaults to 0; must be >= 0 |
| `name` | string | no | |
| `subject` | string | no | |
| `source` | string | no | Where the record came from |
| `message` | string | no | |

Example:

```json
{
  "id": "1001",
  "name": "Jane Doe",
  "email": "jane@example.com",
  "subject": "New order",
  "amount": 149.0,
  "source": "webshop",
  "message": "Please ship asap"
}
```

## Google Sheet

Create a sheet with these columns (header row, exact names):

```text
Timestamp | ID | Name | Email | Subject | Amount | Source | Status
```

`Status` is set to `ok` for written records. The `ID` column is the matching
column for append-or-update.

## Setup

1. **Import** the workflow (`Datenbruecke_Lite.json`).
2. **Google Sheets**: add your credential, then open *Upsert row (dedupe on ID)*
   and select your spreadsheet and sheet. Confirm the matching column is `ID`.
3. **Email**: add an SMTP (or swap the email nodes for Gmail) credential and set
   the `fromEmail` / `toEmail` on *Notify team*, *Notify: needs review* and
   *Send error alert*.
4. **Error workflow**: open **Settings** of this workflow and set its own
   *Error Workflow* to itself, so the *On workflow error* trigger fires on failures.
5. **Activate** the workflow and copy the production webhook URL.

## Behavior / responses

| Case | HTTP response |
|---|---|
| Valid record written | `{ "status": "ok", "id": "..." }` |
| Validation failed | `{ "status": "review", "id": "..." }` |

## Test it

```bash
curl -X POST https://YOUR-N8N-HOST/webhook/data-bridge-lite \
  -H "Content-Type: application/json" \
  -d '{"id":"1001","name":"Jane Doe","email":"jane@example.com","subject":"New order","amount":149.0,"source":"webshop"}'
```

Send the same `id` twice: the row is updated, not duplicated. Send an invalid
email or a negative amount: nothing is written and you get a review email.

## From Lite to full

The Lite version uses Google Sheets as the hub so anyone can run it. In a real
business the same pattern connects your existing tools directly (lexoffice, DATEV,
HubSpot, a shop, an ERP), with monitoring, dashboards and a UI your team actually
likes. That is the difference between connecting tools and building software.

-> **[me-dev.de](https://me-dev.de)** - by Majd Edriss

## Marketplace listing (reference)

- **Title:** Data Bridge (Lite): Webhook to Google Sheets with validation, dedupe & email alerts
- **Short description:** Send any record to a webhook and get it validated,
  deduplicated and written to Google Sheets, with email notifications and a
  built-in error path. A production-grade data sync pattern you can adapt to any source.
- **Tags:** webhook, google sheets, data sync, validation, error handling, email, no-code