# ABC Technologies — AI-Powered Customer Support Automation System

Built with **LangGraph**, **LangChain**, **Ollama llama3.2:3b**, **ChromaDB (RAG)**, and **SQLite (memory)**.

---

## Project Structure

```
customer_support_system/
├── main.py                  # Entry point (demo + interactive)
├── graph.py                 # LangGraph workflow (Tasks 1, 4)
├── state.py                 # State structure (Task 2)
├── init_db.py               # One-time setup: DB + vectorstore
├── schema.sql               # SQLite schema for submission
├── requirements.txt
├── .env.example
├── agents/
│   ├── intent_classifier.py # Task 3 — Intent classification
│   ├── support_agents.py    # Task 5 — 4 specialized agents + memory agent
│   ├── human_approval.py    # Task 8 — Human-in-the-loop
│   └── supervisor_agent.py  # Task 9 — Supervisor review
├── rag/
│   └── rag_pipeline.py      # Task 6 — RAG with ChromaDB
├── memory/
│   └── sqlite_memory.py     # Task 7 — SQLite memory
└── docs/
    ├── company_policy.txt
    ├── pricing_guide.txt
    ├── technical_manual.txt
    └── faq.txt
```

---

## Setup

### 1. Install Dependencies

```bash
pip install -r requirements.txt
```

This installs LangGraph, LangChain, Ollama, ChromaDB, and LangChain text splitters.

### 2. Start Ollama and Download Models

```bash
ollama serve
```

In another terminal:

```bash
ollama pull llama3.2:3b
ollama pull nomic-embed-text
```

Optional local model settings:

```bash
cp .env.example .env
```

### 3. Initialize Database & Build Vector Store

```bash
python init_db.py
```

This creates `memory.db` (SQLite) and `chroma_db/` (vector embeddings).

---

## Run

### Demo Mode (5 sample queries)

```bash
python main.py --demo
```

### Interactive Mode

```bash
python main.py --interactive
```

---

## LangGraph Workflow

```
[Customer Query]
      │
      ▼
[classify_intent]  ──────────────────────────────────────────────┐
      │                                                           │
      ▼ (conditional routing by intent)                          │
┌─────┴──────────────────────────────────────────────────┐       │
│                                                        │       │
▼           ▼            ▼           ▼          ▼       │       │
[sales]  [technical]  [billing]  [account]  [memory]    │       │
  └────────┴────────────┴───────────┴──────────┘        │       │
                         │                               │       │
                         ▼ (conditional: requires_approval?)     │
               ┌─────────┴──────────┐                           │
               │                    │                           │
               ▼                    ▼                           │
        [human_approval]       [supervisor]  ◄──────────────────┘
               │                    │
               └────────────────────┘
                         │
                         ▼
                       [END]
```

---

## 5 Demo Queries

| # | Query | Path |
|---|-------|------|
| 1 | Pricing plans? | Sales |
| 2 | Forgot password | Account |
| 3 | App crashes on file upload | Technical Support |
| 4 | Need a refund for annual subscription | Billing → Human Approval |
| 5 | What was my previous issue? | Memory Recall |

---

## Key Features

- **Intent Classification**: Ollama classifies into sales/technical/billing/account/memory
- **RAG**: ChromaDB retrieves relevant chunks from 4 company documents
- **SQLite Memory**: Stores full conversation history per customer
- **Human-in-the-Loop**: CLI approval for refunds, cancellations, closures, compensation
- **Supervisor Agent**: Reviews and polishes every response before delivery
