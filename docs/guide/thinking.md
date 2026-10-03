# Reasoning (Thinking) System

Boris supports native reasoning trace extraction for local models that support `thinking` capabilities (e.g., Granite 4.2, Qwen, DeepSeek). The system intercepts reasoning fields sent by the model server and displays them in the chat before the final response.

---

## How It Works

### Architecture

```
User Message
    │
    ▼
ChatService.sendMessageStream()
    │
    ├── resolveThinkMode() → determines reasoning effort level
    ├── ThinkContextHolder.setThinkMode(level)
    │
    ▼
Spring AI ChatClient
    │
    ├── buildSpringAiOptions(effectiveThink)
    │       └── reasoningEffort = level  (sent as "reasoning_effort")
    │
    ▼
Ollama / Model Server
    │
    ├── /v1/chat/completions
    │       └── { model, reasoning_effort, messages, tools }
    │
    ▼
Response with thinking + content fields
    │
    ├── ThinkContextHolder.getLastThinking()  ← intercepts via HTTP interceptor
    └── response.content                         ← main text
```

### Request Flow

1. Boris sends the user message + conversation history to the model server via Spring AI's `ChatClient`.
2. The `reasoning_effort` parameter is set in the request options based on the configured think level.
3. The model server (Ollama) processes the request and returns two fields:
   - `thinking` — the model's internal reasoning trace
   - `content` — the final response
4. Boris intercepts these fields via `ThinkClientHttpInterceptor` and `ThinkContextHolder`.
5. If the model doesn't use the standard thinking fields, Boris falls back to regex extraction from the response content using the pattern:
   ```
   (?:\b|<)thinking|<thought>|<reasoning>|```(.*)?(?:\b|<\/)(?:thinking|thought|reasoning|code)
   ```
6. The reasoning trace is displayed in the chat as:
   ```
   <thinking>
   [model's reasoning here]
   </thinking>
   ```
7. The final response follows after the thinking block.

---

## Configuration

### Settings Parameters

| Setting | Values | Description |
|---|---|---|
| `thinkingEnabled` | `true` / `false` | Master switch for reasoning display |
| `thinkingMode` | `"think"`, `"low"`, `"medium"`, `"high"` | Reasoning mode passed to the model |
| `model.options.think` | `"high"`, `"medium"`, `"low"`, `"none"` | Ollama-specific think parameter |

### Mapping to Spring AI

Boris maps the settings to Spring AI's `OpenAiChatOptions.reasoningEffort`:

| `settings.json` value | Spring AI `reasoningEffort` | Ollama behavior |
|---|---|---|
| `"high"` | `"high"` | Deep reasoning trace |
| `"medium"` | `"medium"` | Moderate reasoning |
| `"low"` | `"low"` | Light reasoning |
| `"none"` | `"none"` | No reasoning (faster) |
| (not set) | (omitted) | Ollama auto-activates think |

---

## In-App Control

You can toggle thinking on/off directly in the chat:

| Command | Description |
|---|---|
| `/thinking` | Toggle reasoning on/off (state change) |
| `/thinking on` | Enable reasoning |
| `/thinking off` | Disable reasoning |
| `/think` | Alias for `/thinking` |
| `/think on` | Alias for `/thinking on` |
| `/think off` | Alias for `/thinking off` |
| `/reasoning` | Alias for `/thinking` |
| `/reasoning on` | Alias for `/thinking on` |
| `/reasoning off` | Alias for `/thinking off` |

The system prompt confirms the change with a message:
- `● Razonamiento (thinking) activado`
- `● Razonamiento (thinking) desactivado`

---

## Model Requirements

For Boris's reasoning system to work, your model must support:

1. **OpenAI-compatible `/v1/chat/completions` endpoint** — Boris uses this path.
2. **`reasoning_effort` field support** — The model server must accept this field in the request.
3. **`thinking` field in response** — The model server must return reasoning traces in a separate field (or Boris will fall back to regex extraction from the content).

### Tested Models

| Model | Server | Think Support |
|---|---|---|
| granite4.2:8b | Ollama | Native (`reasoning_effort`) |
| qwen3.6-35b-64k | Ollama | Native (`reasoning_effort`) |
| deepseek-r1 | Ollama | Via content regex (`<think> ... </think>`) |
| llama3.1:8b | Ollama | No think (standard responses) |

### Ollama Configuration

For models with native reasoning (Granite, Qwen), ensure your Ollama server is running:

```bash
ollama serve
```

Pull a model with think support:

```bash
ollama pull granite4.2:8b
```

Then configure `settings.json`:

```json
{
  "model": {
    "baseUrl": "http://localhost:11434",
    "name": "granite4.2:8b",
    "options": {
      "think": "high"
    }
  },
  "thinkingEnabled": true,
  "thinkingMode": "think"
}
```

---

## Technical Details

### ThinkClientHttpInterceptor

Intercepts HTTP requests/responses to add custom headers (API key) and capture thinking-related fields from Ollama's response.

### ThinkContextHolder

Thread-local storage for thinking data. Each request sets the think mode, and the interceptor stores the reasoning trace. The ChatService retrieves it after each response.

### ThinkMode Resolution

Boris resolves the effective think mode as follows:

1. Check `settings.options.think` — if present and non-blank, use it.
2. Check `settings.model.reasoningEffort` — if present and non-blank, use it.
3. If `thinkingEnabled` is true and no mode is found, default to `low`.
4. If `thinkingEnabled` is false, set `reasoningEffort="none"`.
