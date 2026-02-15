# Model Context Protocol (MCP)

> **Organization**: Anthropic  
> **Website**: [modelcontextprotocol.io](https://modelcontextprotocol.io)  
> **Status**: Production Ready | Widely Adopted

## 🎯 What is MCP?

The **Model Context Protocol** is an open standard for connecting AI assistants to external data sources, tools, and services. Think of it as "USB for AI" — a universal interface that lets any AI model talk to any tool.

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     AI Application                          │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                   MCP Client                         │   │
│  │  (Claude Desktop, Cursor, IDEs, Custom Apps)        │   │
│  └─────────────────────────────────────────────────────┘   │
│                           │                                 │
│                    MCP Protocol                             │
│            (JSON-RPC over stdio/HTTP)                       │
│                           │                                 │
│  ┌─────────┬─────────┬─────────┬─────────┬─────────┐       │
│  │  MCP    │  MCP    │  MCP    │  MCP    │  MCP    │       │
│  │ Server  │ Server  │ Server  │ Server  │ Server  │       │
│  │(GitHub) │ (Slack) │(Browser)│ (Files) │(Custom) │       │
│  └─────────┴─────────┴─────────┴─────────┴─────────┘       │
└─────────────────────────────────────────────────────────────┘
```

## 🧩 Core Concepts

### 1. Resources
Contextual data the AI can read:
- Files and documents
- Database records
- API responses
- Real-time data streams

### 2. Tools
Actions the AI can perform:
- Execute code
- Call APIs
- Modify files
- Send messages

### 3. Prompts
Reusable prompt templates:
- Pre-defined workflows
- Domain-specific instructions
- Best practice patterns

### 4. Sampling
Request LLM completions from servers:
- Nested AI calls
- Multi-model workflows
- Human-in-the-loop patterns

## 💻 Quick Start

### Creating an MCP Server (Python)

```python
from mcp.server import Server
from mcp.types import Tool, TextContent

server = Server("my-server")

@server.tool()
async def get_weather(city: str) -> str:
    """Get current weather for a city."""
    # Your implementation
    return f"Weather in {city}: Sunny, 72°F"

@server.resource("weather://{city}")
async def weather_resource(city: str) -> str:
    """Weather data as a resource."""
    return await get_weather(city)

if __name__ == "__main__":
    server.run()
```

### Creating an MCP Server (TypeScript)

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server({
  name: "my-server",
  version: "1.0.0"
});

server.setRequestHandler("tools/list", async () => ({
  tools: [{
    name: "get_weather",
    description: "Get weather for a city",
    inputSchema: {
      type: "object",
      properties: {
        city: { type: "string" }
      },
      required: ["city"]
    }
  }]
}));

server.setRequestHandler("tools/call", async (request) => {
  if (request.params.name === "get_weather") {
    return {
      content: [{ type: "text", text: `Weather: Sunny` }]
    };
  }
});

const transport = new StdioServerTransport();
server.connect(transport);
```

## 🌐 MCP Apps: Interactive UIs in Chat

One of MCP's powerful features is **MCP Apps** — the ability to render interactive UI components directly within chat interfaces.

### Example: Interactive Dashboard

```typescript
server.setRequestHandler("tools/call", async (request) => {
  return {
    content: [{
      type: "app",
      app: {
        type: "chart",
        data: {
          labels: ["Jan", "Feb", "Mar"],
          datasets: [{
            label: "Revenue",
            data: [100, 150, 200]
          }]
        }
      }
    }]
  };
});
```

This renders an actual interactive chart in the chat, not just text!

## 📊 Adoption

| Client | Status | Notes |
|--------|--------|-------|
| Claude Desktop | ✅ Full Support | Official reference client |
| Cursor | ✅ Full Support | Popular AI IDE |
| Continue | ✅ Full Support | Open-source extension |
| Windsurf | ✅ Full Support | Codeium's IDE |
| Zed | 🔄 In Progress | Collaborative editor |
| VS Code | 🔄 Community | Via extensions |

## 🔧 Available Servers

### Official
- `@modelcontextprotocol/server-filesystem` - File system access
- `@modelcontextprotocol/server-github` - GitHub integration
- `@modelcontextprotocol/server-slack` - Slack messaging
- `@modelcontextprotocol/server-postgres` - PostgreSQL database
- `@modelcontextprotocol/server-brave-search` - Web search

### Community
- `mcp-server-browser` - Browser automation
- `mcp-server-docker` - Docker management
- `mcp-server-kubernetes` - K8s operations
- `mcp-server-notion` - Notion workspace
- 100+ more on GitHub

## 🔑 Key Benefits

1. **Vendor Agnostic**: Works with any AI model
2. **Reusable**: Build once, use everywhere
3. **Secure**: Explicit permissions model
4. **Composable**: Chain servers together
5. **Local-First**: Servers run on your machine

## ⚠️ Limitations

- **Not a replacement for fine-tuning**: MCP extends capabilities, doesn't change the model
- **Latency**: External calls add latency
- **Complexity**: More moving parts to manage
- **Security surface**: Each server is a potential attack vector

## 📄 Specification

```yaml
# mcp.json configuration example
{
  "servers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "filesystem": {
      "command": "npx", 
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed"]
    }
  }
}
```

## 🔗 Resources

- **Spec**: [spec.modelcontextprotocol.io](https://spec.modelcontextprotocol.io)
- **SDK (Python)**: [github.com/modelcontextprotocol/python-sdk](https://github.com/modelcontextprotocol/python-sdk)
- **SDK (TypeScript)**: [github.com/modelcontextprotocol/typescript-sdk](https://github.com/modelcontextprotocol/typescript-sdk)
- **Server Directory**: [github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)
- **Discussion**: [github.com/orgs/modelcontextprotocol/discussions](https://github.com/orgs/modelcontextprotocol/discussions)

## 📚 Related Protocols

- [A2UI](a2ui.md) - Google's agent-to-UI protocol
- [OpenAI Function Calling](openai-functions.md) - OpenAI's tool use API
- [Agent Protocol](agent-protocol.md) - Universal agent API

---

*Added: February 2026*
