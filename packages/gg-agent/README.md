# @kenkaiiii/gg-agent

<p align="center">
  <strong>Agent loop with multi-turn tool execution. Build agents that think, act, and loop.</strong>
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/@kenkaiiii/gg-agent"><img src="https://img.shields.io/npm/v/@kenkaiiii/gg-agent?style=for-the-badge" alt="npm version"></a>
  <a href="../../LICENSE"><img src="https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge" alt="MIT License"></a>
</p>

Give an LLM tools. It calls them. Results go back in. It loops until it's done. That's it.

Built on top of [`@kenkaiiii/gg-ai`](../gg-ai/README.md). Part of the [GG Framework](../../README.md) monorepo.

---

## Install

```bash
npm i @kenkaiiii/gg-agent
```

---

## Usage

### Basic agent

```typescript
import { Agent } from "@kenkaiiii/gg-agent";
import { z } from "zod";

const agent = new Agent({
  provider: "anthropic",
  model: "claude-sonnet-4-6",
  apiKey: "sk-...",
  system: "You are a helpful assistant.",
  tools: [{
    name: "get_weather",
    description: "Get the weather for a city",
    parameters: z.object({ city: z.string() }),
    async execute({ city }) {
      return `72°F and sunny in ${city}`;
    },
  }],
});

for await (const event of agent.prompt("What's the weather in Tokyo?")) {
  switch (event.type) {
    case "text_delta":
      process.stdout.write(event.text);
      break;
    case "tool_call_start":
      console.log(`\nCalling ${event.name}...`);
      break;
    case "tool_call_end":
      console.log(`Done (${event.durationMs}ms)`);
      break;
    case "agent_done":
      console.log(`\nFinished in ${event.totalTurns} turns`);
      break;
  }
}
```

### Await the result

```typescript
const result = await agent.prompt("What's the weather in Tokyo?");

console.log(result.message);    // AssistantMessage (final response)
console.log(result.totalTurns); // number of LLM calls
console.log(result.totalUsage); // aggregated token usage
```

Same `agent.prompt()` call. `for await` gives you events. `await` gives you the final result.

### Multi-turn conversations

The `Agent` class maintains conversation history. Each `prompt()` call continues the conversation.

```typescript
await agent.prompt("What's the weather in Tokyo?");
await agent.prompt("How about New York?");
await agent.prompt("Which one is warmer?");
// Agent remembers all previous turns
```

### Using agentLoop directly

For full control, use the pure async generator directly.

```typescript
import { agentLoop } from "@kenkaiiii/gg-agent";

const messages = [
  { role: "system" as const, content: "You are helpful." },
  { role: "user" as const, content: "Hello!" },
];

const loop = agentLoop(messages, {
  provider: "openai",
  model: "gpt-4.1",
  apiKey: "sk-...",
  tools: [/* ... */],
});

for await (const event of loop) {
  // Same events as agent.prompt()
}
// messages array is mutated with the full conversation
```

---

## Defining tools

Tools use Zod schemas for parameters. The `execute` function receives typed args.

```typescript
import { z } from "zod";
import type { AgentTool } from "@kenkaiiii/gg-agent";

const readFile: AgentTool<typeof fileParams> = {
  name: "read_file",
  description: "Read the contents of a file",
  parameters: fileParams,
  async execute({ path }, context) {
    // context.signal — AbortSignal
    // context.toolCallId — unique ID for this call
    // context.onUpdate — send progress updates
    return fs.readFileSync(path, "utf-8");
  },
};

const fileParams = z.object({
  path: z.string().describe("Absolute file path"),
});
```

Return a string, or a `{ content, details }` object for structured results. If `execute` throws, the error becomes a tool result (not a crash). The agent sees the error and can retry or adjust.

---

## Events

| Event | Description |
|---|---|
| `text_delta` | Incremental text output |
| `thinking_delta` | Extended thinking output |
| `tool_call_start` | Tool invocation started (name, args) |
| `tool_call_update` | Progress update from a running tool |
| `tool_call_end` | Tool finished (result, duration, isError) |
| `server_tool_call` | Server-side tool invocation |
| `server_tool_result` | Server-side tool result |
| `turn_end` | One LLM call completed (stop reason, usage) |
| `agent_done` | All turns finished (total turns, total usage) |
| `error` | Fatal error |

---

## Options

```typescript
interface AgentOptions {
  provider: "anthropic" | "openai" | "glm" | "moonshot";
  model: string;
  system?: string;
  tools?: AgentTool[];
  serverTools?: ServerToolDefinition[];
  maxTurns?: number;       // default: 40
  maxTokens?: number;
  temperature?: number;
  thinking?: "low" | "medium" | "high" | "max";
  apiKey?: string;
  baseUrl?: string;
  signal?: AbortSignal;
  cacheRetention?: "none" | "short" | "long";
  compaction?: boolean;    // Anthropic only
  maxContinuations?: number; // default: 5
  transformContext?: (messages: Message[]) => Message[] | Promise<Message[]>;
}
```

`transformContext` is called before each LLM call. Use it for compaction, truncation, or injecting dynamic context.

---

## License

MIT
