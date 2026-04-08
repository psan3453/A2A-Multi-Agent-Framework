# 🤖 A2A MCP Master Class

A comprehensive multi-agent system built from scratch using the **Model Context Protocol (MCP)** and **Agent-to-Agent (A2A)** communication protocols, powered by **Google Gemini AI** and the **Google Agent Development Kit (ADK)**.

---

## 📌 What This Project Does

This project demonstrates how to build a production-style multi-agent architecture where:

- A **Host Agent** acts as the central orchestrator — it receives user requests, discovers available tools and agents, and delegates tasks intelligently
- A **Website Builder Agent** handles web page creation tasks
- **MCP Servers** expose tools (like terminal commands and arithmetic) that agents can call
- Agents communicate with each other using the **A2A protocol**
- Tool discovery is dynamic — agents find available servers and tools at runtime from a config file

---

## 🏗️ Architecture Overview

```
User (CMD App)
     │
     ▼
Host Agent  ──── discovers ────► MCP Servers (terminal, arithmetic)
     │
     └──── delegates ──────────► Website Builder Agent (A2A)
```

### Communication Protocols
- **MCP (Model Context Protocol):** Used to connect agents to tools via Streamable HTTP or STDIO transport
- **A2A (Agent-to-Agent):** Used for agent discovery and task delegation between agents

---

## 📁 Project Structure

```
A2A_MCP/
├── agents/
│   ├── host_agent/               # Central orchestrator agent
│   │   ├── agent.py              # Agent logic and tool loading
│   │   ├── agent_executor.py     # Executor for handling A2A requests
│   │   ├── __main__.py           # Entry point
│   │   ├── instructions.txt      # Gemini system prompt
│   │   └── description.txt       # Agent description for discovery
│   └── website_builder_simple/   # Specialized web-building agent
│       ├── agent.py
│       ├── agent_executor.py
│       └── __main__.py
├── app/
│   └── cmd/
│       └── cmd.py                # CLI interface to interact with the system
├── mcp_local/
│   └── servers/
│       ├── streamable_http_server.py   # MCP server over HTTP (arithmetic tools)
│       └── terminal_server/
│           └── terminal_server.py      # MCP server for running terminal commands
├── utilities/
│   ├── a2a/
│   │   ├── agent_connect.py      # A2A task sender
│   │   └── agent_discovery.py    # Discovers registered A2A agents
│   ├── mcp/
│   │   ├── mcp_connect.py        # Loads MCP toolsets for ADK
│   │   ├── mcp_discovery.py      # Reads mcp_config.json
│   │   └── mcp_config.json       # MCP server definitions
│   └── common/
│       └── file_loader.py        # Utility for loading instruction files
├── .env                          # API keys (not committed to git)
├── pyproject.toml                # Project dependencies
└── uv.lock                       # Locked dependency versions
```

---

## ⚙️ Prerequisites

- Python 3.11+
- [`uv`](https://github.com/astral-sh/uv) package manager
- Google Gemini API key (free tier from [Google AI Studio](https://aistudio.google.com/app/apikey))


---

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/psan3453/A2A-Multi-Agent-Framework.git
cd A2A-Multi-Agent-Framework
```

### 2. Create and activate virtual environment

**Windows:**
```powershell
python -m venv .venv
.venv\Scripts\activate
```

**macOS/Linux:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
uv sync
```

### 4. Set up environment variables

Create a `.env` file in the project root:

```
GOOGLE_API_KEY=your_gemini_api_key_here
```

Get your free API key from [Google AI Studio](https://aistudio.google.com/app/apikey).

### 5. Configure MCP servers

Edit `utilities/mcp/mcp_config.json` and update paths to match your system:

**Windows:**
```json
{
  "mcpServers": {
    "terminal_server": {
      "command": "uv",
      "args": [
        "--directory",
        "C:\\path\\to\\A2A_MCP\\mcp_local\\servers\\terminal_server",
        "run",
        "terminal_server.py"
      ]
    },
    "arithmetic_server": {
      "command": "streamable_http",
      "args": [
        "http://localhost:3000/mcp/"
      ]
    }
  }
}
```

---

## ▶️ Running the Project

Open **4 separate terminals** and run each command in order:

**Terminal 1 — MCP HTTP Server (arithmetic tools):**
```powershell
uv run python -m mcp_local.servers.streamable_http_server
```

**Terminal 2 — Website Builder Agent:**
```powershell
uv run python -m agents.website_builder_simple
```

**Terminal 3 — Host Agent:**
```powershell
uv run python -m agents.host_agent
```

**Terminal 4 — CMD App (talk to the system):**
```powershell
uv run python -m app.cmd.cmd
```

> ⚠️ Always start Terminal 1 first and Terminal 4 last.

---

## 💬 Example Prompts

Once all terminals are running, type in Terminal 4:

```
list available agents
```
```
add 25 and 75
```
```
delegate to website_builder_simple: create a minimal HTML page with title Hello World
```

---

## 🔧 How It Works

1. **User** types a prompt in the CMD app
2. CMD app sends the message to the **Host Agent** via A2A protocol
3. Host Agent uses **Gemini AI** to understand the request
4. Gemini picks the right tool or agent:
   - MCP tools (terminal commands, arithmetic) → called directly
   - Specialized tasks → delegated to child agents via A2A
5. Results are returned back to the user

---
