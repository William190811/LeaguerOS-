# Agent OS - Complete Implementation Guide

**Your Personal AI Operating System**

Built around your Obsidian vault as the memory source, Claude + Hermes as execution engines, OpenClaw as the router, and Next.js as the mission control interface.

---

## 1. Project Vision (The Why)

**Goal:** A locally-hosted "Mission Control" that:
- Reads your Obsidian vault (goals, journal, context)
- Routes tasks to Claude (code, planning) or Hermes (research, data)
- OpenClaw decides which agent to use
- Next.js UI shows everything (bookmarkable, beautiful)
- Zero cloud data. All local.

**Outcome:** Your personal AI OS that knows your goals, remembers your context, and executes tasks with your brain as the reference.

---

## 2. Architecture (4 Layers)

```
┌──────────────────────────────────────────┐
│  UI Layer: Next.js (Bookmarkable SaaS)  │
└──────────────────┬───────────────────────┘
                   │
┌──────────────────┴───────────────────────┐
│  Routing Layer: OpenClaw (Task Router)   │
└──────────────────┬───────────────────────┘
                   │
        ┌──────────┴──────────┐
        │                     │
┌───────▼────────┐  ┌────────▼──────────┐
│ Claude Agent   │  │ Hermes Agent      │
│ (Code/Plan)    │  │ (Research/Data)   │
└────────┬───────┘  └────────┬──────────┘
         │                   │
         └───────────┬───────┘
                     │
         ┌───────────▼──────────────┐
         │ Memory Layer             │
         │ Obsidian Vault Sync      │
         │ + SQLite Local Index     │
         │ + Context Injection      │
         └────────────────────────┘
```

**Data flow:**
1. User submits task via Next.js
2. OpenClaw analyzes task → decides Claude or Hermes
3. Memory layer injects Obsidian context into agent prompt
4. Agent executes with full context
5. Result stored in session DB
6. Next.js displays result (bookmarkable URL)

---

## 3. Tech Stack

### Backend
```
Language:        Python 3.11+
Framework:       FastAPI (async REST API)
Server:          Uvicorn
Database:        SQLite (local sessions) + PostgreSQL (optional, for scale)
Memory Store:    SQLite (Obsidian index)
File Watcher:    watchdog (monitor Obsidian vault)
Markdown Parser: python-frontmatter + markdown
LLM Clients:     anthropic, openrouter
Config:          python-dotenv, pydantic
CLI:             Typer
Testing:         pytest
```

### Frontend
```
Framework:       Next.js 14+ (App Router)
Styling:         Tailwind CSS
State:           React hooks (useState, useEffect, useContext)
API Client:      fetch (built-in)
Real-time:       Server-Sent Events (SSE) for streaming
Icons:           lucide-react or heroicons
```

### Infrastructure
```
Container:       Docker + Docker Compose
Local Execution: Systemd or supervisor
File Storage:    Local filesystem (.obsidian, .sessions, .cache)
```

---

## 4. Project Structure

