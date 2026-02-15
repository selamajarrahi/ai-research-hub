# A2UI (Agent-to-UI Protocol)

> **Organization**: Google  
> **Website**: [a2ui.org](https://a2ui.org)  
> **Status**: Emerging Standard | Growing Adoption

## 🎯 What is A2UI?

**A2UI** (Agent-to-UI) is Google's open protocol for AI agents to interact with graphical user interfaces. While MCP focuses on tools and data, A2UI focuses on **visual interaction** — letting agents see, understand, and manipulate UIs the way humans do.

## 🤔 Why A2UI?

Traditional automation approaches:
- **APIs**: Require explicit integration
- **Selenium/Puppeteer**: Brittle, break with UI changes
- **Screen coordinates**: Unreliable across resolutions

A2UI enables:
- **Semantic UI understanding**: Agent sees "Login button" not "pixels at (x,y)"
- **Cross-platform**: Works on web, mobile, desktop
- **Resilient**: Survives UI redesigns
- **Human-like**: Uses the same interface as users

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      AI Agent                               │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                   A2UI Client                        │   │
│  │    (Understands UI structure, plans actions)        │   │
│  └─────────────────────────────────────────────────────┘   │
│                           │                                 │
│                    A2UI Protocol                            │
│        (Semantic UI tree, action commands)                  │
│                           │                                 │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                   A2UI Runtime                       │   │
│  │    (Browser extension, mobile SDK, desktop app)     │   │
│  └─────────────────────────────────────────────────────┘   │
│                           │                                 │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                   Target UI                          │   │
│  │    (Web page, mobile app, desktop application)      │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## 🧩 Core Concepts

### 1. UI Snapshot
A semantic representation of the current UI state:

```json
{
  "type": "snapshot",
  "timestamp": "2026-02-14T21:00:00Z",
  "viewport": { "width": 1920, "height": 1080 },
  "elements": [
    {
      "ref": "e1",
      "role": "button",
      "name": "Sign In",
      "bounds": { "x": 100, "y": 50, "w": 80, "h": 40 },
      "states": ["enabled", "focusable"],
      "actions": ["click", "focus"]
    },
    {
      "ref": "e2", 
      "role": "textbox",
      "name": "Email",
      "bounds": { "x": 100, "y": 100, "w": 200, "h": 40 },
      "value": "",
      "states": ["enabled", "editable"],
      "actions": ["click", "type", "clear"]
    }
  ]
}
```

### 2. Actions
Commands the agent can execute:

```json
{
  "type": "action",
  "kind": "click",
  "ref": "e1"
}

{
  "type": "action",
  "kind": "type",
  "ref": "e2",
  "text": "user@example.com"
}

{
  "type": "action",
  "kind": "scroll",
  "direction": "down",
  "amount": 500
}
```

### 3. Navigation
High-level navigation commands:

```json
{
  "type": "navigate",
  "url": "https://example.com/dashboard"
}

{
  "type": "navigate",
  "action": "back"
}
```

## 💻 Usage Example

### Agent Flow

```python
from a2ui import A2UIClient

async def book_flight(client: A2UIClient, details: dict):
    # Navigate to airline website
    await client.navigate("https://airline.com")
    
    # Get current UI state
    snapshot = await client.snapshot()
    
    # Find and interact with elements semantically
    departure_field = snapshot.find(role="combobox", name="From")
    await client.type(departure_field.ref, details["from"])
    
    arrival_field = snapshot.find(role="combobox", name="To")
    await client.type(arrival_field.ref, details["to"])
    
    # Click search
    search_btn = snapshot.find(role="button", name="Search Flights")
    await client.click(search_btn.ref)
    
    # Wait for results
    await client.wait_for(role="list", name="Flight Results")
```

### With LLM Planning

```python
async def agent_driven_task(agent, client: A2UIClient, task: str):
    while not task_complete:
        # Get current UI
        snapshot = await client.snapshot()
        
        # Ask agent what to do
        action = await agent.decide(
            task=task,
            current_ui=snapshot.to_description(),
            history=action_history
        )
        
        # Execute action
        result = await client.execute(action)
        action_history.append((action, result))
```

## 📊 Comparison with Alternatives

| Approach | Resilience | Speed | Setup | Cross-Platform |
|----------|------------|-------|-------|----------------|
| A2UI | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Selenium | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| APIs | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐ | ⭐⭐ |
| Screen coords | ⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐ |
| Computer Use | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |

## 🔑 Key Features

1. **Semantic Element IDs**: Elements identified by role/name, not coordinates
2. **Accessibility-First**: Built on accessibility tree standards
3. **Action Validation**: Pre-flight checks before executing actions
4. **State Tracking**: Know when UI has changed
5. **Multi-Tab Support**: Handle complex workflows

## 🔧 Runtime Options

| Platform | Runtime | Status |
|----------|---------|--------|
| Chrome | A2UI Extension | ✅ Available |
| Firefox | A2UI Extension | ✅ Available |
| Android | A2UI SDK | ✅ Available |
| iOS | A2UI SDK | 🔄 Beta |
| Windows | A2UI Agent | 🔄 Beta |
| macOS | A2UI Agent | 🔄 Beta |

## ⚠️ Considerations

### Security
- Agents can see everything on screen
- Credential handling requires care
- Sandbox environments recommended

### Performance
- Snapshot generation adds latency
- Complex UIs = larger snapshots
- Efficient polling strategies needed

### Reliability
- Dynamic content may load after snapshot
- Animations can cause timing issues
- Fallback strategies important

## 🔗 Resources

- **Specification**: [a2ui.org/spec](https://a2ui.org/spec)
- **Chrome Extension**: [Chrome Web Store](https://chrome.google.com/webstore/...)
- **SDKs**: Available for Python, TypeScript, Java
- **Examples**: [github.com/a2ui/examples](https://github.com/a2ui/examples)

## 📚 Related Protocols

- [MCP](mcp.md) - Model Context Protocol for tools/data
- [Computer Use](../papers/computer-use.md) - Anthropic's vision-based approach
- [WebDriver](https://w3c.github.io/webdriver/) - W3C browser automation

## 🔮 Future Directions

- **A2UI 2.0**: Streaming snapshots for real-time UIs
- **Intent API**: High-level goals instead of low-level actions
- **Cross-Device**: Seamless handoff between devices

---

*Added: February 2026*
