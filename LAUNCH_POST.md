---
title: "Building a Production AI Agent in .NET 10 with NativeAOT: Introducing OpenClaw.NET"
published: false
description: "Why we built a self-hosted, sovereign AI agent framework in C# instead of Python/TypeScript."
tags: dotnet, ai, csharp, nativeaot, llm
cover_image: https://raw.githubusercontent.com/user/openclaw.net/main/assets/cover.png
canonical_url: https://openclaw.net/blog/launch
---

Today, we're excited to open-source **[OpenClaw.NET](https://github.com/user/openclaw.net)** — a production-hardened, self-hosted AI agent runtime built for .NET teams who want to ship, not experiment.

Unlike the fragmented Python ecosystem or the heavy TypeScript runtimes, OpenClaw.NET is a single, self-contained binary (under 15 MB) that gives you a complete agent gateway with 14 tools, 10 LLM providers, and a full security stack.

## The Problem: "It works in my notebook"

Building an AI agent prototype is easy. Moving it to production is hard.

- Python dependencies are a nightmare to manage in enterprise environments.
- TypeScript agents are heavy (Node_modules hell) and often leak memory in long-running processes.
- Most frameworks (LangChain, Semantic Kernel) are libraries, not runtimes. You still have to build the API, auth, rate limiting, and observability yourself.

We wanted something different: **A deployable artifact.**

## Enter OpenClaw.NET

Built on the cutting edge of **.NET 10** and **NativeAOT**, OpenClaw is designed to be treated like a database or a message broker: you configure it, deploy it, and connect to it over WebSocket or HTTP.

### Features at a Glance

*   **Self-Contained**: `<15 MB` Docker image (Alpine/Ubuntu Chiseled + NativeAOT).
*   **Provider Agnostic**: Use OpenAI, Anthropic, Google, Ollama, or any local LLM via `Microsoft.Extensions.AI`.
*   **14 Built-in Tools**: Shell, File I/O, Web Search, Git, Code Execution, PDF Reading, Memory, and more.
*   **Multi-Agent**: The `delegate_agent` tool lets your main agent spawn sub-agents for specific tasks.
*   **Resilient**: Circuit breakers, exponential backoff, token budgets, and per-request timeouts.
*   **Observable**: Structured logging, Prometheus metrics, and distributed tracing out of the box.

## Why .NET 10 & NativeAOT?

We chose **C# 14** and **.NET 10** for three reasons:

1.  **Performance**: NativeAOT compilation starts instantly (no JIT warmup) and uses minimal memory.
2.  **Type Safety**: The entire tool execution pipeline is strongly typed.
3.  **Ecosystem**: `Microsoft.Extensions.AI` provides a unified abstraction for all LLM providers.

Here is what the startup looks like:

```text
[14:02:23 INF] OpenClaw Gateway v1.0.0 starting...
[14:02:23 INF] Loaded 14 tools (Shell, FileRead, FileWrite, Memory...)
[14:02:23 INF] Connected to OpenAI (gpt-4o)
[14:02:23 INF] Listening on ws://0.0.0.0:18789
```

And it stays under **50MB of RAM** even under load.

## The Architecture

OpenClaw isn't just a loop. It's a pipeline.

1.  **Gateway**: Handles WebSocket/HTTP connections and authentication.
2.  **Middleware**: Rate limiting, token budgeting, and context management.
3.  **Agent Runtime**: The core loop (Think → Act → Observe).
4.  **Tool Sandbox**: Executes tools with strict permission checks (path traversal protection, command allowlists).
5.  **LLM Backend**: Abstracts the provider complexity.

**Conversation Branching**:
A unique feature of OpenClaw is branching. You can "fork" a conversation at any point to explore a different path without losing the original context.

```csharp
// Fork the session
var branchId = await sessionManager.BranchAsync(sessionId, "exploring-sql-solution");

// Switch to branch
await sessionManager.RestoreBranchAsync(sessionId, branchId);
```

**Delgation**:
The main agent can delegate work to specialized sub-agents.
*"I need to research this topic. Spawning a research specialist..."*

## Getting Started

You can run OpenClaw in seconds using Docker:

```bash
docker run -d -p 18789:18789 \
  -e OPENCLAW_API_KEY="sk-..." \
  ghcr.io/user/openclaw-gateway:latest
```

Or build it from source:

```bash
git clone https://github.com/user/openclaw.net
dotnet run --project src/OpenClaw.Gateway -c Release
```

## Join the Community

We are building OpenClaw in the open.

*   **GitHub**: [github.com/user/openclaw.net](https://github.com/user/openclaw.net)
*   **Discord**: [Join the server](https://discord.gg/openclaw)
*   **Docs**: [docs.openclaw.net](https://docs.openclaw.net)

If you're a .NET developer tired of Python envy, give OpenClaw a try. Let's build agents that are robust, fast, and ready for production.

Happy coding! 🚀