```
agent-os/
├── backend/
│   ├── pyproject.toml              # Poetry or setuptools
│   ├── requirements.txt
│   ├── Dockerfile
│   ├── docker-compose.yml
│   │
│   ├── agentic_os/
│   │   ├── __init__.py
│   │   ├── main.py                # FastAPI app + routes
│   │   │
│   │   ├── agents/
│   │   │   ├── __init__.py
│   │   │   ├── base.py            # BaseAgent class
│   │   │   ├── claude.py          # Claude agent (code/planning)
│   │   │   ├── hermes.py          # Hermes agent (research/data)
│   │   │   └── openclaw.py        # Router agent (task routing)
│   │   │
│   │   ├── memory/
│   │   │   ├── __init__.py
│   │   │   ├── obsidian_sync.py   # Vault watcher + parser
│   │   │   ├── indexer.py         # SQLite indexing
│   │   │   ├── context.py         # Context injection into prompts
│   │   │   └── schema.py          # Memory data models
│   │   │
│   │   ├── llm/
│   │   │   ├── __init__.py
│   │   │   ├── adapter.py         # Provider-agnostic interface
│   │   │   ├── providers/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── claude.py      # Anthropic API
│   │   │   │   └── hermes.py      # OpenRouter API
│   │   │   └── cost.py            # Token counting (optional)
│   │   │
│   │   ├── tools/
│   │   │   ├── __init__.py
│   │   │   ├── base.py            # BaseTool class
│   │   │   ├── files.py           # File read/write
│   │   │   ├── shell.py           # Shell execution
│   │   │   └── web.py             # Web search/scrape
│   │   │
│   │   ├── api/
│   │   │   ├── __init__.py
│   │   │   ├── chat.py            # /api/chat (submit task)
│   │   │   ├── sessions.py        # /api/sessions (list/get)
│   │   │   ├── vault.py           # /api/vault (sync status)
│   │   │   ├── agents.py          # /api/agents (agent status)
│   │   │   └── ws.py              # WebSocket (optional real-time)
│   │   │
│   │   ├── models/
│   │   │   ├── __init__.py
│   │   │   ├── task.py            # Task schema
│   │   │   ├── session.py         # Session schema
│   │   │   ├── message.py         # Message schema
│   │   │   └── vault.py           # Vault index schema
│   │   │
│   │   ├── db/
│   │   │   ├── __init__.py
│   │   │   ├── models.py          # SQLAlchemy models
│   │   │   ├── session.py         # DB connection
│   │   │   └── init.py            # Initialize DB
│   │   │
│   │   ├── config/
│   │   │   ├── __init__.py
│   │   │   └── settings.py        # Pydantic BaseSettings
│   │   │
│   │   └── utils/
│   │       ├── __init__.py
│   │       ├── logging.py
│   │       └── errors.py
│   │
│   ├── agents/
│   │   └── (agent-specific configs, prompts)
│   │
│   └── tests/
│       ├── conftest.py
│       ├── test_agents.py
│       ├── test_memory.py
│       ├── test_api.py
│       └── fixtures/
│
├── frontend/
│   ├── package.json
│   ├── next.config.js
│   ├── tailwind.config.js
│   ├── tsconfig.json
│   │
│   ├── app/
│   │   ├── layout.tsx             # Root layout
│   │   ├── page.tsx               # Home / Dashboard
│   │   ├── chat/
│   │   │   ├── page.tsx           # /chat (new session)
│   │   │   └── [id]/page.tsx      # /chat/session-123 (bookmarkable)
│   │   ├── analytics/
│   │   │   └── page.tsx           # /analytics (usage stats)
│   │   ├── control/
│   │   │   └── page.tsx           # /control (settings, API keys)
│   │   └── api/
│   │       └── (optional: Next.js API routes)
│   │
│   ├── components/
│   │   ├── AgentStatus.tsx        # Real-time agent status
│   │   ├── ChatBox.tsx            # Chat interface
│   │   ├── VaultContext.tsx       # Show vault context used
│   │   ├── SessionHistory.tsx     # List of sessions
│   │   ├── Sidebar.tsx            # Left panel (nav + agents)
│   │   └── ...
│   │
│   ├── lib/
│   │   ├── api.ts                 # Fetch client
│   │   ├── types.ts               # TypeScript types
│   │   └── utils.ts               # Helpers
│   │
│   └── styles/
│       └── globals.css
│
├── .env.example
├── .dockerignore
├── .gitignore
├── docker-compose.yml             # Full stack
├── CLAUDE.md                       # This file
├── README.md
└── docs/
    ├── architecture.md
    ├── setup.md
    └── api-reference.md
```

---

## 5. Core Features (In Priority Order)

### Feature 1: Obsidian Memory Layer ⭐ (The Secret Sauce)

**Files:**
- `agentic_os/memory/obsidian_sync.py`
- `agentic_os/memory/indexer.py`
- `agentic_os/memory/context.py`

**What it does:**
1. Watches your Obsidian vault for file changes
2. Parses `.md` files → extracts goals, journal, schedule, concepts
3. Stores metadata in local SQLite
4. On task submission, injects relevant vault snippets into agent prompts

