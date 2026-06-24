# System Specification: Agent OS (Local AI Operating System)

You are tasked with building a beautiful, locally-hosted AI Operating System called **Agent OS**. This system serves as a central "Mission Control" dashboard that consolidates disconnected AI agents, workflows, and personal contexts into a single unified workspace.

---

## 🏗️ Core Architecture (The 4 Layers)

The system must implement and integrate the following four layers:

1. **Intelligence Layer (Claude):** Connected directly via API with full tool access and MCP (Model Context Protocol) capabilities. Handles code generation, file reading, document analysis, and planning tasks.
2. **Execution Layer (OpenClaw):** Acts as the central "router" of the system. Manages agent-to-agent communication, cross-agent tasks, and session routing.
3. **Research Layer (Hermes / Open-Source Models):** Multi-step background workflow runner. Connected via free/low-cost endpoints (e.g., OpenRouter running models like Hermes/Nous Research) to execute autonomous web research and scheduled data aggregation.
4. **Self/Memory Layer (Local Context):** The user's personalized "brain." It directly syncs and references a local knowledge base (e.g., an **Obsidian Vault** containing markdown files, goals, structure, and continuous stream data like OMI transcripts).

---

## 🎨 User Interface & Experience Requirements

* **Tech Stack:** Next.js and Tailwind CSS.
* **Design Paradigm:** A clean, production-ready, bookmarckable web interface—**not a terminal**. It should look like an elite SaaS mission control center.
* **Layout:**
    * **Sidebar:** Real-time agent status panel (online/offline/busy indicators for Claude, Hermes, and OpenClaw) and single-click access to active chat threads.
    * **Central Workspace:** Unified dynamic view shifting between active agent chat, configuration panels, or analytics.

---

## 🛠️ Feature & View Specification

### 1. Mission Control Dashboard
* Live connectivity statuses for all backend integrations.
* Single-panel view showing active agent processes.

### 2. Multi-Agent Live Chat Hub
* Direct interaction with Claude or Hermes without opening multiple tabs or copy-pasting context.
* Persistent session history to pull up historical chat logs effortlessly.

### 3. Deep Memory Layer (The Core Superpower)
* **Automated Logging:** Automatically index every goal set, journal entry written, and historical chat session.
* **Obsidian Integration:** A local indexing pipeline that reads from a specified Obsidian Vault directory (`.md` files) to extract goals, schedules, business workflows, and daily memory captures.
* **Context Injection:** Automatically format and feed relevant snippets from this local context into agent system prompts so agents provide hyper-personalized responses instead of generic answers.

### 4. Interactive Control Room
* Manage API keys locally and securely.
* Configure model parameters (temperature, max tokens) per agent.
* Define custom system skills and plugins (e.g., specific onboarding instructions or content calendars).

### 5. AI Screen Time Analytics (Insights Tab)
* A monitoring dashboard showing usage over the last 30 days.
* Track metric cards for: number of sessions run, total tool calls executed by Hermes, most-used models, peak hours, and session lengths.

---

## 🔒 Privacy & Infrastructure Blueprint

* **Local-First Architecture:** The absolute entire app must run locally. Data storage, session history, and note parsing must reside securely on the host machine.
* **Configurability:** The UI must be easily extendable via simple instructions (e.g., writing components that allow Claude to modify or add new agent panels programmatically).
