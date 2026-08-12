# 📧 AI-Powered Customer Support Email Workflow (n8n)

An automated email support agent built in **n8n** that reads incoming Gmail messages, classifies them, and generates a professional, on-brand reply using an AI Agent grounded in a company knowledge base (RAG).

---

## 🎯 What This Workflow Does

1. **Listens** for new emails in Gmail.
2. **Filters out noise** — promotional, social, and forum-category emails are excluded before they ever reach the AI, saving cost and avoiding misclassification.
3. **Classifies** the remaining emails as either `customer support` or `Other` using an LLM-based Text Classifier.
4. **Generates a reply** for genuine support emails using an AI Agent that:
   - Follows a defined persona/system prompt (tone, sign-off, formatting rules)
   - Looks up company FAQs/policies from a vector database (Pinecone) before answering, instead of guessing
5. **Skips non-support emails** silently (routed to a No-Op node) so nothing gets an unwanted auto-reply.

---

## 🧩 Architecture

```
Gmail Trigger
     │  (filtered: excludes promotions/social/forums)
     ▼
Text Classifier ──── "Other" ────▶ No Operation, do nothing
     │
  "customer support"
     ▼
   AI Agent  ◀── Chat Model (OpenAI gpt-4o-mini)
     │      ◀── Memory (conversation context)
     │      ◀── Tool: Pinecone Vector Store (FAQ/policy retrieval)
     ▼
  Draft reply (output)
```

| Node | Role |
|---|---|
| **Gmail Trigger** | Polls inbox every minute for new mail; pre-filtered to skip marketing/social emails |
| **Text Classifier** | Uses an LLM to sort each email into `customer support` vs `Other` |
| **OpenAI Chat Model** (x2) | Language models powering the classifier and the AI Agent |
| **AI Agent** | Core reasoning node — reads the email, applies the system prompt/persona, and drafts a reply |
| **Pinecone Vector Store** | Retrieval tool giving the AI Agent access to real company FAQ/policy data (RAG) |
| **Embeddings (Google Gemini)** | Converts FAQ documents into vector embeddings for storage/search in Pinecone |
| **No Operation** | Dead-ends non-support emails so no reply is generated |

---

## ⚙️ Setup

1. Make 06_Customer_Support_workflow.json into n8n 
2. Connect credentials 
   - Gmail OAuth2
   - OpenAI API
   - Pinecone API
   - Google Gemini (PaLM) API
3. Create/point the Pinecone index to your FAQ or policy documents (chunked + embedded).
4. Adjust the **System Message** on the AI Agent node to match your own brand voice, sign-off, and rules.
5. Activate the workflow.

---


## 🛡️ Known Fix Applied

Initially, promotional/marketing emails (e.g. sale newsletters) were occasionally slipping through and getting misclassified as `customer support`. Fixed by:
- Adding a Gmail filter (`-category:promotions -category:social -category:forums`) to block junk before it reaches the classifier
- Tightening the classifier's category descriptions to explicitly exclude newsletters, marketing, and automated notifications

---

## 📌 Notes

- Built and tested in **n8n** using the **LangChain nodes** (`@n8n/n8n-nodes-langchain`).
- Part of an ongoing learning series on building practical AI agents — see other projects in this repo.
