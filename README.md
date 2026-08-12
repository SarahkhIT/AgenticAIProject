# Smart Event Planner

**Program:** Building Agentic AI Systems by SDAIA Academy

**Session Dates:** 9th of August, 2026 – 13th of August, 2026

**Declared Track:** Track A

## Team Members
- Setah Mohammed Alajmi
- Raneem Abdullah Alsheddi
- Jana Hamad Alhumaizi
- Shatha Hamad Bin Mana
- Sarah Abdulaziz Alkhudhiri

---

## Project Description

**Smart Event Planner** is an agentic AI system for planning events end-to-end. A user
describes an event in natural language (type, guest count, budget, date, indoor/outdoor
preference, theme), and the system extracts a structured request, plans the event using
real tools, answers grounded questions from a knowledge base, and routes between planning
and knowledge specialists automatically — all with a human approval step before anything
is finalized.

**Capabilities:**

- **Structured intake** — natural-language requests are parsed into a validated Pydantic
  `EventRequest` (no invented fields).
- **Real planning tools** — budget allocation, venue search, catering search, decoration
  suggestions, and a preparation checklist, each driven by the arguments the LLM provides.
- **Multi-agent routing** — an LLM supervisor delegates each request to either the
  `planning_agent` or the `knowledge_agent`, with no keyword-based routing.
- **Retrieval-Augmented Generation (RAG)** — event-planning guidance (venue selection,
  safety, graduation-event planning) is loaded from PDFs, chunked, embedded, and retrieved
  to ground the knowledge agent's answers.
- **Context & state management** — short-term workflow state via a LangGraph
  checkpointer/`thread_id`, and long-term user preferences via a separate `Store`, with a
  cross-thread test proving the memory persists across threads.
- **Human-in-the-loop** — the workflow pauses with `interrupt()` before finalizing a plan
  and resumes with `Command(resume=...)` once a human approves, rejects, or edits it.
- **Reliability** — core operations are wrapped in the LangGraph Functional API
  (`@task` / `@entrypoint`) with a real `RetryPolicy` and a fallback strategy for failed or
  empty results.
- **Observability** — LangSmith tracing is enabled end-to-end, with a results-based
  write-up generated from a real trace query (slowest span, error count, tool activity).

---

## Repository Structure

```
AgenticAIProject/
├── README.md
├── .gitignore
└── notebooks/
    ├── 01_agent_fundamentals.ipynb
    ├── 02_rag_and_multi_agent_routing.ipynb
    ├── 03_context_state_and_human_in_the_loop.ipynb
    └── 04_functional_api_reliability_and_observability.ipynb
```



**Each notebook is self-contained and can be run on its own, top to bottom** — any tool
definitions a later notebook depends on (from Rubric 1) are re-declared inside it, so
there's no required run order for any of them to work.

| # | Notebook | Rubric section(s) covered |
|---|---|---|
| 1 | `01_agent_fundamentals.ipynb` | Rubric 1 — Agent Fundamentals (15 pts) |
| 2 | `02_rag_and_multi_agent_routing.ipynb` | Rubric 3 — RAG Pipeline (15 pts) · Rubric 2 — Multi-Agent / Routing Architecture (15 pts) · Rubric 7 — Workflow Pattern (10 pts) |
| 3 | `03_context_state_and_human_in_the_loop.ipynb` | Rubric 4 — Context & State Management (15 pts) · Rubric 5 — Human-in-the-Loop (10 pts) |
| 4 | `04_functional_api_reliability_and_observability.ipynb` | Rubric 6 — LangGraph Functional API & Error Handling (15 pts) · Rubric 8 — LangSmith Observability (5 pts) |

The numbering (01–04) is a suggested reading order that follows the flow of the project,
not a required execution order — the notebooks can be opened and run in any order.

### Notebook structure on GitHub

All four notebooks live under `notebooks/` at the repo root — GitHub renders `.ipynb`
files directly in the browser, so you can click any notebook in the file list and read
its cells (markdown, code, and saved outputs) without downloading anything or setting up
a Python environment locally.

**Browsing on GitHub:**
- Click into `notebooks/` from the repo's main page to see all four files.
- Click a notebook to view it rendered inline — markdown cells, code, and the captured
  output from the original run are all visible directly on GitHub.
- GitHub's inline `.ipynb` viewer is read-only. It's the fastest way to review or grade
  a notebook without running anything.

