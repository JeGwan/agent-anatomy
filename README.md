# Agent Anatomy

**Interactive visualization of how LLM agents actually work — the real API message flow between User, Agent, and LLM.**

[![Live Demo](https://img.shields.io/badge/Live%20Demo-jegwan.github.io-818cf8?style=for-the-badge)](https://jegwan.github.io/agent-anatomy/)

<p align="center">
  <img src="https://img.shields.io/badge/Agent_Engineering-=%20Context_Engineering-818cf8?style=flat-square&labelColor=08080d" alt="Agent Engineering = Context Engineering" />
</p>

## The Thesis

> **Agents aren't magic. They just assemble context.**
>
> `system_prompt + tools[] + messages[]` → LLM API → if `tool_use`: execute → append `tool_result` → repeat.
>
> That loop is the entire "intelligence" of an LLM agent.

## What You'll See

An interactive sequence diagram showing **6 turns** of a real agent session with actual JSON payloads:

| Turn | What happens | Key insight |
|------|-------------|-------------|
| 1 | User request → Agent assembles context → LLM API call | `system` + `tools[]` + `messages[]` — that's all the LLM receives |
| 2 | Tool result fed back → LLM decides next action | `tool_result` goes in as `"role": "user"` — there is no "tool" role |
| 3 | Test failure → self-correction | Error logs in context → LLM can reason about failures |
| 4 | MCP tool call | MCP tools are just mixed into `tools[]` — the LLM doesn't know MCP exists |
| 5 | Skill invocation | A skill is just a prompt template injected into the user message |
| 6 | Loop termination | No `tool_use` in response = agent stops the loop |

## Three Nodes, One AI

```
┌──────────┐     ┌──────────┐     ┌──────────┐
│   User   │ ──→ │  Agent   │ ──→ │   LLM    │
│  (human) │ ←── │ (program)│ ←── │(only AI) │
└──────────┘     └──────────┘     └──────────┘
```

- **User** — a human giving instructions
- **Agent** — a program (deterministic code) that assembles context and executes tools
- **LLM** — the only AI in the system; it reads context and outputs the next action

## Features

- **Step-by-step or auto-play** — walk through each turn or watch the full sequence
- **Real JSON payloads** — see actual API message structure with syntax highlighting
- **5 languages** — 한국어 · English · 中文 · 日本語 · Español
- **Key insights** — each step explains what's really happening and why

## Why I Built This

"Agent engineering" sounds complex, but once you see the actual messages flowing between components, it clicks: **the Agent is just a loop, and the LLM is just reading context.**

I couldn't find a visual that showed the real API payloads at each step, so I made one.

## How Real Agents Work (TL;DR)

```
while (true) {
  // 1. Assemble context
  const request = {
    system: "You are Claude Code...",      // persona + rules + skill list
    tools: [...builtinTools, ...mcpTools],  // all tools mixed together
    messages: conversationHistory            // grows every turn
  };

  // 2. Call LLM
  const response = await llm.call(request);

  // 3. Check response
  if (response.hasToolUse()) {
    const result = await executeTools(response.toolUse);
    conversationHistory.push(response);      // assistant message
    conversationHistory.push(asUser(result)); // tool_result as "user" role
  } else {
    showToUser(response.text);               // no tool_use = done
    break;
  }
}
```

## Tech Stack

Single HTML file. No build step. No dependencies.

- Vanilla JS + CSS
- Token-based JSON syntax highlighter
- CSS animations for sequence diagram flow
- SVG favicon

## License

MIT

---

<p align="center">
  <b>Agent Engineering = Context Engineering</b><br>
  <sub>Built by <a href="https://github.com/JeGwan">@JeGwan</a></sub>
</p>
