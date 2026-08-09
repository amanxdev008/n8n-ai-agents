# Email Summarizer Agent

An n8n automation that checks Gmail for new unread emails, summarizes each one using AI (Google Gemini), and logs the results to a Google Sheet — so you can scan your inbox activity in seconds instead of opening every email.

## What it does

1. **Trigger** — runs on a schedule (e.g. every hour)
2. **Fetch** — pulls the latest unread emails from Gmail (Gmail API)
3. **Summarize** — sends each email's subject, sender, and body snippet to Google Gemini, which returns a 2-3 sentence summary
4. **Log** — appends the subject, sender, and summary as a new row in a Google Sheet

## Why this matters

This is a small but practical example of a common automation pattern:

```
Trigger → Fetch Data → AI Processing → Store Result
```

The same pattern extends naturally to real business use cases — daily inbox digests for founders, support ticket summaries for a team, lead notifications for sales, or a Slack/WhatsApp version instead of a spreadsheet.

## Workflow

```
Schedule Trigger → Gmail (Get Many Messages, unread) → Basic LLM Chain (Google Gemini) → Google Sheets (Append Row)
```

## Setup

1. **Google Cloud project** — create a project, enable the **Gmail API**, and set up an OAuth consent screen (add yourself as a test user).
2. **n8n Gmail credential** — create OAuth2 credentials in Google Cloud (Client ID + Secret), add `http://localhost:5678/rest/oauth2-credential/callback` as an authorized redirect URI, and connect it in n8n.
3. **Google Sheet** — create a sheet with columns: `subject`, `from`, `summary`.
4. **Gemini credential** — connect your Google Gemini (PaLM) API account in n8n for the Chat Model node.
5. **Import `workflow.json`** into n8n, connect the credentials above, and publish.

## Tools used

- [n8n](https://n8n.io) — workflow automation
- Gmail API — fetching unread emails
- Google Gemini — AI summarization
- Google Sheets API — storing results

## Notes

- Limit is set to 5 emails per run for testing — increase as needed.
- Filter is set to "Unread emails only" so it doesn't reprocess the same emails repeatedly.
