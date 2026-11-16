# Running llama.cpp as a Local Inference Engine

This guide shows the **simplest** ways to run [llama.cpp](https://github.com/ggml-org/llama.cpp) with a local model — either on your own machine (driven from Node.js) or on a server (local network or cloud) — and connect to it from any client that speaks the OpenAI API, including Boris AI.

llama.cpp ships an **OpenAI-compatible HTTP server** (`llama-server`). Once it is running, anything that can call OpenAI (Node.js, curl, Boris) can use your local model with zero extra code.

---

## Prerequisites

| Requirement | Notes |
|---|---|
| A GGUF model file | Download from Hugging Face (e.g. `Qwen2.5-7B-Instruct-Q4_K_M.gguf`) |
| Compiler toolchain | Linux: `build-essential` / macOS: Xcode CLT — **not needed** if you use a prebuilt binary |
| Node.js 18+ | Only if you want to call the model from Node.js |

> **Rule of thumb for model size:** the quantized model file should fit in your RAM (or VRAM). A `Q4_K_M` 7B model is ~4.5 GB and runs comfortably on 8 GB of RAM.

---

## Option 1 — Fastest Start (no compilation)

Download a prebuilt binary and run the server. This is the easiest path and works on Linux, macOS, and Windows.

**Linux (x86_64):**

```bash
mkdir -p ~/llama && cd ~/llama

# Download the latest prebuilt CPU build
curl -L -o llama.tar.gz \
  https://github.com/ggml-org/llama.cpp/releases/latest/download/llama-b0000-bin-ubuntu-x64.zip

unzip llama.tar.gz
```

> Check the [releases page](https://github.com/ggml-org/llama.cpp/releases) for the exact file name of the latest build (CUDA, Vulkan, and macOS builds are also published). On macOS you can simply `brew install llama.cpp`.

**Download a model:**

```bash
# Example: Qwen2.5 7B instruct, 4-bit quant (~4.7 GB)
curl -L -o model.gguf \
  "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct-GGUF/resolve/main/qwen2.5-7b-instruct-q4_k_m.gguf"
```

**Start the inference server:**

```bash
./llama-server -m model.gguf --host 0.0.0.0 --port 8080 -ngl 99
```

| Flag | Meaning |
|---|---|
| `-m model.gguf` | The model file to load |
| `--host 0.0.0.0` | Listen on all interfaces (use `127.0.0.1` for local-only) |
| `--port 8080` | HTTP port |
| `-ngl 99` | Offload all layers to GPU (omit on CPU-only machines) |

You now have an OpenAI-compatible API at `http://localhost:8080/v1` and a small web UI at `http://localhost:8080`.

**Verify with curl:**

```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

---

## Option 2 — Build from Source (Linux)

If you want the latest version or GPU support tuned for your machine, build it. It takes ~2 minutes:

```bash
sudo apt update && sudo apt install -y build-essential git cmake

git clone https://github.com/ggml-org/llama.cpp
cd llama.cpp

cmake -B build
cmake --build build --config Release -j$(nproc)

# Binary is at build/bin/llama-server
./build/bin/llama-server -m ~/llama/model.gguf --port 8080
```

**NVIDIA GPU build:**

```bash
cmake -B build -DGGML_CUDA=ON
cmake --build build --config Release -j$(nproc)
```

---

## Running on a Server (LAN or Cloud)

Because `llama-server` is a plain HTTP service, running it on a remote box is trivial.

### On a Linux server

```bash
# 1. Copy (or download) the model on the server
scp model.gguf user@server:/opt/llama/

# 2. Run the server listening on all interfaces
./llama-server -m /opt/llama/model.gguf --host 0.0.0.0 --port 8080 -ngl 99
```

**Run it as a systemd service** so it survives reboots:

```ini
# /etc/systemd/system/llama-server.service
[Unit]
Description=llama.cpp inference server
After=network.target

[Service]
ExecStart=/opt/llama/llama-server -m /opt/llama/model.gguf --host 0.0.0.0 --port 8080 -ngl 99
Restart=always
User=llama

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable --now llama-server
```

### Exposing it safely

- **LAN only:** nothing else needed — clients use `http://<server-ip>:8080/v1`.
- **Cloud / public:** do **not** open the port bare. Put a reverse proxy with TLS and an API key in front:

```bash
# nginx snippet
location /v1/ {
    proxy_pass http://127.0.0.1:8080;
    # llama-server also accepts an API key check itself:
    # start it with --api-key <secret> and clients send
    # "Authorization: Bearer <secret>"
}
```

Start the server with a key and pass it from clients:

```bash
./llama-server -m model.gguf --port 8080 --api-key supersecret
```

### Cloud sizing tips

| GPU | Works well with |
|---|---|
| 8 GB VRAM (T4, RTX 3060) | 7B–9B models at Q4 |
| 16 GB VRAM | 14B models at Q4, 7B at Q8 |
| 24 GB VRAM | 32B models at Q4 |

Spot/preemptible instances are fine for experiments; use a persistent disk for the `.gguf` files so you don't re-download tens of GB.

---

## Calling the Model from Node.js

Since `llama-server` exposes the OpenAI API, the cleanest way from Node.js is the official `openai` package pointed at your server.

```bash
npm init -y
npm install openai
```

```javascript
// index.mjs
import OpenAI from "openai";

const client = new OpenAI({
  baseURL: "http://localhost:8080/v1", // or http://<server-ip>:8080/v1
  apiKey: "not-needed",                 // use your --api-key value if set
});

const completion = await client.chat.completions.create({
  model: "local", // llama-server ignores the model name
  messages: [
    { role: "system", content: "You are a helpful assistant." },
    { role: "user", content: "Explain what a GGUF file is in one sentence." },
  ],
});

console.log(completion.choices[0].message.content);
```

```bash
node index.mjs
```

**Streaming response:**

```javascript
const stream = await client.chat.completions.create({
  model: "local",
  messages: [{ role: "user", content: "Write a haiku about local LLMs." }],
  stream: true,
});

for await (const chunk of stream) {
  process.stdout.write(chunk.choices[0]?.delta?.content ?? "");
}
```

**Plain `fetch` (no dependencies):**

```javascript
const res = await fetch("http://localhost:8080/v1/chat/completions", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    messages: [{ role: "user", content: "Hello!" }],
  }),
});

const data = await res.json();
console.log(data.choices[0].message.content);
```

---

## Connecting Boris AI

Boris works with any OpenAI-compatible endpoint. In Boris settings, point the provider at your llama.cpp server:

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

If the server runs on another machine, replace `localhost` with the server IP (e.g. `http://192.168.1.50:8080/v1`).

---

## Quick Reference

```bash
# 1. Get a binary (or brew install llama.cpp)
# 2. Get a model
curl -L -O "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct-GGUF/resolve/main/qwen2.5-7b-instruct-q4_k_m.gguf"

# 3. Serve it
llama-server -m model.gguf --port 8080 -ngl 99

# 4. Talk to it
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"messages":[{"role":"user","content":"Hi"}]}'
```

## Troubleshooting

| Symptom | Fix |
|---|---|
| `out of memory` on load | Use a smaller quant (Q4_K_M) or a smaller model; drop `-ngl` on GPU-less machines |
| Very slow generation | CPU-only inference is slow for big models — use a smaller model or a GPU build (`-DGGML_CUDA=ON`) |
| Connection refused from Node | Check host/port; if the server is remote, make sure `--host 0.0.0.0` and the firewall allows the port |
| 401 response | The server was started with `--api-key`; send the same key as `apiKey` |
