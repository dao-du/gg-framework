# @kenkaiiii/gg-ai

<p align="center">
  <strong>Unified LLM streaming API. Four providers. One interface.</strong>
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/@kenkaiiii/gg-ai"><img src="https://img.shields.io/npm/v/@kenkaiiii/gg-ai?style=for-the-badge" alt="npm version"></a>
  <a href="../../LICENSE"><img src="https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge" alt="MIT License"></a>
</p>

One function. Flat options. Switch providers by changing a string. No adapters, no plugins, no wrapper classes.

Part of the [GG Framework](../../README.md) monorepo.

---

## Install

```bash
npm i @kenkaiiii/gg-ai
```

---

## Usage

### Stream events

```typescript
import { stream } from "@kenkaiiii/gg-ai";

for await (const event of stream({
  provider: "anthropic",
  model: "claude-sonnet-4-6",
  apiKey: "sk-...",
  messages: [{ role: "user", content: "Explain quicksort in one paragraph." }],
})) {
  if (event.type === "text_delta") process.stdout.write(event.text);
}
```

### Await the response

```typescript
const response = await stream({
  provider: "openai",
  model: "gpt-4.1",
  apiKey: "sk-...",
  messages: [{ role: "user", content: "Hello!" }],
});

console.log(response.message);    // AssistantMessage
console.log(response.stopReason); // "end_turn"
console.log(response.usage);      // { inputTokens, outputTokens, ... }
```

Same `stream()` call. `for await` gives you events. `await` gives you the final response. Your choice.

### Tools

```typescript
import { z } from "zod";

const response = await stream({
  provider: "anthropic",
  model: "claude-sonnet-4-6",
  apiKey: "sk-...",
  messages: [{ role: "user", content: "What's the weather in Tokyo?" }],
  tools: [{
    name: "get_weather",
    description: "Get weather for a city",
    parameters: z.object({ city: z.string() }),
  }],
});

// response.stopReason === "tool_use"
// response.message.content includes the ToolCall
```

Tool parameters are Zod schemas. Converted to JSON Schema at the provider boundary automatically.

### Extended thinking

```typescript
for await (const event of stream({
  provider: "anthropic",
  model: "claude-opus-4-6",
  apiKey: "sk-...",
  thinking: "high",
  messages: [{ role: "user", content: "Solve this step by step..." }],
})) {
  if (event.type === "thinking_delta") process.stderr.write(event.text);
  if (event.type === "text_delta") process.stdout.write(event.text);
}
```

---

## Providers

| Provider | Models | Notes |
|---|---|---|
| `anthropic` | Claude Opus 4.6, Sonnet 4.6, Haiku 4.5 | Extended thinking, prompt caching, server-side compaction |
| `openai` | GPT-4.1, o3, o4-mini | Supports OAuth (codex endpoint) and API keys |
| `glm` | GLM-5, GLM-4.7 | Z.AI platform, OpenAI-compatible |
| `moonshot` | Kimi K2.5 | Moonshot platform, OpenAI-compatible |

---

## Stream events

| Event | Description |
|---|---|
| `text_delta` | Incremental text output |
| `thinking_delta` | Extended thinking output (Anthropic) |
| `toolcall_delta` | Streaming tool call arguments |
| `toolcall_done` | Completed tool call with parsed args |
| `server_toolcall` | Server-side tool invocation |
| `server_toolresult` | Server-side tool result |
| `done` | Stream finished, includes stop reason |
| `error` | Error occurred |

---

## Options

```typescript
interface StreamOptions {
  provider: "anthropic" | "openai" | "glm" | "moonshot";
  model: string;
  messages: Message[];
  tools?: Tool[];
  toolChoice?: "auto" | "none" | "required" | { name: string };
  serverTools?: ServerToolDefinition[];
  maxTokens?: number;
  temperature?: number;
  topP?: number;
  stop?: string[];
  thinking?: "low" | "medium" | "high" | "max";
  apiKey?: string;
  baseUrl?: string;
  signal?: AbortSignal;
  cacheRetention?: "none" | "short" | "long";
  compaction?: boolean; // Anthropic only
}
```

---

## License

MIT
