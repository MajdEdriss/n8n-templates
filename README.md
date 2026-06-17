# n8n-templates

Reusable, production-grade n8n workflows by **Majd Edriss** ([me-dev.de](https://me-dev.de)).

Engineered, not just clicked together: validation, deduplication, retries and a
dedicated error path are included, the parts most templates skip.

## Workflows

| Workflow | What it does | Docs |
|---|---|---|
| **Data Bridge (Lite)** | Webhook -> validated, deduplicated record into Google Sheets + email notifications | [docs/Data_Bridge_Lite.md](docs/Data_Bridge_Lite.md) |
| **Feature & Bug Automator** | AI/RAG workflow that turns unstructured requirements & bug reports into Jira tickets | [docs/Feature_and_Bug_Automator.md](docs/Feature_and_Bug_Automator.md) |
| **Inquiry Assistant (Lite)** | Contact form -> instant reply, AI triage, Google Sheets + email, with follow-up reminder | [docs/Inquiry_Assistant_Lite.md](docs/Inquiry_Assistant_Lite.md) |

## How to import

1. In n8n: **Workflows -> Import from File** and pick the `.json`.
2. Add the required credentials (e.g. Google Sheets, SMTP/email).
3. Read the sticky notes inside the workflow, they explain setup and design.

## Philosophy

These templates are meant to show how the same automation looks when it is built
to survive production: clear validation, no duplicate writes, retries on flaky
APIs, and failures that alert you instead of disappearing silently.

---

by Majd Edriss - software development, automation, AI integration - <https://me-dev.de>