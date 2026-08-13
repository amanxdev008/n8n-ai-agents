# 📧 AI-Powered Customer Support Email Workflow (n8n)

An automated email support agent built in **n8n** that reads incoming Gmail messages, classifies them, and automatically replies with a professional, on-brand response using an AI Agent grounded in a company knowledge base (RAG).

---

## 🎯 What This Workflow Does

1. **Listens** for new emails in Gmail.
2. **Filters out noise** — promotional, social, and forum-category emails are excluded before they ever reach the AI, saving cost and avoiding misclassification.
3. **Classifies** the remaining emails as either `customer support` or `Other` using an LLM-based Text Classifier.
4. **Generates and sends a reply** for genuine support emails using an AI Agent that:
   - Follows a defined persona/system prompt (tone, sign-off, formatting rules)
   - Looks up company FAQs/policies from a vector database (Pinecone) before answering, instead of guessing
   - The generated reply is sent automatically back to the customer via Gmail (as a reply in the same thread)
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
Reply to a message (Gmail) ── sends the AI-generated reply back to the customer
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
| **Reply to a message** | Sends the AI Agent's generated reply back to the customer directly via Gmail, in the same thread |

---

## ⚙️ Setup

1. Make Customer_Support_workflow.json into n8n 
2. Connect credentials
   - Gmail OAuth2
   - OpenAI API
   - Pinecone API
   - Google Gemini (PaLM) API
3. Create/point the Pinecone index to your FAQ or policy documents (chunked + embedded).
4. Adjust the **System Message** on the AI Agent node to match your own brand voice, sign-off, and rules.
5. Activate the workflow.

---

## ✅ Proof of Concept — Live Test

Test email sent:
> *"Hello, I received my order but the product is damaged. I want a replacement..."*

The workflow correctly classified it as **customer support**, retrieved context, generated a reply, and **sent it automatically** back to the customer via Gmail:

> *"I'm really sorry to hear that your product arrived damaged! 😢 We definitely want to get this sorted out for you as soon as possible. To request a replacement, please follow these simple steps: 1. Contact Us: Reply to this email with your order number and a brief description of the damage. 2. Return the Item: We'll provide you with a return shipping label to send back the damaged product. 3. Receive Your Replacement: Once we receive the damaged item, we'll ship out your replacement right away! Thank you for your understanding, and we're here to help every step of the way!"*



---

## 🛡️ Known Fix Applied

Initially, promotional/marketing emails (e.g. sale newsletters) were occasionally slipping through and getting misclassified as `customer support`. Fixed by:
- Adding a Gmail filter (`-category:promotions -category:social -category:forums`) to block junk before it reaches the classifier
- Tightening the classifier's category descriptions to explicitly exclude newsletters, marketing, and automated notifications

---

## 📌 Notes

- Built and tested in **n8n** using the **LangChain nodes** (`@n8n/n8n-nodes-langchain`).
- Part of an ongoing learning series on building practical AI agents — see other projects in this repo.