**Implementation sketch:**
```python
# agentic_os/memory/obsidian_sync.py
from watchdog.observers import Observer
from pathlib import Path
import frontmatter

class ObsidianVault:
    def __init__(self, vault_path: str):
        self.vault_path = Path(vault_path)
        self.observer = Observer()
        self.observer.schedule(VaultChangeHandler(self), self.vault_path)
    
    def parse_file(self, file_path: str):
        """Parse .md file, extract frontmatter + content"""
        with open(file_path, 'r') as f:
            post = frontmatter.load(f)
        
        # Extract: goals, date, tags, links
        return {
            'title': post.get('title'),
            'date': post.get('date'),
            'tags': post.get('tags', []),
            'goals': post.get('goals', []),
            'content': post.content
        }
    
    def start_watching(self):
        self.observer.start()

# agentic_os/memory/context.py
class ContextInjector:
    def __init__(self, vault: ObsidianVault, db):
        self.vault = vault
        self.db = db
    
    def get_context_for_task(self, task: str) -> str:
        """
        Search vault for relevant snippets.
        Return formatted context string for system prompt.
        """
        # Search by keywords in task
        relevant_files = self.db.search_files(task, limit=3)
        
        context = "## Your Context\n"
        for file in relevant_files:
            context += f"- {file['title']}: {file['excerpt']}\n"
        
        return context
```

**Usage in agent prompt:**
```python
system_prompt = f"""
You are Claude, an AI assistant.
{context_injector.get_context_for_task(task)}

Now help with: {task}
"""
```

---

### Feature 2: Three Agents (Claude, Hermes, OpenClaw)

#### Agent 2a: Claude Agent (Code + Planning)

**File:** `agents/claude.py`

```python
from agentic_os.agents.base import BaseAgent

class ClaudeAgent(BaseAgent):
    name = "claude"
    description = "Code generation, planning, analysis"
    provider = "anthropic"
    tools = ["files", "shell", "web"]
    
    async def run(self, task: str, context: str = "") -> str:
        # 1. Get vault context
        vault_context = await self.context_injector.get_context_for_task(task)
        
        # 2. Build prompt
        system_prompt = f"""
        You are Claude, expert AI assistant.
        
        {vault_context}
        
        You have access to:
        - file: read/write files
        - shell: execute commands
        - web: search the web
        """
        
        # 3. Call Claude
        response = await self.llm.generate(
            prompt=task,
            system=system_prompt,
            model="claude-opus-4-6"
        )
        
        # 4. Execute tools if needed (parse [TOOL: ...] markers)
        # 5. Return final result
        return response
```

#### Agent 2b: Hermes Agent (Research + Data)

**File:** `agents/hermes.py`

```python
class HermesAgent(BaseAgent):
    name = "hermes"
    description = "Web research, data aggregation"
    provider = "openrouter"  # Free/cheap via OpenRouter
    tools = ["web_search", "web_scrape"]
    
    async def run(self, task: str) -> str:
        # Similar to Claude but specialized for research
        # Uses Hermes model via OpenRouter API
        # Can run cheaper/faster for data tasks
        pass
```

#### Agent 2c: OpenClaw Agent (Router)

**File:** `agents/openclaw.py`

**The key innovation: explicitly routes tasks**

```python
class OpenClawAgent(BaseAgent):
    name = "openclaw"
    description = "Task router"
    
    async def route(self, task: str) -> tuple[str, str]:
        """
        Analyze task, decide: Claude or Hermes?
        Return (agent_name, reason)
        """
        
        # Use Claude to analyze
        prompt = f"""
        Task: {task}
        
        Should this go to:
        A) Claude (code, planning, file operations, detailed analysis)
        B) Hermes (research, web data, quick summaries)
        
        Respond with A or B only.
        """
        
        decision = await self.llm.generate(prompt)
        
        if 'A' in decision:
            return ("claude", "Requires code/detailed work")
        else:
            return ("hermes", "Requires research/data")
    
    async def execute(self, task: str) -> dict:
        """
        Route task + execute appropriate agent
        """
        agent_name, reason = await self.route(task)
        
        agent = self.get_agent(agent_name)
        result = await agent.run(task)
        
        return {
            "routed_to": agent_name,
            "routing_reason": reason,
            "result": result
        }
```

---

### Feature 3: LLM Adapter (Multi-Provider)

**File:** `agentic_os/llm/adapter.py`

