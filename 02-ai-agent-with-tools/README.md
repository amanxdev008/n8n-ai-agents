# 🧠 AI Agent with Memory, Calculator & Google Sheets Logging (n8n + Gemini)

A conversational **AI Agent** built in **n8n** that remembers your conversation, can do math when needed, and can log information straight into a **Google Sheet** — all through a simple chat interface, no code required.

---

## 📖 What does this workflow do? (in plain English)

This isn't just a basic chatbot — it's an **AI Agent**, meaning it can decide *on its own* which tool to use to answer you properly:

1. You type a message in the chat
2. The **AI Agent** reads your message and thinks about what it needs to respond well
3. If your question involves numbers or math, it can call the **Calculator** tool to get an exact answer instead of guessing
4. If you ask it to save or log something, it can use the **Google Sheets** tool to append a new row automatically
5. It also remembers earlier parts of your conversation using **Simple Memory**, so it doesn't forget what you already told it
6. Finally, it replies to you in the chat using **Google Gemini** as its "brain"

---

## 🏗️ How It Works — Architecture Overview

```
When Chat Message Received → AI Agent → replies to you
                                  │
                    ┌─────────────┼─────────────┐
              Chat Model       Memory          Tools
           (Google Gemini)  (Simple Memory)   ┌───┴───┐
                                          Calculator   Append Row
                                                        in Sheets
```

| Node | What it does |
|---|---|
| **When Chat Message Received** | Triggers the workflow every time you send a message in the n8n chat window. |
| **AI Agent** | The decision-maker. Reads your message, decides whether it needs a tool, uses memory for context, then crafts a reply. |
| **Google Gemini Chat Model** | The language model that powers the agent's understanding and responses. |
| **Simple Memory** | Keeps track of the conversation history so the agent has context from earlier messages. |
| **Calculator (Tool)** | Lets the agent perform accurate math calculations instead of estimating numbers itself. |
| **Append Row in Google Sheets (Tool)** | Lets the agent save information (e.g. notes, logs, data you give it) as a new row in a connected Google Sheet, whenever it decides that's the right action. |

---

## ⚙️ Prerequisites

Before running this workflow, make sure you have:

- ✅ An [n8n](https://n8n.io/) instance (self-hosted or cloud)
- ✅ A [Google AI Studio](https://aistudio.google.com/) API key for **Gemini**
- ✅ A **Google Sheet** ready to receive logged data (with column headers matching whatever you want the agent to save)
- ✅ Google Sheets OAuth2 access set up in n8n

---

## 🚀 Setup Guide

### 1. Prepare your Google Sheet
- Create a new Google Sheet
- Add header columns for whatever data you want the agent to log (e.g. `Date | Note | Value`)
- Copy the Sheet's URL — you'll need it in n8n

### 2. Connect your credentials in n8n
- Add your **Google Gemini (PaLM) API** credential
- Add your **Google Sheets OAuth2** credential

### 3. Import the workflow
- In n8n, click **Add Workflow → Import from File**
- Select `workflow.json` from this folder

### 4. Configure each node
- **AI Agent** → review its system prompt/instructions so it knows when to use the Calculator vs. when to log to Sheets
- **Append Row in Google Sheets** → select your spreadsheet and map the columns
- **Simple Memory** → leave on default settings unless you want to adjust how much conversation history it retains

### 5. Test it
- Click **Open Chat** (bottom of the canvas)
- Try a math question (e.g. *"What's 234 × 18?"*) — it should use the Calculator tool
- Try asking it to log something (e.g. *"Save a note that says: meeting went well"*) — it should append a row to your sheet
- Ask a follow-up question referencing something you said earlier — it should remember, thanks to Simple Memory 🎉

---

## 🐛 Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| Agent doesn't use the Calculator for math | Tool isn't properly described/connected, or the prompt discourages tool use | Check the Calculator node is connected under **Tool**, and that the AI Agent's instructions allow tool use |
| Nothing gets added to the sheet | Column mapping mismatch, or the agent wasn't clearly asked to save something | Double-check column mapping in **Append Row in Google Sheets**, and phrase requests clearly (e.g. "log this...", "save this...") |
| Agent forgets earlier messages | Memory node not connected, or a new session was started | Confirm **Simple Memory** is linked under **Memory**, and that you're continuing the same chat session |
| Agent gives vague or generic replies | Gemini API key issue or missing instructions | Verify the Gemini credential is valid, and add clearer instructions in the AI Agent's system prompt |

---

## 📂 Project Structure

```
ai-agent-calculator-sheets/
├── README.md          ← you are here
└── workflow.json       ← importable n8n workflow
```

---

## 🙌 Credits

Built while learning n8n + AI agent development — a hands-on exercise combining conversational memory, tool use (Calculator), and real-world data logging (Google Sheets) into one agent.
 
