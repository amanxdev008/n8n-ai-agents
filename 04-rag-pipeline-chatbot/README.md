# 🤖 RAG Pipeline Chatbot (n8n + Pinecone + Google Gemini)

A no-code **Retrieval-Augmented Generation (RAG)** chatbot built entirely in **n8n**. It automatically reads documents from Google Drive, turns them into searchable knowledge, and lets you chat with an AI agent that answers questions using that knowledge — instead of just guessing.

---

## 📖 What is RAG? (in plain English)

Normally, an AI chatbot only knows what it learned during training — it has no idea about *your* documents. **RAG** fixes that by:

1. Reading your documents (PDFs, Word files, Google Docs, etc.)
2. Breaking them into small chunks and converting each chunk into a list of numbers called an **embedding** (a mathematical fingerprint of the meaning of that text)
3. Storing those embeddings in a **vector database** (Pinecone)
4. When you ask a question, the chatbot searches the vector database for the most relevant chunks and hands them to the AI as context
5. The AI then answers your question **using your actual documents**, not just general knowledge

This project has **two independent workflows** working together: one that *ingests* documents, and one that *chats*.

---

## 🏗️ How It Works — Architecture Overview

### Part 1: Document Ingestion (left side of the workflow)

```
Google Drive Trigger  →  Download File  →  Pinecone Vector Store (Insert)
                                                   ▲
                                    ┌──────────────┴──────────────┐
                              Embeddings                      Document
                          (Google Gemini)              (Default Data Loader)
                                                                   │
                                                            Text Splitter
                                                     (Recursive Character Splitter)
```

| Node | What it does |
|---|---|
| **Google Drive Trigger** | Watches a Google Drive folder. The moment a new file is added, it kicks off the workflow. |
| **Download File** | Downloads the actual file content from Google Drive so it can be processed. |
| **Default Data Loader** | Reads the downloaded file (binary data) and prepares it to be split into chunks. |
| **Recursive Character Text Splitter** | Breaks the document into small, overlapping chunks of text — small enough for the AI to process accurately. |
| **Embeddings Google Gemini** | Converts each text chunk into a numeric vector (embedding) that represents its meaning. |
| **Pinecone Vector Store (Insert)** | Saves the chunks and their embeddings into your Pinecone index, ready to be searched later. |

### Part 2: The Chatbot (right side of the workflow)

```
Chat Trigger → AI Agent → answers you
                  │
        ┌─────────┼─────────┐
   Chat Model   Memory    Tool
  (Gemini Chat)          (Pinecone Vector Store — Retrieval)
                                │
                        Embeddings Google Gemini
```

| Node | What it does |
|---|---|
| **When Chat Message Received** | Triggers the workflow whenever you send a message in the chat window. |
| **AI Agent** | The "brain" of the chatbot. It decides whether it needs to look something up before answering. |
| **Google Gemini Chat Model** | The language model that actually generates the chatbot's replies. |
| **Pinecone Vector Store (Tool)** | Lets the AI Agent search your Pinecone index for relevant document chunks whenever it needs extra context. |
| **Embeddings Google Gemini** | Converts your question into an embedding so it can be compared against the stored document chunks. |

---

## ⚙️ Prerequisites

Before running this workflow, make sure you have:

- ✅ An [n8n](https://n8n.io/) instance (self-hosted or cloud)
- ✅ A [Google Cloud](https://console.cloud.google.com/) project with an OAuth client for **Google Drive** access
- ✅ A [Google AI Studio](https://aistudio.google.com/) API key for **Gemini** (chat model + embeddings)
- ✅ A free [Pinecone](https://www.pinecone.io/) account and API key

---

## 🚀 Setup Guide

### 1. Create your Pinecone index
- Go to Pinecone → **Create Index**
- ⚠️ **Important:** Set **Dimensions to 3072**. This must match Google Gemini's embedding output size, or you'll get a `Vector dimension does not match` error.
- Set **Metric** to `cosine`

### 2. Connect your credentials in n8n
- Add your **Google Drive OAuth2** credential
- Add your **Google Gemini (PaLM) API** credential
- Add your **Pinecone API** credential

### 3. Import the workflow
- In n8n, click **Add Workflow → Import from File**
- Select `workflow.json` from this folder

### 4. Point the nodes at your resources
- **Google Drive Trigger** → select the folder you want to watch
- **Pinecone Vector Store** (both nodes) → select your Pinecone index

### 5. Run it
- Add a document to your watched Google Drive folder → it gets automatically ingested
- Open the **Chat** panel in n8n and ask a question about that document 🎉

---

## 🐛 Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| `Vector dimension does not match the dimension of the index` | Your Pinecone index dimension doesn't match Gemini's embedding size (3072) | Delete and recreate the index with **Dimensions = 3072** |
| No "+" connector on Default Data Loader | Newer n8n versions moved Text Splitter settings inline into the node itself | Open the node → set **Type of Data** to `Binary` and **Text Splitting** to `Custom` |
| Chat gives generic answers, ignoring your documents | Ingestion workflow hasn't run yet, or the Tool node isn't linked to the same Pinecone index | Confirm both Pinecone nodes point to the same index, and that documents were successfully inserted |

---

## 📂 Project Structure

```
03-rag-pipeline-chatbot/
├── README.md          ← you are here
└── workflow.json       ← importable n8n workflow
```

---

## 🙌 Credits

Built while learning n8n + AI agent development, inspired by community RAG-building tutorials, adapted and debugged for a Pinecone + Gemini stack.
