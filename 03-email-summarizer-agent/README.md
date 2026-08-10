# 📧 Email Summarizer (n8n + Gmail + Google Gemini)

A no-code automation built in **n8n** that checks your Gmail inbox on a schedule, summarizes new emails using **Google Gemini**, and logs the summaries into a **Google Sheet** — so you can catch up on your inbox in seconds instead of reading every email.

---

## 📖 What does this workflow do? (in plain English)

Instead of manually opening and reading every new email, this workflow:

1. Runs automatically on a **schedule** (e.g. every hour, or every morning)
2. Fetches new messages from your **Gmail** inbox
3. Sends the content of each email to an **AI model (Google Gemini)** and asks it to write a short summary
4. Saves that summary into a **Google Sheet**, giving you a running log you can skim anytime

No coding required — everything happens inside n8n.

---

## 🏗️ How It Works — Architecture Overview

```
Schedule Trigger → Get Many Messages → Basic LLM Chain → Append Row in Sheet
                       (Gmail)                │
                                          Google Gemini
                                            Chat Model
```

| Node | What it does |
|---|---|
| **Schedule Trigger** | Starts the workflow automatically at set intervals (e.g. every hour, once a day) — no need to run it manually. |
| **Get Many Messages** | Connects to your Gmail account and pulls in a batch of recent emails. |
| **Basic LLM Chain** | Sends each email's content to an AI model with instructions to summarize it. |
| **Google Gemini Chat Model** | The actual AI model that reads the email and generates a short, clear summary. |
| **Append Row in Sheet** | Adds the summary (and any details you choose, like sender or subject) as a new row in a Google Sheet. |

---

## ⚙️ Prerequisites

Before running this workflow, make sure you have:

- ✅ An [n8n](https://n8n.io/) instance (self-hosted or cloud)
- ✅ A **Gmail** account with API access enabled via Google Cloud OAuth
- ✅ A [Google AI Studio](https://aistudio.google.com/) API key for **Gemini**
- ✅ A **Google Sheet** set up to receive the summaries (with column headers like `Date`, `Sender`, `Subject`, `Summary`)

---

## 🚀 Setup Guide

### 1. Prepare your Google Sheet
- Create a new Google Sheet
- Add header columns, for example: `Date | Sender | Subject | Summary`
- Copy the Sheet's URL — you'll need it in n8n

### 2. Connect your credentials in n8n
- Add your **Gmail OAuth2** credential
- Add your **Google Gemini (PaLM) API** credential
- Add your **Google Sheets OAuth2** credential

### 3. Import the workflow
- In n8n, click **Add Workflow → Import from File**
- Select `workflow.json` from this folder

### 4. Configure each node
- **Schedule Trigger** → set how often it should run (e.g. every hour)
- **Get Many Messages** → set filters if needed (e.g. only unread emails, or a specific label)
- **Basic LLM Chain** → check the prompt instructs Gemini clearly, e.g. *"Summarize this email in 2-3 sentences"*
- **Append Row in Sheet** → select your Google Sheet and map each column (Sender, Subject, Summary, etc.)

### 5. Test it
- Click **Execute Workflow** to run it once manually
- Check your Google Sheet — new rows with summaries should appear 🎉
- Once it works, make sure the workflow is **Published/Active** so the schedule keeps running automatically

---

## 🐛 Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| No emails coming through | Gmail credential not authorized, or filters too strict | Re-check Gmail OAuth connection and loosen the filters in **Get Many Messages** |
| Summary is empty or off-topic | The AI prompt isn't specific enough | Edit the prompt in **Basic LLM Chain** to be more explicit about tone and length |
| Nothing appears in the sheet | Column mapping is wrong, or the sheet URL is incorrect | Reopen **Append Row in Sheet** and re-select the correct spreadsheet and columns |
| Workflow doesn't run on its own | Workflow isn't published/active | Toggle it to **Published** (top-right) so the Schedule Trigger runs in the background |

---

## 📂 Project Structure

```
email-summarizer/
├── README.md          ← you are here
└── workflow.json       ← importable n8n workflow
```

---

## 🙌 Credits

Built while learning n8n + AI agent automation, combining Gmail, Google Sheets, and Google Gemini into a simple daily inbox digest.
