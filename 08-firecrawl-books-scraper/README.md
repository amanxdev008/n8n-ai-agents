# 📚 Firecrawl Books Scraper — n8n Async Extraction Workflow

An n8n automation that scrapes book titles and prices from [books.toscrape.com](https://books.toscrape.com) using [Firecrawl](https://firecrawl.dev)'s AI-powered `/extract` endpoint — including a full async **submit → poll → wait → aggregate** pattern.

Built while learning n8n + AI agent automation. This project demonstrates how to handle APIs that don't return data instantly (a very common pattern for AI/LLM-powered tools).

---

## 🧠 What it does

1. **Extract** — sends a URL, a plain-English prompt, and a JSON schema to Firecrawl, asking it to pull out every book's `title` and `price` from the site.
2. **Get Request** — since AI extraction takes time, Firecrawl responds with a job ID first, not the data. This node checks the job status.
3. **If** — checks whether the extracted data has arrived yet.
4. **Wait** — if not ready, pauses 5 seconds and loops back to check again.
5. **Aggregate** — once the data is ready, combines everything into one clean, structured list.

```
Trigger → Extract → Get Request → If ─true(not ready)→ Wait ─┐
                          ▲                                   │
                          └───────────────────────────────────┘
                                       │
                                   false (ready)
                                       ▼
                                  Aggregate
```

This "submit a job, poll until it's done" pattern isn't unique to Firecrawl — it's used by most async AI APIs (image generation, video generation, deep research tools, etc.), so this workflow is a reusable template for that pattern in general.

---

## 🖼️ Screenshots

**Workflow canvas after a successful run:**
![Workflow diagram](screenshots/workflow-diagram.png)

**Final aggregated output (book titles + prices):**
![Aggregate node output](screenshots/aggregate-output.png)

---

## ⚙️ Setup

1. Import `workflow.json` into n8n (**Import from File**).
2. Create a **Header Auth** credential in n8n:
   - Name: `Authorization`
   - Value: `Bearer YOUR_FIRECRAWL_API_KEY`
3. Get an API key from [firecrawl.dev](https://firecrawl.dev) (500 free credits to start).
4. Assign that credential to the **Extract** and **get Request** nodes.
5. Click **Execute workflow**.

> ⚠️ This repo does **not** include any API key. `workflow.json` only references a credential by name — you must connect your own Firecrawl credential after importing.

---

## 🔧 Adapting this for another website

Only three things need to change in the `Extract` node's JSON body:

```json
{
  "urls": ["https://YOUR-SITE.com/*"],
  "prompt": "Extract all X and their Y from the website",
  "schema": {
    "type": "object",
    "properties": {
      "items": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "field1": { "type": "string" },
            "field2": { "type": "string" }
          }
        }
      }
    }
  }
}
```

And update the `If` condition and `Aggregate` field name to match your new field/array names.

---

## 📝 Notes / limitations

- Firecrawl's `/extract` is AI-based and still in beta — response time varies (a few seconds to a couple minutes), which is exactly why the Wait/loop pattern exists.
- On very large sites (books.toscrape.com has ~1000 books across 50 pages), `/extract` may only return a partial dataset since it isn't a systematic page-by-page crawler. For full-site coverage, Firecrawl's `/crawl` endpoint (combined with per-page JSON extraction) is more reliable.

---

## 🙏 Credit

Built while following [Nate Herk's n8n AI Agents course](https://www.youtube.com/@nateherk) — adapted here with a different target site and my own notes for future reference.
