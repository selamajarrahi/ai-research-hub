# Augment Code

> **Category**: AI Coding Assistant  
> **Website**: [augmentcode.com](https://augmentcode.com)  
> **Status**: Production | Enterprise Focus

## 🎯 What is Augment Code?

Augment Code is an AI-powered coding assistant built around a **Context Engine** that understands your entire codebase. Unlike tools that only see the current file, Augment builds and maintains a semantic index of your code, documentation, and development patterns.

## 🧠 The Context Engine

The key differentiator: Augment doesn't just autocomplete — it **understands**.

```
Traditional AI Assistants:
┌─────────────────────────────────────────────┐
│  Current File → LLM → Suggestion           │
│  (Limited context, misses dependencies)     │
└─────────────────────────────────────────────┘

Augment's Context Engine:
┌─────────────────────────────────────────────┐
│  ┌─────────────────────────────────────┐   │
│  │         Context Engine              │   │
│  │  ┌───────┬───────┬───────┐         │   │
│  │  │ Code  │ Docs  │ Git   │         │   │
│  │  │ Graph │ Index │History│         │   │
│  │  └───────┴───────┴───────┘         │   │
│  └─────────────────────────────────────┘   │
│                    ↓                        │
│  Relevant Context → LLM → Suggestion       │
│  (Full understanding of your codebase)      │
└─────────────────────────────────────────────┘
```

## 🔑 Key Features

### 1. Codebase Understanding
- Parses all languages in your repo
- Builds dependency graphs
- Tracks function/class relationships
- Indexes documentation

### 2. Intelligent Retrieval
- Retrieves relevant code snippets
- Pulls in related tests
- Includes relevant documentation
- Considers recent changes

### 3. Multi-File Edits
- Understands cross-file implications
- Suggests coordinated changes
- Maintains consistency

### 4. Chat Interface
- Ask questions about your codebase
- "How does the auth flow work?"
- "Show me where X is implemented"
- "What would break if I change Y?"

## 💻 Installation

### VS Code Extension

```bash
# Install from VS Code marketplace
code --install-extension augment.augment-vscode
```

### JetBrains Plugin

Available in JetBrains Marketplace for:
- IntelliJ IDEA
- PyCharm
- WebStorm
- Other JetBrains IDEs

### CLI

```bash
# Install CLI
npm install -g @augmentcode/cli

# Index your codebase
augment index .

# Chat with your codebase
augment chat "How does authentication work?"
```

## 📊 How It Compares

| Feature | Augment | GitHub Copilot | Cursor | Continue |
|---------|---------|----------------|--------|----------|
| Full codebase context | ✅ | ❌ | ✅ | 🔄 |
| Offline mode | ✅ | ❌ | ❌ | ✅ |
| Self-hosted option | ✅ | ❌ | ❌ | ✅ |
| Custom models | ✅ | ❌ | ✅ | ✅ |
| Enterprise features | ✅ | ✅ | 🔄 | ❌ |

## 🏢 Enterprise Features

- **On-premise deployment**: Keep code in your infrastructure
- **SSO/SAML**: Enterprise authentication
- **Audit logs**: Track all AI interactions
- **Custom training**: Fine-tune on your codebase (optional)
- **Team sharing**: Share indexed knowledge

## 💡 Use Cases

### Code Exploration
```
You: "What design patterns does this codebase use?"

Augment: "Based on my analysis:
1. Repository Pattern - Used in src/repositories/
2. Factory Pattern - UserFactory, OrderFactory
3. Observer Pattern - Event handling in src/events/
4. Dependency Injection - Throughout controllers
..."
```

### Bug Investigation
```
You: "Users report login failures after the Feb update"

Augment: "Looking at changes between v2.3 and v2.4:
1. auth/session.ts line 45: Session timeout changed
2. middleware/auth.ts: New token validation added
3. The most likely cause is..."
```

### Documentation
```
You: "Generate API documentation for the UserService"

Augment: [Generates comprehensive docs including all methods,
parameters, return types, example usage, and related services]
```

## ⚙️ Configuration

```json
// .augment/config.json
{
  "indexing": {
    "include": ["src/**/*", "lib/**/*"],
    "exclude": ["node_modules", "dist", "*.min.js"],
    "languages": ["typescript", "python", "go"]
  },
  "context": {
    "maxTokens": 100000,
    "includeTests": true,
    "includeDocumentation": true
  },
  "privacy": {
    "excludePatterns": ["**/secrets/**", "**/*.env"]
  }
}
```

## 🔒 Privacy & Security

- **Code stays local**: Indexing happens on your machine
- **Configurable exclusions**: Keep sensitive code out
- **Enterprise air-gap**: Fully offline deployment available
- **SOC 2 compliant**: Enterprise security standards

## 💰 Pricing

| Tier | Price | Features |
|------|-------|----------|
| Individual | $20/mo | Full features, personal use |
| Team | $40/seat/mo | Collaboration, admin controls |
| Enterprise | Custom | On-prem, SSO, audit, SLA |

## 🔗 Resources

- **Documentation**: [docs.augmentcode.com](https://docs.augmentcode.com)
- **GitHub**: [github.com/augmentcode](https://github.com/augmentcode)
- **Discord**: [discord.gg/augment](https://discord.gg/augment)
- **Blog**: [augmentcode.com/blog](https://augmentcode.com/blog)

## 📚 Related Tools

- [Cursor](cursor.md) - AI-first IDE
- [Continue](continue.md) - Open-source alternative
- [Aider](aider.md) - Terminal-based coding

---

*Added: February 2026*
