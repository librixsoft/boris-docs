# Installation Guide

This guide covers building Boris AI from source and running it on your local machine.

## Prerequisites

| Requirement | Minimum Version | Notes |
|---|---|---|
| **Java JDK** | 21 | Must be JDK 21 or higher |
| **Maven** | 3.6+ | Required for building the project |
| **Ollama** | Latest | Or any OpenAI-compatible local model server |

### Verify Java

```bash
java -version
```

Expected output: `java version "21..."` or higher.

### Verify Maven

```bash
mvn -version
```

Expected output: `Apache Maven 3.6...` or higher.

## Building from Source

Boris uses Maven with a fat JAR (shaded) packaging strategy. All dependencies are bundled into a single executable JAR.

### Standard Build

```bash
cd /path/to/boris-ai
mvn clean package -DskipTests
```

This produces the fat JAR at:

```
target/boris-cli-1.0.0.jar
```

### Build with Tests

```bash
mvn clean package
```

### Build with E2E Tests

```bash
mvn verify -Pe2e
```

The E2E test profile (id: `e2e`) runs only `**/*E2ETest.java` and `**/e2e/**` tests.

## Running Boris

### Option 1: Using the run script (Recommended)

```bash
./run.sh
```

The `run.sh` script:
1. Runs `mvn clean package -DskipTests` to compile and package.
2. Launches `java -jar target/boris-cli-1.0.0.jar`.

#### Script flags

| Flag | Description |
|---|---|
| `./run.sh` | Compiles and launches Boris (skips tests) |
| `./run.sh --run-tests` | Runs the full test suite before launching |
| `./run.sh -- <args>` | Passes extra arguments to the Java process |

### Option 2: Direct JAR execution

```bash
java -jar target/boris-cli-1.0.0.jar
```

The application accepts a settings path as an argument (defaults to `~/.boris/settings.json`).

## Using a Custom Settings Path

Boris accepts the settings file path as a system property or programmatically:

```bash
java -Duser.home=$HOME -jar target/boris-cli-1.0.0.jar
```

The settings path is hardcoded as `System.getProperty("user.home") + "/.boris/settings.json"` in `BorisApp.run()`.

## Directory Structure

```
boris-ai/
├── pom.xml                         # Maven project config
├── run.sh                          # Build + launch script
├── src/main/java/com/boris/        # Source code
│   ├── cli/                        # TUI and CLI entry point
│   │   ├── BorisApp.java           # Picocli main entry
│   │   ├── BorisUI.java            # Lanterna TUI window manager
│   │   └── ui/                     # UI components
│   ├── chat/                       # Chat engine
│   │   ├── ChatService.java        # Chat + tool calling logic
│   │   └── ChatServiceTest.java    # Unit tests
│   ├── llm/                        # LLM adapter
│   │   ├── LlmClient.java          # ChatClient factory
│   │   └── think/                  # Ollama reasoning support
│   ├── settings/                   # Configuration
│   │   ├── Settings.java           # Settings POJO
│   │   ├── SettingsManager.java    # Load/ensure settings
│   │   └── ModelConfig.java        # Model config POJO
│   ├── tooling/                    # Agent tools
│   │   ├── ToolDefinition.java     # Tool schema definition
│   │   ├── integration/            # Tool registration
│   │   │   └── ToolCallingConfig.java
│   │   └── tool/                   # Individual tool implementations
│   ├── task/                       # Task management
│   │   └── TaskAborter.java        # Abort/reset support
│   └── exceptions/
│       └── BorisException.java     # Custom exception
├── src/main/resources/             # App resources
│   ├── application.properties      # Web search config
│   └── prompts/
│       ├── init/                   # Default templates
│       │   ├── settings.json
│       │   └── AGENTS.md
│       └── core/
│           └── default-system-prompt.md
├── src/test/java/com/boris/        # Test suite
├── target/                         # Build output
│   └── boris-cli-1.0.0.jar         # Fat JAR
└── models/                         # Optional model files
```

## Build Configuration

The project uses these key dependencies:

| Dependency | Version | Purpose |
|---|---|---|
| Spring AI | 1.0.0-M6 | Chat client, tool calling, OpenAI adapter |
| Picocli | 4.7.6 | CLI argument parsing |
| Lanterna | 3.1.2 | Terminal TUI framework |
| Jackson | 2.18.2 | JSON serialization |
| Apache PDFBox | 2.0.30 | PDF generation |
| Apache POI | 5.2.5 | Office document (Word/Excel/PowerPoint) |
| Playwright | 1.48.0 | Headless browser for web search |
| Jsoup | 1.17.2 | HTML parsing |
| CommonMark | 0.21.0 | Markdown parsing |
| Gson | 2.12.1 | JSON utilities |

### Maven Shade Plugin

The project uses `maven-shade-plugin` (v3.6.0) to produce a single fat JAR containing all dependencies. The main class is set to `com.boris.cli.BorisApp`.

### Maven Surefire Plugin

Tests are configured with Java module opens for Mockito/ByteBuddy:

```
--add-opens java.base/java.lang=ALL-UNNAMED
--add-opens java.base/java.util=ALL-UNNAMED
--add-opens java.base/java.lang.reflect=ALL-UNNAMED
--add-opens java.base/java.io=ALL-UNNAMED
--add-opens java.base/java.net=ALL-UNNAMED
--add-opens java.base/java.net.http=ALL-UNNAMED
-XX:+EnableDynamicAgentLoading
-Dnet.bytebuddy.experimental=true
```

## Troubleshooting

### "Java is not recognized"

Install JDK 21 or higher:

```bash
# macOS (Homebrew)
brew install openjdk@21

# Ubuntu/Debian
sudo apt install openjdk-21-jdk
```

### "mvn: command not found"

Install Maven:

```bash
# macOS (Homebrew)
brew install maven

# Ubuntu/Debian
sudo apt install maven
```

### Build fails with module errors

Ensure you're using Java 21+. Older JDK versions don't support the required module opens.

### Ollama connection refused

Make sure Ollama is running:

```bash
ollama serve
```

Default Ollama endpoint: `http://localhost:11434`
