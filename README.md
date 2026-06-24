# Leaguer Agent Operating System (Agent OS)

**Agent OS** is a locally-hosted, "Mission Control" dashboard designed to unify disconnected AI agents, complex workflows, and personal knowledge into a single, cohesive workspace. Unlike generic chat interfaces, this system serves as a personal AI operating system that uses your own **Obsidian vault** as its primary memory source, ensuring that every agent interaction is hyper-personalized to your goals, journals, and context.

---

## 🏗️ Core Architecture: The 4 Layers

The system is built on a modular, four-layer architecture to handle intelligence, execution, research, and memory:

1.  **Intelligence Layer (Claude):** Connected via API with full tool access (Model Context Protocol). It handles high-level reasoning, code generation, file analysis, and complex planning.
2.  **Routing Layer (OpenClaw):** Acts as the central executor and "router." It analyzes incoming tasks and decides whether to route them to Claude or Hermes for execution.
3.  **Research Layer (Hermes):** A multi-step background workflow runner connected via low-cost endpoints (e.g., OpenRouter). It is optimized for autonomous web research and scheduled data aggregation.
4.  **Memory Layer (Obsidian):** The system's "brain." It indexes your local Obsidian Vault (.md files) to extract goals, schedules, and daily captures, injecting this context directly into agent prompts.

---

## ⭐ Key Features

*   **Obsidian Memory Sync:** A local indexing pipeline that watches your vault for changes, parses markdown files, and stores metadata in a local SQLite database for instant context retrieval.
*   **Mission Control Interface:** A production-ready Next.js and Tailwind CSS dashboard featuring real-time agent status indicators and a unified workspace for active chats.
*   **Persistent & Bookmarkable Sessions:** Every chat session is stored in a local database with unique IDs, allowing you to return to specific research threads or planning sessions via shareable URLs.
*   **Integrated Toolbelt:** Agents have native access to local tools, including file system operations, shell execution, and web searching.
*   **Privacy-First Design:** The entire application runs locally. Your data storage, session history, and note parsing reside securely on your host machine.

---

## 🛠️ Tech Stack

*   **Frontend:** Next.js, Tailwind CSS (Design paradigm: Elite SaaS Mission Control).
*   **Backend:** FastAPI (Python), SQLite (Local metadata & session storage).
*   **Agents:** Claude (Anthropic API), Hermes (OpenRouter/Local), OpenClaw (Router).
*   **Infrastructure:** Local-first architecture with support for manual or Docker-based deployment.

---

## 📂 Project Structure

```text
├── agentic_os/             # Backend Core
│   ├── api/                # FastAPI endpoints
│   ├── llm/                # Multi-provider adapters
│   ├── memory/             # Obsidian indexing & context injection
│   ├── tools/              # Files, Shell, and Web tools
│   └── agents/             # Agent logic (Claude, Hermes, OpenClaw)
├── frontend/               # Next.js Application
│   ├── app/                # Main views (Chat, Analytics, Control)
│   └── components/         # Reusable UI components
├── config/                 # YAML configuration files
└── logs/                   # System and sync logs
```


---

## 🚀 Getting Started

### Prerequisites
*   Python 3.10+
*   Node.js & npm
*   An active Obsidian Vault
*   API Keys (Anthropic and/or OpenRouter)

### Installation
1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-repo/agent-os.git
    cd agent-os
    ```
2.  **Configure Environment:**
    Create a `.env` file in the root directory and add your keys and vault path:
    ```env
    ANTHROPIC_API_KEY=your_key_here
    OBSIDIAN_VAULT_PATH=/path/to/your/vault
    DATABASE_URL=sqlite:///./agent_os.db
    ```
   
3.  **Setup Backend:**
    ```bash
    pip install -r requirements.txt
    python -m agentic_os.api.main  # Starts FastAPI on :8000
    ```
4.  **Setup Frontend:**
    ```bash
    cd frontend
    npm install
    npm run dev  # Starts Next.js dashboard
    ```
   

---

## 📅 Development Roadmap

*   **Phase 1 (MVP):** End-to-end loop implementation: Task submission → Obsidian context injection → Claude execution → Bookmarkable results.
*   **Phase 2:** Integration of Hermes for background research and semantic (vector) search for the Obsidian vault.
*   **Phase 3:** Docker Compose orchestration, real-time WebSocket updates, and an AI usage analytics dashboard.

---

## 🔒 Privacy
This system is built on the principle of **Zero Cloud Data**. While LLM requests are sent to providers via API, all persistent data—including your notes, session history, and indexed metadata—remains on your local hardware.
