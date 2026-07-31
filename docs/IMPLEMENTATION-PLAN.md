# MORGAN — Implementation Plan

## Architecture

A single **Next.js** app (App Router) provides both the frontend and backend:

- **Frontend**: React chat UI (message list + input), talks to a server-side agent endpoint.
- **Backend**: a server route (`app/api/agent/route.ts` or a server action) that runs the **agent loop**:
  1. Send the conversation + tool schema to the active LLM provider.
  2. If the model requests a tool call, execute it server-side and feed the result back to the model.
  3. Repeat until the model returns a final text answer.
  4. Return the answer (and any tool activity, for UI display) to the frontend.

- **LLM providers** (`lib/llm/`): a common interface (`generate(messages, tools) -> { text | toolCalls }`) with two adapters:
  - `gemini.ts` — calls the Gemini API (primary).
  - `ollama.ts` — calls a local Ollama instance (offline/local fallback).
  - Active provider selected via env config, swappable without code changes.

- **Tools** (`lib/tools/`), one module each:
  - `runShellCommand` — executes a shell command; destructive-looking commands require UI confirmation before running.
  - `fileOp` — read / write / move / search files and folders.
  - `webSearch` — searches the web and returns results for the agent to summarize.
  - `memoryGet` / `memorySet` — read/write persisted facts and conversation history.

- **Memory** (`lib/memory/`): local persistence for conversation history and learned facts. Start with a JSON file for simplicity; move to SQLite (`better-sqlite3`) if querying needs grow.

- **Safety**: shell/file tools that could be destructive (delete, overwrite, `rm`, etc.) return a "needs confirmation" result instead of executing immediately; the UI surfaces this and only proceeds once the user confirms.

## Milestones

1. **Scaffold** — new Next.js app, replacing the placeholder `main.py`. Minimal chat UI (send message, see response).
2. **Agent loop v1** — wire the agent route to the Gemini API with tool-calling support, even with zero tools yet (plain Q&A working end to end).
3. **Core tools** — add `runShellCommand` and `fileOp`, including the confirmation flow in the UI for destructive actions.
4. **Memory** — add `memoryGet`/`memorySet` tools and the underlying storage layer; conversation history persists across restarts.
5. **Web search** — add the `webSearch` tool.
6. **Ollama support** — add the Ollama adapter behind the same provider interface; verify switching providers via config works without code changes.

## Key Files

- `app/` — Next.js App Router pages and the chat UI.
- `app/api/agent/route.ts` — the agent loop endpoint.
- `lib/tools/` — one file per tool (`shell.ts`, `fileOp.ts`, `webSearch.ts`, `memory.ts`).
- `lib/llm/` — provider adapters (`gemini.ts`, `ollama.ts`) behind a shared interface.
- `lib/memory/` — storage layer (JSON file to start, SQLite if needed later).
- `.env.local` — Gemini API key and other local config (git-ignored).

`main.py` is retired once the Next.js app is in place.

## Open Questions (deferred to implementation)

- Final memory storage format: JSON file vs SQLite for v1.
- Which web search API/provider to integrate.
- Exact UI treatment for destructive-action confirmations (modal, inline prompt, etc.).
- Package manager: npm vs pnpm.
