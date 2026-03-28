# 👟 ShoeOps Agent — Autonomous AI Agent for Retail Operations

> **Capstone project** for *"Programming in Generative AI Environments"* (תכנות בסביבת בינה גנרטיבית)  
> B.Sc. Industrial & Management Engineering, Azrieli College of Engineering, 2025–2026  
> Lecturer: Dr. Roni Horowitz

**Team:**  Niv Meirovitz · Alon Achituv · Noa Ezri

---
For watching our video demo of the agent [click here](https://www.youtube.com/watch?v=ijzfr5l4km4)
---

## What We Built

An end-to-end autonomous AI agent that manages retail operations for **ZAMSH**, an Israeli women's shoe retail chain with 10 physical stores and an e-commerce site. The agent replaces fragmented service channels (phone, email, WhatsApp, in-store) with a single conversational interface that handles product inquiries, ordering, inventory management, and analytics — all with role-based access control and human approval for sensitive operations.

The system supports three user roles — **Guest**, **Customer**, and **Manager** — each with deterministic permission enforcement controlling which tools and operations are available.

<p align="center">
  
  <img width="500" height="300" alt="UI" src="https://github.com/user-attachments/assets/a58bc154-c21c-4704-bb4a-0518db9b80f7" />

</p>

---

## Architecture

Built on a **cyclic LangGraph state machine** with the following core nodes:

- **Planner** — Identifies user intent, extracts structured fields, detects missing information, and enforces role-based permissions
- **Executor** — Activates the appropriate tools for data retrieval, operational actions, or draft preparation
- **Critic** — Validates write operations before execution (SQL safety checks, business rule validation)
- **HITL (Human-in-the-Loop)** — Interrupts execution for sensitive write operations, presenting proposed actions to the manager for approval, editing, or rejection
- **Formatter / Finalize** — Shapes tool results into user-friendly responses
- **Guardrail nodes** — Prevent uncontrolled free-text output or invalid tool/response mixing

The graph supports **correction loops**: missing information routes back to the user for clarification, rejected HITL actions feed back as `ToolMessage` corrections, and read operations cycle through an evidence-collection path before returning to the executor.

<p align="center">
<img width="392" height="419" alt="Agent Flow Chart" src="https://github.com/user-attachments/assets/7edc83b4-aeca-449c-8f6a-598daf5ac1de" />
</p>

---

## Tech Stack & Integrations

| Component | Technology |
|-----------|-----------|
| Agent Framework | **LangGraph** (StateGraph, conditional edges, cyclic routing, MemorySaver) |
| LLM | **OpenAI GPT-4o-mini** via LangChain |
| Database | **Supabase (PostgreSQL)** — products, inventory, orders, customers, stores, audit_log |
| RAG | **ChromaDB** + OpenAI Embeddings on ZAMSH policy PDFs |
| Web Search | **Tavily** with brand/model awareness |
| Observability | **LangSmith** for tracing, debugging, and run inspection |
| UI | **Gradio** with custom dark theme, role-adaptive layout |
| Runtime | **Google Colab** (Jupyter Notebook) |
| HITL | LangGraph `interrupt()` + `Command(resume=...)` |

---

## Key Capabilities

- **10+ custom tools** for reads and writes against Supabase (product search, inventory checks, order creation, stock transfers, customer lookup, user registration, and more)
- **Conversational ordering flow** — two-phase process: item selection → stock verification → confirmation → order execution with inventory reservation
- **Manager-on-behalf-of-customer ordering** — managers can place orders for identified customers
- **Text2SQL analytics** — managers can run natural-language analytical queries translated to read-only SQL
- **Guest self-registration** — guided conversational flow with duplicate checking and automatic role transition
- **SQL safety layer** — blocks destructive operations (`DELETE`, `DROP`, `TRUNCATE`, `ALTER`) regardless of LLM output
- **Audit logging** — write operations logged with action type, JSON details, approver, and timestamp
- **Email notifications** — HTML templates for order confirmations and status updates

---

## Rich State Management

The LangGraph State object goes well beyond message history, holding structured fields including: user role, display name, customer ID, intent, order ID, SKU, size, quantity, source/target store, registration fields, pending order draft, missing fields, collected evidence, and manager feedback/decision.

---

## QA & Testing

We built a systematic **116-test QA plan** across 15 levels, covering login/session, guest capabilities, self-registration, permission denials, customer ordering, manager read/write operations, HITL flows, Text2SQL, security, multi-step flows, and edge cases — **all 116 tests passing at 100%**.

LangSmith was used throughout for tracing and debugging agent execution flows.

---

## What We Learned

- **Deterministic > prompt-based enforcement**: The LLM repeatedly ignored prompt instructions for critical business logic (permissions, store selection, customer ID resolution). We moved all enforcement into Python — a key lesson in building reliable agents.
- **LangGraph cyclic architecture**: Designing correction loops, conditional routing, and multi-node graphs for real-world workflows.
- **Human-in-the-Loop patterns**: Implementing `interrupt()` and `Command(resume=...)` for manager approval with edit/reject capabilities.
- **ToolMessage ordering matters**: OpenAI requires strict adjacency between AIMessage and its ToolMessage — orphaned tool calls must be fixed positionally.
- **RAG + structured data + write operations**: Combining knowledge retrieval, database access, and state-changing actions in a single agent with proper safety controls.
- **Gradio UI customization**: Targeting Gradio's DOM directly with CSS variable overrides for theme-resilient styling.

---

## How to Run

1. Open the notebook in **Google Colab**
2. Save API keys for all services in main google drive folder
3. Mount Google Drive with API key files (`openai_api_key.txt`, `supabase.txt`, `tavily.txt`)
4. Run all cells — the Gradio UI launches with a shareable link
5. Log in as **Guest** (no credentials), **Customer** (username + PIN), or **Manager** (username + PIN)

---

## Contact

**Alon Achituv** — [LinkedIn](https://www.linkedin.com/in/alon-achituv-551237bb/) · [Email](mailto:Achituv15@gmail.com) 