```python
class LLMAdapter:
    def __init__(self, config):
        self.claude_client = AsyncAnthropic(api_key=config.ANTHROPIC_API_KEY)
        self.hermes_client = httpx.AsyncClient(
            base_url="https://openrouter.ai/api/v1",
            headers={"Authorization": f"Bearer {config.OPENROUTER_API_KEY}"}
        )
    
    async def generate(
        self,
        prompt: str,
        system: str = "",
        model: str = "claude-opus-4-6",
        temperature: float = 0.7
    ) -> str:
        """
        Provider-agnostic generate.
        Auto-selects based on model name.
        """
        
        if "claude" in model:
            return await self._call_claude(prompt, system, model)
        elif "hermes" in model:
            return await self._call_hermes(prompt, system, model)
    
    async def _call_claude(self, prompt, system, model):
        message = await self.claude_client.messages.create(
            model=model,
            max_tokens=1024,
            system=system,
            messages=[{"role": "user", "content": prompt}]
        )
        return message.content[0].text
    
    async def _call_hermes(self, prompt, system, model):
        response = await self.hermes_client.post(
            "/chat/completions",
            json={
                "model": model,
                "messages": [
                    {"role": "system", "content": system},
                    {"role": "user", "content": prompt}
                ]
            }
        )
        return response.json()["choices"][0]["message"]["content"]
```

---

### Feature 4: Tools (Files, Shell, Web)

**File:** `agentic_os/tools/files.py`

```python
class FilesTool(BaseTool):
    name = "files"
    
    async def read(self, path: str) -> str:
        # Read file content
        with open(path, 'r') as f:
            return f.read()
    
    async def write(self, path: str, content: str) -> str:
        # Write file
        with open(path, 'w') as f:
            f.write(content)
        return f"Wrote {len(content)} bytes to {path}"
    
    async def list_dir(self, path: str) -> list:
        # List directory
        return [f.name for f in Path(path).iterdir()]
```

**File:** `agentic_os/tools/shell.py`

```python
class ShellTool(BaseTool):
    name = "shell"
    
    async def execute(self, command: str, timeout: int = 30) -> str:
        # Execute shell command
        result = await asyncio.create_subprocess_shell(
            command,
            stdout=asyncio.subprocess.PIPE
        )
        stdout, _ = await asyncio.wait_for(
            result.communicate(),
            timeout=timeout
        )
        return stdout.decode()
```

**File:** `agentic_os/tools/web.py`

```python
class WebTool(BaseTool):
    name = "web"
    
    async def search(self, query: str) -> list:
        # Google/web search via API
        # Return top 5 results
        pass
```

---

### Feature 5: API Endpoints

**File:** `agentic_os/api/main.py`

```python
from fastapi import FastAPI

app = FastAPI(title="Agent OS")

@app.post("/api/chat")
async def submit_task(task: str, session_id: str = None) -> dict:
    """
    Submit a task.
    Returns session_id (bookmarkable URL).
    """
    if not session_id:
        session_id = str(uuid.uuid4())
    
    # Store task in DB
    session = Session(id=session_id, task=task)
    db.add(session)
    db.commit()
    
    # Route via OpenClaw
    result = await openclaw.execute(task)
    
    # Store result
    session.result = result['result']
    session.routed_to = result['routed_to']
    db.commit()
    
    return {
        "session_id": session_id,
        "status": "completed",
        "result": result
    }

@app.get("/api/sessions")
async def list_sessions() -> list:
    """List all chat sessions (for history)"""
    sessions = db.query(Session).all()
    return [s.to_dict() for s in sessions]

@app.get("/api/sessions/{session_id}")
async def get_session(session_id: str) -> dict:
    """Get specific session (bookmarkable)"""
    session = db.query(Session).filter_by(id=session_id).first()
    return session.to_dict()

@app.get("/api/agents")
async def agent_status() -> dict:
    """Get real-time status of all agents"""
    return {
        "claude": {"status": "online", "last_seen": "now"},
        "hermes": {"status": "online", "last_seen": "now"},
        "openclaw": {"status": "online", "last_seen": "now"}
    }

@app.get("/api/vault/sync")
async def vault_sync_status() -> dict:
    """Get Obsidian vault sync status"""
    return {
        "vault_path": config.OBSIDIAN_VAULT_PATH,
        "last_sync": "2 minutes ago",
        "indexed_files": 145,
        "total_files": 147
    }
```

---

### Feature 6: Next.js Frontend (Mission Control)

**File:** `frontend/app/page.tsx`

