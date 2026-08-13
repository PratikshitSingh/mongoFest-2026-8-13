# Google ADK (Agent Development Kit)

## 2 Components of ADK
1. **SDK and CLI component**
2. **ADK Web** — chat application using a local web-based interface

---

## Overview
Open-source, **code-first** framework from Google for building, evaluating, and deploying AI agents — from single agents to complex multi-agent systems. Announced at Google Cloud NEXT 2025.

- **Model-agnostic** (optimized for Gemini; also Claude, OpenAI, Ollama, vLLM, LiteLLM via the `BaseLlm` interface)
- **Deployment-agnostic** (local, Cloud Run, GKE, Vertex AI / Agent Platform)
- Multi-language: Python, TypeScript/JS, Go, Java, Kotlin
- Philosophy: make agent building *feel like software development*

## Core Concepts (Primitives)
- **Agent** — worker unit for a task:
	- `LlmAgent` — LLM reasoning (non-deterministic)
	- **Workflow agents** — deterministic flow: `SequentialAgent`, `ParallelAgent`, `LoopAgent`
	- Custom via `BaseAgent`
- **Tool** — abilities beyond chat: call APIs, search, run code, query DBs, call other agents
- **Callbacks** — hooks at lifecycle points (logging, checks, behavior mods)
- **Session & State** — one conversation. `Session` = history (`Events`); `State` = short-term working memory
- **Memory** — long-term recall *across* sessions (distinct from `State`)
- **Artifacts** — save/load/version files & binary data (`ArtifactService`)
- **Code Execution** — agents generate & run code
- **Planning** — break goals into steps (e.g. ReAct planner)
- **Event** — basic unit of comms (msg, reply, tool call); forms history
- **Runner** — engine orchestrating execution based on Events

## Key Capabilities
1. Multi-agent design — hierarchical; delegate via LLM transfer or `AgentTool`
2. Rich tools — `FunctionTool`, `AgentTool`, built-ins, MCP tools, OpenAPI tools, long-running/async
3. Flexible orchestration — predictable pipelines + LLM-driven dynamic routing
4. Integrated dev tooling — CLI + Developer/Web UI to run, inspect, debug, visualize
5. Native streaming — bidirectional text/audio (Gemini Live API Toolkit)
6. Built-in evaluation — multi-turn datasets, run via CLI or dev UI
7. Broad LLM support — `BaseLlm`
8. Artifact management — versioned files/binaries
9. Extensibility — 3rd-party tools/connectors; **A2A** + **MCP** (expose agent via `to_mcp_server`)
10. State & memory — short-term via `SessionService`; pluggable long-term `Memory`

## Ways to Run / Interact
Same `agent.py` logic regardless of invocation:
- **CLI** — command line
- **Web UI / Dev UI (ADK Web)** — local browser interface to run + debug; also Visual Builder
- **API Server** — serve over HTTP
- **API** (Python etc.) — call from code

## Deployment
Local (dev) · **Cloud Run** · **GKE** · **Vertex AI / Agent Platform** (managed runtime)

## Ecosystem Extras
- **A2A** — agent-to-agent interop · **MCP** — consume/expose tools
- **Grounding** (Google Search) · **Skills** (reusable capabilities, local + cloud registries)
- **Observability** — logging, metrics, traces
- **Context mgmt** — treats context like source code: filters events, summarizes old turns, lazy-loads artifacts, tracks tokens; supports compaction & caching

## Licensing
- Content: **CC BY 4.0** · Code: **Apache 2.0**
- GitHub: `google/adk-python`, `adk-js`, `adk-go`, `adk-java`, `adk-kotlin`

## Links
- https://adk.dev · https://google.github.io/adk-docs/ · https://adk.dev/get-started/about/

---
*Sourced from official ADK docs (adk.dev), Aug 2026.*