# Running llama.cpp Locally

The easiest way to run llama.cpp from Node.js is the [`node-llama-cpp`](https://node-llama-cpp.withcat.ai) package. It ships pre-built binaries (macOS, Linux, Windows), auto-detects your GPU (Metal/CUDA/Vulkan needs **zero configuration**), and can download the model for you.

## 1. Install

```bash
npm install node-llama-cpp
```

## 2. Load a Model and Chat

This single script downloads the model from Hugging Face automatically (first run only), then loads it and chats:

```javascript
// index.mjs
import { getLlama, LlamaChatSession, resolveModelFile } from "node-llama-cpp";

const llama = await getLlama();

// Downloads the GGUF model (~1.5 GB) to ./models on first run
const modelPath = await resolveModelFile(
  "hf:Qwen/Qwen2.5-1.5B-Instruct-GGUF/qwen2.5-1.5b-instruct-q4_k_m.gguf",
  "./models"
);

const model = await llama.loadModel({ modelPath });
const context = await model.createContext();
const session = new LlamaChatSession({ contextSequence: context.getSequence() });

const answer = await session.prompt("Hi! What are you?");
console.log("AI:", answer);
```

```bash
node index.mjs
```

That's it — no Python, no cmake, no API keys.

> **Tip:** for a bigger model with better answers, swap the URL for a 7B/8B model, e.g. `qwen2.5-7b-instruct-q4_k_m.gguf` (~4.7 GB). Rule of thumb: the model file should fit in your RAM (or VRAM).

## 3. Try It Without Writing Code

Chat in the terminal with a single command — it shows a list of recommended models and downloads one for you:

```bash
npx -y node-llama-cpp chat
```

## 4. Using Your Own .gguf File

Already downloaded a model? Just point `modelPath` at the file:

```javascript
const model = await llama.loadModel({
  modelPath: "./Meta-Llama-3.1-8B-Instruct.Q4_K_M.gguf"
});
```

Search for GGUF models on [Hugging Face](https://huggingface.co/models?library=gguf) — pick ones with `Instruct` (or `it`) in the name so they can chat.

## Running on a Server (LAN or Cloud)

To share the model with other machines or apps, run llama.cpp's built-in HTTP server (`llama-server`). It exposes an **OpenAI-compatible API** that any client can call.

**Install (pick one):**

```bash
# macOS
brew install llama.cpp

# Linux — download a prebuilt binary, no compilation needed
# See: https://github.com/ggml-org/llama.cpp/releases
```

**Start the server:**

```bash
llama-server -m /path/to/model.gguf --host 0.0.0.0 --port 8080 -ngl 99
```

| Flag | Meaning |
|---|---|
| `-m model.gguf` | Model file to load |
| `--host 0.0.0.0` | Listen on all interfaces (`127.0.0.1` = local only) |
| `--port 8080` | HTTP port |
| `-ngl 99` | Offload all layers to GPU (omit on CPU-only servers) |

**Call it from Node.js** (or any OpenAI client) with the `openai` package:

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  baseURL: "http://<server-ip>:8080/v1",
  apiKey: "not-needed",
});

const res = await client.chat.completions.create({
  model: "local",
  messages: [{ role: "user", content: "Hello!" }],
});
console.log(res.choices[0].message.content);
```

**Security note for cloud servers:** start the server with `--api-key mysecret` and use that value as `apiKey` in clients. Behind a public IP, also put it behind nginx + TLS.

## Connecting Boris AI

Boris works with any OpenAI-compatible endpoint. Point its provider at your llama.cpp server:

```json
{
  "providers": [
    {
      "name": "llama.cpp",
      "baseUrl": "http://localhost:8080/v1",
      "apiKey": "not-needed"
    }
  ]
}
```

## Troubleshooting

- **Out of memory** → use a smaller model or a Q4_K_M quant; drop `-ngl` on machines without GPU.
- **Slow generation** → CPU inference is slow for large models; use a smaller model or a GPU build.
- **`require` fails / ERR_UNKNOWN_FILE_EXTENSION** → `node-llama-cpp` is ESM-only; add `"type": "module"` to your `package.json`.
