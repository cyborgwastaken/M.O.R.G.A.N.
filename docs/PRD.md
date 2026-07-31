# MORGAN — Product Requirements Document

## Overview

**MORGAN** — *Multi-domain Operational Response and Guidance Artificial Network*.

A personal, local-first AI assistant with a web-based chat UI. MORGAN is an **agentic** assistant: instead of a fixed set of hand-coded commands, it runs on an LLM tool-calling loop that decides for itself when to answer directly versus when to invoke a tool (run a shell command, touch the filesystem, search the web, or recall/save memory).

## Problem

There's no single local assistant that can hold a conversation, act on the machine it runs on (open apps, manage files), remember things across sessions, and pull in fresh information from the web — without juggling several separate tools.

## Goals (v1)

- Conversational Q&A through a chat UI.
- Agentic tool use: the assistant can run shell commands, open applications, and perform file/folder operations when asked.
- Persistent memory: conversation history and learned facts survive restarts.
- Web search: the assistant can look things up and summarize.
- Support two LLM providers behind one interface: **Gemini API** (primary) and **Ollama** (local/offline).

## Non-goals (v1)

- No mobile app.
- No multi-user support — single local user only.
- No voice input/output.
- No cloud deployment/hosting — runs locally.

## Users

Just the developer (single-user, local-first tool).

## Core Features

1. **Conversational Q&A** — general questions answered directly by the LLM (Gemini primary, Ollama as a local/offline alternative), no tool call needed.
2. **Command execution** — open applications and run shell commands on request, with a confirmation step before anything destructive.
3. **File/folder operations** — create, move, search, and read files/folders on request.
4. **Persistent memory** — conversation history and learned facts stored locally, available across sessions.
5. **Web search & summarization** — the agent can search the web and summarize findings when it doesn't know something or needs current information.

## Interface

A local web app: a React chat UI served by a Next.js app, running on localhost.

## Constraints & Risks

- **API key handling**: Gemini API key must be kept out of source control (env config).
- **Shell command safety**: arbitrary command execution is powerful and risky — destructive actions need explicit confirmation in the UI before running.
- **Data privacy**: memory/conversation history is stored locally only, not sent anywhere except to the chosen LLM provider as needed for context.
- **Provider parity**: Gemini and Ollama need to work behind the same tool-calling interface, but capability/quality will differ (Ollama is local and likely less capable).

## Success Criteria (v1)

- Can hold a multi-turn conversation and answer general questions.
- Can be asked to open an app or run a shell command, confirm, and have it execute.
- Can be asked to create/move/find a file and do it correctly.
- Remembers a fact told to it in one session and recalls it in a later session.
- Can answer a question that requires a live web search, with a summarized result.
- Works with Gemini configured; can be switched to Ollama via config without code changes.
