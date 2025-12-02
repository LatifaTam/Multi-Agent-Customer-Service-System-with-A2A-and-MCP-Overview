# Multi-Agent Customer Service System

A2A + MCP + Google ADK Demo

This project demonstrates a multi-agent customer service architecture combining:

* Google ADK LLM Agents
* Agent-to-Agent (A2A) messaging
* MCP (Model Context Protocol) tools
* A Flask MCP server backed by SQLite
* A Host/Router agent orchestrating two specialized agents

The system exposes three agents over HTTP (via uvicorn/A2A):

| Agent                  | Port  | Description                                             |
| ---------------------- | ----- | ------------------------------------------------------- |
| Customer Data Agent    | 10020 | Retrieves database records via MCP tools                |
| Customer Support Agent | 10021 | Handles support reasoning and decisions                 |
| Host Agent (Router)    | 10022 | Breaks queries into subtasks and routes to other agents |

A local MCP server exposes database tools and is optionally published through ngrok to allow LLM agents to call MCP tools remotely.

---

## 1. Environment Setup

Always use a Python virtual environment for isolation.

### 1.1 Create and activate venv

```bash
python3 -m venv venv
source venv/bin/activate     # Mac/Linux
venv\Scripts\activate        # Windows
```

---

## 2. Install Requirements

Install:

```bash
pip install -r requirements.txt
```

---

## 3. Set Environment Variables

Create a `.env` file:

```
GOOGLE_API_KEY=your_key_here
MCP_SERVER_URL=https://your-ngrok-url.ngrok-free.dev
```

Or export manually:

```bash
export GOOGLE_API_KEY="your_key"
export MCP_SERVER_URL="your_ngrok_url"
```

---

## 4. Initialize the Database

Run:

```bash
python database_setup.py
```

This creates:

* support.db
* customers table
* tickets table

---

## 5. Run the MCP Server

Start the MCP server and Expose it with ngrok (optional):

```bash
ngrok http 5001
```

Copy the HTTPS URL into your `.env`.

---

## 6. Start All Three A2A Agents
Expected output:

```
Customer Data Agent: http://127.0.0.1:10020
Customer Support Agent: http://127.0.0.1:10021
Host Agent: http://127.0.0.1:10022
```

---

## 7. Test the System

Example host agent query:

```python
asyncio.run(
    a2a_client.create_task(
        "http://localhost:10022",
        "Show me all active customers who have open tickets"
    )
)
```

---

## 8. Restarting and Cleaning Ports

When updating agent code, restart all servers.

Kill uvicorn processes:

```bash
pkill -f uvicorn
```

Reset notebook runtime (for Colab):

```
Runtime → Restart runtime
```

If a port is stuck:

```bash
lsof -i :10020
kill -9 <PID>
```

Repeat for ports 10021 and 10022 if needed.