```typescript
import { AgentStatus } from '@/components/AgentStatus'
import { ChatBox } from '@/components/ChatBox'
import { VaultContext } from '@/components/VaultContext'

export default function Home() {
  return (
    <div className="flex h-screen">
      {/* Sidebar */}
      <aside className="w-64 bg-gray-900 text-white p-4">
        <h1 className="text-2xl font-bold mb-4">Agent OS</h1>
        
        {/* Agent status */}
        <div className="space-y-2">
          <AgentStatus agent="claude" />
          <AgentStatus agent="hermes" />
          <AgentStatus agent="openclaw" />
        </div>
        
        {/* Session history */}
        <div className="mt-6">
          <h2 className="text-lg font-bold mb-2">Sessions</h2>
          <SessionList />
        </div>
      </aside>
      
      {/* Main area */}
      <main className="flex-1 p-6">
        <h1 className="text-3xl font-bold mb-4">Mission Control</h1>
        
        <div className="grid grid-cols-3 gap-4 mb-6">
          <AgentStatus agent="claude" />
          <AgentStatus agent="hermes" />
          <AgentStatus agent="openclaw" />
        </div>
        
        <ChatBox />
      </main>
    </div>
  )
}
```

**File:** `frontend/app/chat/[id]/page.tsx` (Bookmarkable sessions)

```typescript
export default async function ChatSession({ params }) {
  const { id } = params
  
  // Fetch session from API
  const session = await fetch(`http://localhost:8000/api/sessions/${id}`)
    .then(r => r.json())
  
  return (
    <div>
      <h1>{session.task}</h1>
      <ChatMessage role="user">{session.task}</ChatMessage>
      <ChatMessage role="assistant">{session.result}</ChatMessage>
      <VaultContext context={session.vault_context} />
    </div>
  )
}
```

**File:** `frontend/app/analytics/page.tsx`

```typescript
export default function Analytics() {
  return (
    <div className="p-6">
      <h1 className="text-3xl font-bold mb-6">AI Screen Time Analytics</h1>
      
      <div className="grid grid-cols-2 gap-4">
        <MetricCard title="Sessions (30d)" value="47" />
        <MetricCard title="Tool Calls" value="324" />
        <MetricCard title="Most Used Model" value="Claude Opus" />
        <MetricCard title="Peak Hour" value="2-3 PM" />
      </div>
      
      <UsageChart />
    </div>
  )
}
```

**File:** `frontend/app/control/page.tsx` (Config)

```typescript
export default function Control() {
  const [apiKey, setApiKey] = useState('')
  const [vaultPath, setVaultPath] = useState('')
  
  return (
    <div className="p-6">
      <h1 className="text-3xl font-bold mb-6">Control Room</h1>
      
      <div className="space-y-4">
        <input
          placeholder="Anthropic API Key"
          value={apiKey}
          onChange={(e) => setApiKey(e.target.value)}
          className="w-full p-2 border rounded"
        />
        
        <input
          placeholder="Obsidian Vault Path"
          value={vaultPath}
          onChange={(e) => setVaultPath(e.target.value)}
          className="w-full p-2 border rounded"
        />
        
        <button
          onClick={() => saveConfig()}
          className="bg-blue-500 text-white px-4 py-2 rounded"
        >
          Save
        </button>
      </div>
    </div>
  )
}
```

---

## 6. Day 1 Implementation Plan

### You (Person 1): Memory Layer (4 hours)

**Task 1: Obsidian Vault Watcher** (1.5h)
```bash
# Ask Claude Code:
"Implement agentic_os/memory/obsidian_sync.py with:
- Watchdog file system monitor
- Detect new/modified .md files in vault
- Call parser when file changes
- Test: Create sample .md file, verify detection"
```

**Task 2: Markdown Parser** (1.5h)
```bash
# Ask Claude Code:
"Implement agentic_os/memory/indexer.py with:
- Parse frontmatter (YAML front matter)
- Extract: title, date, goals, tags
- Parse markdown content
- Store in SQLite
- Test: Parse sample vault file"
```

**Task 3: Context Injector** (1h)
```bash
# Ask Claude Code:
"Implement agentic_os/memory/context.py with:
- search_vault(query) function
- Format results as markdown for system prompt
- Test: Inject context into Claude prompt"
```

---

### Your Partner (Person 2): Agents + API (4 hours)

**Task 1: Claude Agent** (1.5h)
```bash
# Ask Claude Code:
"Implement agents/claude.py with:
- Extends BaseAgent
- Calls Anthropic API
- Receives vault context
- Access to files + shell tools
- Test: Ask Claude a question"
```

**Task 2: OpenClaw Router** (1.5h)
```bash
# Ask Claude Code:
"Implement agents/openclaw.py with:
- route(task) → decision: 'claude' or 'hermes'
- execute(task) → calls appropriate agent
- Test: Route a code task to Claude"
```

**Task 3: API Endpoint** (1h)
```bash
# Ask Claude Code:
"Implement agentic_os/api/chat.py with:
- POST /api/chat {task} endpoint
- Routes to OpenClaw
- Stores session in SQLite
- Returns session_id
- Test: curl to /api/chat"
```

---

### Together (Both): Next.js UI (2 hours)

**Task 1: Basic UI** (1h)
```bash
# Ask Claude Code:
"Create Next.js app/page.tsx with:
- Sidebar showing Claude, Hermes, OpenClaw status
- Input box to submit task
- Display task result below
- Use Tailwind CSS"
```

**Task 2: Bookmarkable Sessions** (1h)
```bash
# Ask Claude Code:
"Create app/chat/[id]/page.tsx with:
- Fetch /api/sessions/{id}
- Display task + result
- Show vault context used
- Make it bookmarkable (shareable URL)"
```

---

## 7. Running Day 1 Build

### Setup
```bash
# Backend
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# .env file
cp .env.example .env
# Edit: ANTHROPIC_API_KEY, OBSIDIAN_VAULT_PATH