**Opening a notebook to actually run it (Colab):**
GitHub can't execute notebooks itself. To run one:
1. Go to [colab.research.google.com](https://colab.research.google.com) → **File → Open
   notebook → GitHub tab**.
2. Paste the repo URL (or search `<your-github-username>/AgenticAIProject`) and pick the
   notebook from the list.
3. Add your `GROQ_API_KEY` and `LANGSMITH_API_KEY` to Colab Secrets (see **How to Run**
   below) and use **Runtime → Run all**.

Alternatively, prefix any notebook's GitHub URL with
`https://colab.research.google.com/github/` instead of `https://github.com/` and it opens
directly in Colab — e.g.
`https://colab.research.google.com/github/<username>/AgenticAIProject/blob/main/notebooks/01_agent_fundamentals.ipynb`.

**Editing and pushing changes:**
- Don't edit `.ipynb` files directly in the GitHub web editor — it edits raw JSON, which
  is easy to corrupt (a single misplaced comma breaks the whole notebook). Always edit in
  Colab or Jupyter, then re-download/re-export and push the file.
- After editing in Colab, use **File → Download → Download .ipynb**, replace the file at
  the same path in your local clone of the repo, then `git add`, `git commit`, `git push`
  as usual — pushing to the same path overwrites that file on GitHub.
- Each notebook is independent, so a commit that only touches `03_context_state_and_
  human_in_the_loop.ipynb` doesn't need to touch the other three.

---

## How to Run

These notebooks were built and run in **Google Colab**.

### 1. Requirements
- A Groq API key (free tier is sufficient) — [console.groq.com](https://console.groq.com)
- A LangSmith API key — [smith.langchain.com](https://smith.langchain.com)

### 2. Set up Colab Secrets
In each Colab notebook, open the **Secrets** panel (key icon in the left sidebar) and add:

| Name | Value |
|---|---|
| `GROQ_API_KEY` | your Groq API key |
| `LANGSMITH_API_KEY` | your LangSmith API key |

Enable **Notebook access** for both secrets. No API keys are ever hardcoded in the
notebooks — they are loaded exclusively via `google.colab.userdata`.

### 3. Run order
Open each notebook in Colab and use **Runtime → Run all**. Each notebook installs its own
dependencies in its first code cell(s), so no separate `pip install` step is required
beforehand. The notebooks can be run in any order — each is self-contained — but the
suggested reading order matches the numbering: `01` → `02` → `03` → `04`.

### 4. Notes on state
Because each notebook is a separate Colab runtime, state (chat threads, vector stores,
in-memory checkpointers) does **not** persist between notebooks. Each notebook builds
whatever it needs from scratch, so this does not affect functionality — only note that,
for example, the long-term memory `Store` in `03_context_state_and_human_in_the_loop.ipynb`
is local to that notebook's run, not shared with `01` or `02`.

---

## Technical Documentation

### Architecture

```
User request
     │
     ▼
Structured intake (Pydantic EventRequest)
     │
     ▼
Supervisor (LLM-based routing)
     ├──► planning_agent  → budget / venue / catering / decoration / checklist tools
     └──► knowledge_agent → RAG retriever over event-knowledge PDFs
     │
     ▼
LangGraph workflow: build context → generate plan → save preferences (long-term Store)
     → human_approval (interrupt) → [pause for human] → finalize_plan (resume)
     │
     ▼
Functional API layer (@task / @entrypoint): retries + fallback for budget/venue steps
     │
     ▼
LangSmith tracing throughout
```

### Design choices
- **RAG design:** 2-Step RAG (retrieve, then generate) rather than Agentic RAG — event
  knowledge questions should always be grounded in the project's documents rather than
  leaving retrieval up to agent discretion, and the knowledge base is small enough that
  a Hybrid design would add routing complexity without benefit.
- **Workflow pattern:** Routing — requests naturally split into two specialist
  responsibilities (operational planning vs. document-grounded knowledge), and the LLM
  supervisor decides which one applies per request.
- **Reliability:** a formal `RetryPolicy` (attempts, backoff) handles transient failures
  in tool tasks; a separate fallback function supplies a safe default when a search
  returns no results or an exception occurs.

### Key modules and where to find them
- Tools & structured intake — `01_agent_fundamentals.ipynb`
- RAG pipeline (load → split → embed → store → retrieve) and Supervisor/Workers routing —
  `02_rag_and_multi_agent_routing.ipynb`
- Checkpointer, Store, cross-thread memory test, `interrupt()`/`Command(resume=...)` —
  `03_context_state_and_human_in_the_loop.ipynb`
- `@task`/`@entrypoint`, `RetryPolicy`, fallback logic, and the LangSmith trace inspection
  write-up — `04_functional_api_reliability_and_observability.ipynb`

---

## Git Practices

- `.gitignore` excludes secrets (`.env`, key files), Python/Jupyter build artifacts,
  runtime-generated files (the `event_knowledge/` PDFs created at run time), and local
  vector store / cache directories.
- No API keys are committed anywhere in this repository — all secrets are loaded from
  Colab Secrets at runtime.
- Commits are scoped and incremental (setup, per-rubric-section notebooks, README,
  `.gitignore`) rather than a single bulk upload.

---

## Acknowledgements

This project was completed as the capstone for the **Building Agentic AI Systems**
program run by **SDAIA Academy**.
[SDAIA Academy's GitHub](https://github.com/SDAIAAcademy) 
