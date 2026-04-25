# Demo 02: Executing Serial and Parallel Workflows Using CrewAI Agents

## Overview

This demo shows how to orchestrate **multi-agent workflows** with [CrewAI](https://github.com/crewAIInc/crewAI), a framework for building collaborative AI agent systems. Two execution patterns are demonstrated:

| Pattern | Description |
|---------|-------------|
| **Serial (Sequential)** | Tasks run one after another. A downstream task receives the output of an upstream task through an explicit `context` link, creating a chain of dependent steps. |
| **Parallel (Concurrent)** | Tasks run independently at the same time. No `context` is shared between tasks, so they execute without blocking each other. |

Both patterns use the same set of agents — an **AI Researcher** (with web-search capabilities) and a **Technical Writer** — to explore AI advancements in healthcare.

---

## Architecture

```
┌────────────────────────────────────────────────────────┐
│                      CrewAI Crew                       │
│                                                        │
│  Agents                                                │
│  ├── AI Researcher  (uses Tavily web-search tool)      │
│  └── Technical Writer (no tools — pure generation)     │
│                                                        │
│  LLM Backend  (interchangeable via LLM_PROVIDER)       │
│  ├── Groq   — llama-3.3-70b-versatile                  │
│  ├── Hugging Face — Qwen2.5-7B-Instruct                │
│  └── Azure OpenAI — gpt-5-mini                         │
│                                                        │
│  Workflows                                             │
│  ├── Serial:   Research ──▶ Write (context-linked)     │
│  └── Parallel: Research ──┐                            │
│                           ├──▶ Combined result         │
│                  Write ───┘                             │
└────────────────────────────────────────────────────────┘
```

---

## Project Structure

```
Demo_2/
├── Demo_02_Executing_Serial_and_Parallel_Workflows_Using_CrewAI_Agents.ipynb  # Main notebook
├── .env                 # API keys (NOT committed to Git)
├── .env.example         # Template showing required variables
├── .gitignore           # Excludes .env from version control
└── README.md            # This file
```

---

## Prerequisites

- **Python 3.10+**
- API keys for at least one LLM provider and Tavily (see *Setup* below)

---

## Setup

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd Demo_2
```

### 2. Create a `.env` file

Copy the example template and fill in your keys:

```bash
cp .env.example .env
```

The `.env` file must contain the following variables:

```env
# ── LLM Provider Keys (provide at least one) ──
GROQ_API_KEY=your_groq_api_key_here
HF_API_KEY=your_huggingface_api_key_here

# ── Azure OpenAI (optional — only if using Azure) ──
AZURE_OPENAI_API_KEY=your_azure_openai_api_key_here
AZURE_OPENAI_ENDPOINT=https://your-endpoint.azure-api.net/
AZURE_OPENAI_API_VERSION=2024-12-01-preview
AZURE_OPENAI_DEPLOYMENT=gpt-5-mini

# ── Web Search ──
TAVILY_API_KEY=your_tavily_api_key_here
```

> **Security note:** The `.gitignore` ensures `.env` is never committed. Never hard-code API keys in the notebook.

### 3. Install dependencies

All dependencies are installed inline via `%pip install` cells in the notebook. Alternatively, install manually:

```bash
pip install crewai langchain langchain-openai langchain-community langchain-tavily tavily-python pydantic litellm python-dotenv "crewai[azure-ai-inference]"
```

### 4. Choose an LLM provider

In the notebook, set the `LLM_PROVIDER` variable to one of:

| Value | Provider | Model | Free Tier? |
|-------|----------|-------|------------|
| `"groq"` | Groq | `llama-3.3-70b-versatile` | Yes |
| `"huggingface"` | Hugging Face Inference API | `Qwen/Qwen2.5-7B-Instruct` | Yes |
| `"azure"` | Azure OpenAI | `gpt-5-mini` | No |

---

## How to Run

1. Open the notebook in **VS Code** or **Jupyter**.
2. Run all cells from top to bottom.
3. Observe the two workflow outputs:
   - **Serial Result** — The writer's executive summary, built from the researcher's web-search notes.
   - **Parallel Result** — Both tasks execute concurrently; the final output combines their independent results.

---

## Key Concepts

### Agents

An **Agent** is an autonomous unit defined by:
- **Role** — what the agent is (e.g., "AI Researcher")
- **Goal** — what the agent is trying to achieve
- **Backstory** — shapes the agent's reasoning persona
- **Tools** — external capabilities the agent can invoke (e.g., web search)
- **LLM** — the language model powering the agent

### Tasks

A **Task** is a specific assignment given to an agent:
- **`description`** — what to do
- **`expected_output`** — what a good result looks like
- **`agent`** — which agent handles the task
- **`context`** *(optional)* — list of upstream tasks whose output is fed as input (**serial dependency**)
- **`async_execution`** *(optional)* — set to `True` to run concurrently (**parallel execution**)

### Crew

A **Crew** orchestrates agents and tasks:
- Manages execution order based on task dependencies
- Passes context between serial tasks automatically
- Schedules async tasks to run in parallel

### Serial vs. Parallel

| Aspect | Serial | Parallel |
|--------|--------|----------|
| Execution order | One task at a time, in sequence | Multiple tasks at the same time |
| `context` param | Used to pass output between tasks | Not used — tasks are independent |
| `async_execution` | `False` (default) | `True` on at least one task |
| Use case | When Task B depends on Task A's output | When tasks are independent |

---

## API Key Sources

| Service | Sign-up URL |
|---------|-------------|
| Groq | https://console.groq.com |
| Hugging Face | https://huggingface.co/settings/tokens |
| Azure OpenAI | https://portal.azure.com |
| Tavily | https://app.tavily.com |

---

## License

This demo is provided for educational purposes as part of the Multi-Agent Ecosystem course.