# Frontend
cd ../frontend
npm install

# Init DB
python -m agentic_os.db.init
```

### Run Everything
```bash
# Terminal 1: Backend
cd backend
uvicorn agentic_os.api.main:app --reload

# Terminal 2: Vault watcher
python -m agentic_os.memory.obsidian_sync

# Terminal 3: Frontend
cd frontend
npm run dev

# Open http://localhost:3000
```

### Test End-to-End
```bash
# Terminal 4: Submit task
curl -X POST http://localhost:8000/api/chat \
  -H "Content-Type: application/json" \
  -d '{"task": "Write hello world in Python"}'

# Expected:
# {
#   "session_id": "abc-123",
#   "status": "completed",
#   "result": "created hello.py...",
#   "routed_to": "claude"
# }

# Then visit: http://localhost:3000/chat/abc-123
```

---

## 8. Success Criteria (Day 1 Done)

- [x] Obsidian vault monitored for changes
- [x] .md files parsed → metadata indexed in SQLite
- [x] Claude agent receives vault context in prompt
- [x] OpenClaw routes tasks (decides Claude or Hermes)
- [x] `/api/chat` endpoint works
- [x] Next.js displays agent response
- [x] Sessions are bookmarkable (shareable URLs)
- [x] All tests pass

---

## 9. What NOT to Do Day 1

- ✗ Hermes agent (defer to Day 2)
- ✗ Cost tracking (defer)
- ✗ Analytics dashboard (defer)
- ✗ Real-time updates (WebSocket)
- ✗ Docker Compose (test manually only)
- ✗ Advanced NLP (simple keyword search OK)

---

## 10. Config Files

### `.env.example`
```bash
# APIs
ANTHROPIC_API_KEY=sk-ant-...
OPENROUTER_API_KEY=...

# Paths
OBSIDIAN_VAULT_PATH=/Users/you/Notes
AGENT_OS_DATA_PATH=./data

# Database
DATABASE_URL=sqlite:///./agent_os.db

# Server
BACKEND_URL=http://localhost:8000
FRONTEND_URL=http://localhost:3000
```

### `config/default.yaml` (Backend config)
```yaml
agents:
  claude:
    provider: anthropic
    model: claude-opus-4-6
    temperature: 0.7
  hermes:
    provider: openrouter
    model: nous-hermes-2-mixtral-8x7b-dpo
    temperature: 0.5
  openclaw:
    provider: anthropic
    model: claude-opus-4-6

memory:
  obsidian_vault_path: ${OBSIDIAN_VAULT_PATH}
  sync_interval_seconds: 30
  max_context_length: 2000

api:
  host: 0.0.0.0
  port: 8000
  reload: true

logging:
  level: INFO
  format: json
```

---

## 11. Key Files to Implement

**Backend (Priority)**
```
agentic_os/memory/obsidian_sync.py    ← START HERE
agentic_os/memory/indexer.py
agentic_os/memory/context.py
agents/claude.py
agents/openclaw.py
agentic_os/api/chat.py
agentic_os/llm/adapter.py
agentic_os/tools/files.py
agentic_os/tools/shell.py
```

**Frontend (Priority)**
```
app/page.tsx
app/chat/[id]/page.tsx
components/ChatBox.tsx
components/AgentStatus.tsx
lib/api.ts
```

---

## 12. Questions to Ask Claude Code

### Day 1, Morning (You)
```
"Implement agentic_os/memory/obsidian_sync.py:
- Use watchdog library to monitor vault directory
- When .md file changes, parse it
- Extract frontmatter (title, date, goals, tags)
- Extract markdown content
- Store metadata in SQLite (agentic_os.db)
- Include error handling and logging

