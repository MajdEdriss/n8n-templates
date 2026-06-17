# Inquiry Assistant (Lite)

**Contact form -> instant acknowledgement -> AI triage -> Google Sheets + email, with an automatic follow-up reminder.**

No customer inquiry gets lost. Every request that comes in through a form is
captured, acknowledged in seconds, classified by AI (category, urgency, summary,
missing info), written to a Google Sheet and routed to your team by email. If an
inquiry stays open too long, a scheduled check sends a reminder.

This is the *Lite* version: a generic, broadly useful pattern. The full service
wires the same flow into your real inbox, CRM and routing rules. See
[From Lite to full](#from-lite-to-full).

## Why this is different

The parts that decide whether this works in real life, and that most templates skip:

- **Instant acknowledgement** before anything else, the customer hears back in seconds.
- **Validation** before writing, so no broken records land in your sheet.
- **Deduplication** via append-or-update on the `ID` column.
- **Retry with backoff** on every external call (AI, Sheets, email).
- **Scheduled follow-up** instead of a long-running wait node.
- **Dedicated error path** so failures alert you instead of disappearing.

## How it works

```text
Main flow
  Inquiry form
   -> Normalize fields
   -> Validate (valid email + message)
        valid   -> Acknowledge customer (email) -> Classify with AI -> Save to Google Sheets -> Notify team (email)
        invalid -> Notify team: incomplete

Follow-up flow (separate)
  Daily schedule -> read sheet -> keep rows Status = "new" AND older than 2 days -> email reminder

On workflow error -> email alert
```

## Form fields

Name, Email (required, validated), Subject, Message (required). Adjust the fields in
the **Inquiry form** node to match your needs.

## AI output

The AI returns structured JSON, mapped to the sheet:

- `category` - what the inquiry is about
- `urgency` - low / medium / high
- `summary` - one-line summary
- `missingInfo` - what is still needed to answer

## Google Sheet

Create a sheet with these columns (header row):

```text
Timestamp | ID | Name | Email | Subject | Category | Urgency | Summary | Status
```

New rows get Status `new`. Set Status to `done` to stop follow-up reminders.

## Setup

1. **Import** `Inquiry_Assistant_Lite.json`.
2. **OpenAI** (or another LLM): add your credential to the *OpenAI Chat Model* node. The model is swappable; an EU-hosted model works too.
3. **Google Sheets**: add your credential, then select the spreadsheet and sheet in *Save inquiry (upsert)* and *Get inquiries*. Matching column is `ID`.
4. **Email**: add SMTP credentials and set `fromEmail` / `toEmail` on the four email nodes.
5. **Error workflow**: in **Settings**, set this workflow as its own Error Workflow.
6. Activate. The form has a public URL you can share or embed.

## From Lite to full

The Lite version uses a built-in form and a Google Sheet so anyone can run it. In a
real business the same flow connects your actual inbox, CRM and routing rules, with a
shared overview and the model that fits your data-protection needs. That is the
difference between connecting tools and building software that fits.

-> **[me-dev.de](https://me-dev.de)** - by Majd Edriss

## Marketplace listing (reference)

- **Title:** Inquiry Assistant (Lite): contact form to AI triage, Google Sheets & email with follow-up
- **Short description:** Captures inquiries from a form, acknowledges them instantly,
  classifies them with AI (category, urgency, summary, missing info), saves them to
  Google Sheets and notifies your team, with a scheduled follow-up reminder so nothing
  is forgotten.
- **Tags:** ai, openai, customer support, lead, google sheets, email, automation