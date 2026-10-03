# Welcome to Boris AI Documentation

!!! info "Project in Development"
    This is the official documentation for Boris AI.

## What is Boris AI?

**Boris** is an autonomous terminal-based AI development assistant built with Java 21, Spring AI, Lanterna (TUI), and Picocli.

Boris is designed exclusively for running local AI models (Ollama, llama.cpp, or local OpenAI-compatible inference servers). It does **not** use or rely on third-party cloud AI APIs, ensuring complete data privacy, offline capability, zero subscription fees, and total ownership over your code and prompts.

Boris connects your local models with a rich set of developer tools to inspect codebases, execute shell commands, edit files, generate documents, and perform complex automation workflows directly from your terminal.

---

## Key Features

- **100% Local AI Execution** — Exclusively engineered for local model runtimes (Ollama / local inference endpoints). Your code and data never leave your machine or local network.

- **Terminal User Interface (TUI)** — Rich interactive full-screen terminal interface powered by Lanterna, featuring Markdown rendering, scrolling, mouse capture, command menu, and live token usage tracking.

- **Native Reasoning & `thinking` Blocks** — Real-time streaming and inspection of reasoning traces with configurable reasoning effort (`high`, `medium`, `low`, `none`) for local models with thinking capabilities (e.g., Granite 4.2, Qwen, DeepSeek).

- **Autonomous Tool Execution** — Built-in set of developer and system tools with sequential execution and automatic result feedback:
  - **Filesystem & Code**: Read, write, surgically edit, list, and delete files with precision.
  - **Shell & Execution**: Run shell commands, inspect output, and capture execution results.
  - **Documents**: Generate PDFs and create/inspect Office documents (Word, Excel, PowerPoint).
  - **Web & Information**: Web search integration and system resource monitoring.

- **Task Abort & Control** — Ability to abort running tasks, commands, and generations on demand.

- **Zero Cloud Subscriptions** — Full autonomy and control running on your own hardware.

- **Automatic Initialization** — Automatically bootstraps configurations and agent rules in `~/.boris/` upon first run.

---

## Architecture Overview

Boris is built on a modular architecture:

| Layer | Technology | Purpose |
|---|---|---|
| **CLI Entry** | Picocli | Command-line argument parsing and application bootstrap |
| **TUI** | Lanterna | Full-screen terminal interface with mouse support |
| **Chat Engine** | Spring AI | Chat client, message streaming, and conversation history |
| **LLM Adapter** | Spring AI OpenAI + OllamaThinkSpringAiFactory | Connects to local models via `/v1/chat/completions` endpoint with custom `reasoning_effort` support |
| **Tool System** | Spring AI `@Tool` / `ToolCallbacks` | Declarative tool registration via annotations |
| **Settings** | Jackson (JSON) | Persistent configuration in `~/.boris/settings.json` |
| **Task Management** | Custom TaskAborter | Thread-level task abort and reset |

---

## Built-in Agent Tools

| Tool | Description |
| :--- | :--- |
| `read_file` | Reads contents of files in the workspace with full path resolution |
| `write_file` | Creates or replaces files, auto-creates parent directories |
| `edit_file` | Surgically edits files using string replacements (`apply_edit`, `multi_edit`, `revert_edit`) |
| `list_files` | Explores workspace directories, file sizes, and children counts |
| `delete_file` | Deletes files or directories safely |
| `execute_command` | Executes shell commands in the background or synchronously with abort support (60s timeout) |
| `pdf_generation` | Generates formatted PDF documents from text/markdown/HTML using Apache PDFBox |
| `office_document` | Creates Word (`.docx`), PowerPoint (`.pptx`), and Excel (`.xlsx`) documents with advanced styling |
| `web_search` | Performs web queries and extracts relevant search results via Bing using Playwright (no API key) |
| `system_info` | Retrieves OS details, CPU/memory stats, storage, and environment info |

---

## Recommended Models

Boris has been tested and verified with the following local models. All run via Ollama or any OpenAI-compatible local server.

| Model | Parameters | Reasoning | Notes |
|---|---|---|---|
| **GPT-20B** | 20B | No | Solid general-purpose model, good reasoning baseline |
| **Laguna-XS-2.1** | 30B | No | Strong code generation and tool calling |
| **Nemotron-3.5-Lightning:30B** | 30B | No | Fast inference, balanced quality/speed |
| **Granite4.2:8B** | 8B | Yes | Native `reasoning_effort` support, excellent for thinking tasks |
| **Qwen 3.6:35B** | 35B | Yes | Deep reasoning, strong code and document generation |
| **Qwen 3.6:9B** | 9B | No | Lightweight alternative, fast responses |
| **Gemma4:26B** | 26B | No | Strong general capability, good tool use |
| **Gemma4:4B** | 4B | No | Fastest model, ideal for quick tasks and constrained hardware |

!!! tip "Model Selection Guide"
    - **Thinking/reasoning tasks**: Use Granite4.2:8B or Qwen 3.6:35B (both support `reasoning_effort`)
    - **Code generation**: Laguna-XS-2.1:30B or Qwen 3.6:35B
    - **Fast responses**: Gemma4:4B or Qwen 3.6:9B
    - **Balanced quality/speed**: Nemotron-3.5-Lightning:30B or GPT-20B

---

## Prerequisites

- **Java 21+** (JDK 21 or higher)
- **Maven 3.6+**
- **Ollama** (or any OpenAI-compatible local model server) running locally or over the network.

---

## Quick Start

### 1. Build and Run with `run.sh` (Recommended)

```bash
# Compile (skipping tests) and launch Boris CLI
./run.sh
```

**Additional execution options:**
- `./run.sh` — Compiles and launches the CLI.
- `./run.sh --run-tests` — Runs test suite before packaging and launching.
- `./run.sh -- <args>` — Passes command line arguments to Boris.

### 2. Manual Build and Run

```bash
mvn clean package
java -jar target/boris-cli-1.0.0.jar
```

---

## What happens on first launch?

On first run, Boris automatically creates `~/.boris/` along with:

- **`~/.boris/settings.json`** — Primary configuration file for model endpoints, parameters, and environment.
- **`~/.boris/AGENTS.md`** — Persona instructions, execution rules, and system prompt constraints.

These templates come bundled in the app's classpath (`src/main/resources/prompts/init/`) and are copied only if they don't already exist.

---

!!! note "Getting Started"
    - [Installation Guide](guide/installation.md) - How to build and run Boris
    - [Configuration](guide/configuration.md) - Detailed settings.json reference
    - [Agent Tools](guide/tools.md) - Full tool reference
    - [Command Reference](guide/commands.md) - In-app commands and keyboard shortcuts