Reference structure in CLAUDE.md.
Write tests in tests/test_memory.py."
```

### Day 1, Morning (Partner)
```
"Implement agents/claude.py as a complete agent:
- Extend BaseAgent class
- Initialize Anthropic client from config
- run(task) method that:
  1. Gets vault context
  2. Builds system prompt with context
  3. Calls Claude API
  4. Returns response
- Include error handling

Also implement agentic_os/api/chat.py with POST /api/chat endpoint."
```

### Day 1, Afternoon (Together)
```
"Create Next.js app with:
- app/page.tsx showing agent status + chat box
- app/chat/[id]/page.tsx for bookmarkable sessions
- Use Tailwind CSS
- Fetch from http://localhost:8000/api/chat
- Display results in markdown"
```

---

## 13. Directory Structure Checklist

Before starting, ensure these directories exist:

```bash
mkdir -p backend/agentic_os/{agents,memory,llm,tools,api,models,db,config,utils}
mkdir -p backend/tests/{fixtures,unit,integration}
mkdir -p frontend/app/{chat,analytics,control}
mkdir -p frontend/components
mkdir -p frontend/lib
mkdir -p frontend/styles
```

---

## 14. Git & Version Control

### Initial commits
```bash
# Commit 1: Project skeleton
git add .
git commit -m "Initial project structure"

# Commit 2: Core agents + API
git commit -m "Add Claude + OpenClaw agents + /api/chat"

# Commit 3: Memory layer
git commit -m "Add Obsidian vault sync + indexing"

# Commit 4: Frontend
git commit -m "Add Next.js dashboard"
```

---

## 15. Testing Strategy

### Unit Tests (Day 1+)
```python
# tests/test_memory.py
def test_obsidian_parse():
    # Parse sample .md, verify extraction

def test_context_injection():
    # Inject context, verify format

# tests/test_agents.py
def test_claude_agent_runs():
    # Call Claude agent, verify response

def test_openclaw_routes():
    # Route code task, verify goes to Claude
```

### Integration Tests (Day 2)
```
# Full flow: task → API → OpenClaw → Claude → result
```

### Manual Tests (Day 1)
```bash
# Terminal 1: Start backend
uvicorn agentic_os.api.main:app --reload

# Terminal 2: Start vault watcher
python -m agentic_os.memory.obsidian_sync

# Terminal 3: Frontend
npm run dev

# Terminal 4: Manual test
curl http://localhost:8000/api/chat?task="write%20hello%20world"
```

---

## 16. Debugging Tips

If memory sync doesn't work:
- Check `OBSIDIAN_VAULT_PATH` in `.env`
- Verify vault path exists
- Check logs: `cat logs/obsidian_sync.log`

If Claude doesn't respond:
- Verify `ANTHROPIC_API_KEY` is valid
- Check API quota on Anthropic dashboard
- Try simple prompt first

If API endpoint returns 500:
- Check backend console output
- Verify SQLite DB is initialized
- Check database logs

If frontend doesn't connect:
- Verify backend is running on :8000
- Check CORS (should be enabled in FastAPI)
- Check browser console for errors

---

## 17. What Comes Next (Day 2+)

**Day 2:**
- Hermes agent + OpenRouter integration
- Session analytics
- Improved vault search (semantic)

**Day 3:**
- Docker Compose full stack
- Real-time updates (WebSocket)
- API documentation

**Week 2:**
- Advanced features (DAGs, scheduling)
- Mobile-responsive UI
- Production deployment

---

## Summary: You're Building

A **local AI operating system** that:
1. Reads your Obsidian vault (your brain)
2. Routes tasks intelligently (OpenClaw)
3. Executes with Claude or Hermes (your hands)
4. Shows everything in a beautiful Next.js UI (your dashboard)
5. Keeps everything local (your privacy)

**Day 1 goal:** Prove the loop works end-to-end.

By end of Day 1: Task → Obsidian context → Claude → Result → Bookmarkable URL.

---

**Ready to start? Choose your role and ask Claude Code.**

