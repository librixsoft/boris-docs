# Configuration

Boris uses a single JSON configuration file located at `~/.boris/settings.json`. On first launch, Boris automatically creates this file with sensible defaults if it doesn't already exist.

## Default Configuration

```json
{
  "model": {
    "baseUrl": "http://localhost:8080",
    "name": "granite4.2:8b",
    "options": {
      "think": "high"
    }
  },
  "env": {
    "OLLAMA_API_KEY": "ollama"
  },
  "maxHistorySize": 20,
  "enableHistory": true,
  "enforceSequentialExecution": true,
  "temperature": 0.7,
  "contextWindow": 10000,
  "thinkingEnabled": true,
  "thinkingMode": "think"
}
```

## Configuration Reference

### `model` — LLM Model Configuration

| Field | Type | Default | Description |
|---|---|---|---|
| `model.baseUrl` | string | `http://localhost:8080` | Endpoint URL for the Ollama or OpenAI-compatible local model server. Boris uses the `/v1/chat/completions` path internally. |
| `model.name` | string | `granite4.2:8b` | The model identifier (e.g., `granite4.2:8b`, `qwen3.6-35b-64k`, `deepseek-r1`, `llama3.1:8b`). Must match a model available on your server. |
| `model.options` | object | `{ "think": "high" }` | Model-specific options. For Ollama models with thinking capabilities, use `"think"` with values `"high"`, `"medium"`, `"low"`, or `"none"`. |
| `model.reasoningEffort` | string | (derived from `options.think`) | Alternative field for reasoning effort. Boris maps this to Spring AI's `OpenAiChatOptions.reasoningEffort`. |

### `env` — Environment Variables

| Field | Type | Default | Description |
|---|---|---|---|
| `env` | map | `{ "OLLAMA_API_KEY": "ollama" }` | Key-value pairs passed as environment variables to the model server. Add your own keys here (e.g., API keys for local proxies). |

### Chat and Context Settings

| Field | Type | Default | Description |
|---|---|---|---|
| `maxHistorySize` | integer | `20` | Maximum number of conversation turns (user + assistant messages) preserved in memory. Boris trims older messages when the limit is reached. |
| `enableHistory` | boolean | `true` | Whether to maintain conversation history. Set to `false` to start fresh with every message. |
| `enforceSequentialExecution` | boolean | `true` | Forces sequential (one-at-a-time) tool execution by the agent. |
| `temperature` | number | `0.7` | Sampling temperature for the model (0.0 - 1.0). Lower values produce more deterministic outputs. |
| `contextWindow` | integer | `10000` | Token limit for the context window, used for token counting in the TUI. |

### Reasoning (Thinking) Settings

| Field | Type | Default | Description |
|---|---|---|---|
| `thinkingEnabled` | boolean | `true` | Toggles extraction and display of `thinking` reasoning traces. When enabled, Boris shows the model's internal reasoning before the final answer. |
| `thinkingMode` | string | `"think"` | Mode of reasoning. Values: `"think"`, `"low"`, `"medium"`, `"high"`. Maps to Spring AI's `reasoningEffort` field sent to the model server. |
| `model.options.think` | string | `"high"` | Ollama-specific think parameter. Values: `"high"`, `"medium"`, `"low"`, `"none"`. Controls the depth of the model's internal reasoning. |

## How Boris Maps Think Parameters

Boris uses a specific strategy for Ollama's reasoning support:

1. The `model.options.think` value from `settings.json` is read.
2. It is mapped to Spring AI's `OpenAiChatOptions.reasoningEffort` field.
3. This field is sent as `reasoning_effort` in the `/v1/chat/completions` request to Ollama.
4. Ollama maps `reasoning_effort` to its internal think:
   - `"high"` — deep reasoning
   - `"medium"` — moderate reasoning
   - `"low"` — light reasoning
   - `"none"` — no reasoning (disables think)

**Important**: If `think` is not configured, Boris sends `reasoning_effort="none"`. If omitted entirely, Ollama auto-activates think.

## Environment Properties

Additional configuration is loaded from `src/main/resources/application.properties` (bundled in the JAR):

```properties
# Web Search Configuration
web.search.bing.url=https://www.bing.com/search
web.search.headless=true
web.search.timeout.ms=15000
web.search.default.count=5
web.search.max.count=10
web.search.wait.timeout.ms=2000
```

| Property | Default | Description |
|---|---|---|
| `web.search.bing.url` | `https://www.bing.com/search` | Bing search URL used by the `web_search` tool |
| `web.search.headless` | `true` | Run Playwright browser in headless mode |
| `web.search.timeout.ms` | `15000` | Maximum wait time for search page load (milliseconds) |
| `web.search.default.count` | `5` | Default number of search results |
| `web.search.max.count` | `10` | Maximum number of search results |
| `web.search.wait.timeout.ms` | `2000` | Extra wait time for dynamic content loading (milliseconds) |

## Agent Prompt (AGENTS.md)

Boris loads a second prompt file: `~/.boris/AGENTS.md`. This file defines the agent's personality, behavior rules, and execution constraints. It is loaded from:

1. `~/.boris/AGENTS.md` (user's custom file)
2. `src/main/resources/prompts/init/AGENTS.md` (bundled default)

The system prompt is a concatenation of `default-system-prompt.md` + `AGENTS.md`.

You can customize the agent's behavior by editing `~/.boris/AGENTS.md`. Changes take effect on the next message sent to the model (the system prompt is included with every request).

## Model Compatibility

Boris works with any OpenAI-compatible local model server. Tested configurations include:

| Server | Endpoint | Compatible Models |
|---|---|---|
| Ollama | `http://localhost:11434` | granite4.2, qwen3.6, deepseek-r1, llama3.1, mistral, mixtral, etc. |
| llama.cpp server | `http://localhost:8080` | any GGUF model served via OpenAI-compatible API |
| vLLM / TGI | custom | any model with OpenAI-compatible endpoint |

To configure a different server, update `model.baseUrl` in `settings.json` to point to your server's endpoint.
