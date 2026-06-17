# Feature & Bug Automator (AI / RAG)

**Turn unstructured requirements and bug reports into clean, structured Jira tickets, automatically.**

Requirements and bug reports usually arrive in plain language: an email, a form, a
quick note from a product owner. Someone then has to translate them by hand into
user stories, subtasks, acceptance criteria or reproducible bug reports. That is
slow, inconsistent and a frequent source of misunderstandings between the business
side and the development team.

This workflow does that translation for you, with an AI agent that can ground its
output in your own product knowledge (RAG).

## What it does

1. You submit a request in natural language (a feature idea or a bug).
2. A **text classifier** decides: feature, bug, or unclear.
3. The matching **AI agent** produces a structured result:
   - **Feature** -> summary, user story ("As a ... I want ... so that ..."),
     subtasks, acceptance criteria.
   - **Bug** -> summary, description, steps to reproduce, expected vs. actual
     behavior, priority.
4. The result is written into **Jira** as the right issue type.

The agents can query a **vector store (RAG)** first, so answers reflect your
product, processes and terminology instead of generic guesses.

## Three entry points

- **Submit a request** (form) -> classify -> agent -> Jira. The main flow.
- **Upload files** (form) -> embeddings -> vector store. Loads your documentation
  (PDF/CSV) into the knowledge base the agents draw on.
- **Chat** -> AI agent over the same knowledge base. Ask questions about the docs.

## How it works

```text
Submit a request (form, field "Text")
   -> Text Classifier  -> feature | bug | unclear
        feature -> Req-to-feature-Agent (+ RAG) -> Structured Output -> Create feature in Jira
        bug     -> Req-to-bug-Agent     (+ RAG) -> Structured Output -> Create bug in Jira

Upload file(s) (form) -> Embeddings -> Insert into Vector Store   (build the knowledge base)
Chat message          -> AI Agent (+ Vector Store)               (Q&A over the docs)
```

## Output contract

The agents emit structured JSON; the Jira nodes map it to ticket fields:

- **Feature:** `summary`, `userStory`, `subtasks[]`, `acceptanceCriteria[]`
- **Bug:** `summary`, `description`, `stepsToReproduce[]`, `expectedBehavior`,
  `actualBehavior`, `source`, `priority`

If you change a field name, change it in the agent prompt, the structured output
schema and the Jira node together, they are one contract.

## Setup

1. **Import** `Feature_and_bug_Automator.json` into n8n.
2. **OpenAI** (or another LLM): add your credential to the chat/model nodes.
   The model is swappable, an EU-hosted model works too.
3. **Jira**: add your credential, then open *Create feature in Jira* and
   *Create bug in jira* and select your project and issue types.
4. **Knowledge base (optional but recommended)**: open the *Upload your data*
   form and add a few PDFs/CSVs so the agents can ground their answers (RAG).
5. Activate and submit a request through the *Submit a request* form.

## Notes

- The form field is named `Text`; the rest of the workflow reads `$json.Text`.
  Keep that label or update the references.
- The vector store is in-memory in this template (simple to try). For production,
  swap it for a persistent vector database.

## From template to your setup

This template shows the pattern end to end. In a real team it gets wired to your
Jira, your documentation and your tone of voice, with a persistent knowledge base
and the model that fits your data-protection needs. That is the difference between
wiring tools together and building software that fits.

-> **[me-dev.de](https://me-dev.de)** - by Majd Edriss

## Marketplace listing (reference)

- **Title:** Feature & Bug Automator: turn plain-language requests into structured Jira tickets (AI + RAG)
- **Short description:** Classifies incoming requests as feature or bug and uses an
  AI agent, grounded in your own docs via RAG, to generate user stories, subtasks,
  acceptance criteria or reproducible bug reports, then creates the matching Jira issue.
- **Tags:** ai, rag, jira, requirements, agile, openai, automation