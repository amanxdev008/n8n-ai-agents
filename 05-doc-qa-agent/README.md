# 📄 Docs Q&A Agent

An n8n workflow that lets you ask natural-language questions about an uploaded document (PDF) and get accurate, context-aware answers — powered by Retrieval-Augmented Generation (RAG).

## 🧠 How It Works

1. **Chat Trigger** — User sends a question through the n8n chat interface.
2. **Simple Vector Store** — Stores and retrieves embedded chunks of the uploaded document.
3. **Default Data Loader + Text Splitter** — Loads the source document and splits it into manageable chunks for embedding.
4. **Embeddings (Google Gemini)** — Converts document chunks and the user's query into vector embeddings for semantic search.
5. **AI Agent (OpenAI Chat Model)** — Uses the retrieved relevant chunks as context to generate a natural, accurate answer to the user's question.

## 🛠️ Tech Stack

- **n8n** — workflow orchestration
- **OpenAI (GPT-4o-mini)** — chat/response generation
- **Google Gemini** — embeddings
- **Simple Vector Store** — in-memory vector storage for retrieval

## 🚀 Workflow Architecture

```
Chat Trigger → Simple Vector Store (retrieval) → AI Agent → Response
                       ↑
        Default Data Loader + Embeddings (Google Gemini)
```

## 💡 Example Use Case

Upload a document (e.g. a textbook chapter, manual, or report) and ask:
- "What is this document about?"
- "Explain [topic] mentioned in the document"
- "Summarize section X"

The agent retrieves the most relevant chunks from the document and answers using only that context — reducing hallucination and keeping answers grounded in the source material.

## 📌 Notes

- Built as part of a daily practice series exploring AI agent and automation patterns in n8n.
- This is a foundational RAG pattern that can be adapted for real-world use cases like customer support bots, internal documentation assistants, or product/price catalogs.

## 🔗 Related Projects

Part of an ongoing series of small AI agent builds — see the main repo for other projects (email summarizer, RAG pipeline chatbot, AI agent with tools, etc.).
