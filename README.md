# Project M.O.R.G.A.N.

**Multi-domain Operational Response and Guidance Artificial Network**

A personal, local-first AI assistant with a web-based chat UI.

MORGAN is *agentic*: rather than matching your input against a fixed list of hand-coded commands, it runs an LLM tool-calling loop that decides for itself when to answer directly and when to reach for a tool — running a shell command, touching the filesystem, searching the web, or recalling something you told it last week.

> **Status: pre-implementation.** The spec is written; the app hasn't been scaffolded yet. See [PROGRESS.md](docs/PROGRESS.md) for where things stand.

## What it will do (v1)

- **Answer questions** — ordinary multi-turn conversation, no tool call needed.
- **Run local automations** — open applications and run shell commands, with a confirmation step before anything destructive.
- **Work with files** — create, move, search, and read files and folders.
- **Remember** — conversation history and learned facts persist across sessions.
- **Search the web** — look things up and summarize when it needs current information.

Explicitly out of scope for v1: mobile, multi-user, voice, cloud hosting.

## How it works

A single **Next.js** app serves both the React chat UI and the server-side agent.

```
React chat UI  →  /api/agent  →  agent loop  ⇄  LLM provider (Gemini | Ollama)
                                     ↓
                                   tools
                    shell · files · web search · memory
```

The agent loop sends your conversation plus a tool schema to the active LLM. If the model asks for a tool, the server executes it, feeds the result back, and loops — until the model returns a final answer.

Two LLM providers sit behind one interface, selected by config: the **Gemini API** (primary) and **Ollama** (local/offline).

## Docs

| Doc | What's in it |
| --- | --- |
| [PRD.md](docs/PRD.md) | Goals, non-goals, features, constraints, success criteria |
| [IMPLEMENTATION-PLAN.md](docs/IMPLEMENTATION-PLAN.md) | Architecture, milestones, file layout, open questions |
| [PROGRESS.md](docs/PROGRESS.md) | Current status and running log |

## Roadmap

1. Scaffold the Next.js app + minimal chat UI
2. Agent loop wired to the Gemini API
3. Shell and file tools, with confirmation UX
4. Persistent memory
5. Web search
6. Ollama provider support

## A note on safety

MORGAN executes shell commands on the machine it runs on. Anything destructive goes through an explicit confirmation in the UI before it runs, and all memory stays local — it leaves the machine only as context sent to whichever LLM provider you've configured.
