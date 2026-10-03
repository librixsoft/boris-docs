# Agent Tools Reference

Boris ships with 10 built-in tools that the agent can use autonomously. Each tool is registered via Spring AI's `@Tool` annotation and executed sequentially by the chat engine.

---

## Filesystem Tools

### `read_file`

Reads the contents of a file at the given path. Returns the full file content as a string.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `path` | string | Yes | Absolute or relative path to the file |

**Returns:** JSON with `success`, `message` (file content), and `error` fields.

**Example:**
```
read_file(path: "/Users/anibal/project/Main.java")
```

**Notes:**
- Auto-creates parent directories for write operations.
- Reads entire file into memory (no line-range filtering).

---

### `write_file`

Creates a new file or overwrites an existing one with the given content.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `path` | string | Yes | Absolute or relative path for the file |
| `content` | string | Yes | Full text content to write |

**Returns:** JSON with `success`, `message` (includes file size in bytes).

**Example:**
```
write_file(path: "/Users/anibal/project/hello.py", content: "print('Hello, World!')")
```

---

### `apply_edit`

Applies a surgical edit to an existing file by finding `old_text` and replacing it with `new_text`. Only replaces the first occurrence.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `path` | string | Yes | Absolute or relative file path |
| `old_text` | string | Yes | Exact text to find and replace |
| `new_text` | string | Yes | Replacement text |

**Returns:** JSON with `success`, `message` (includes file size).

**Example:**
```
apply_edit(path: "/Users/anibal/project/Main.java", old_text: "System.out.println(\"old\");", new_text: "System.out.println(\"new\");")
```

---

### `multi_edit`

Applies multiple sequential edits to a single file. Each edit replaces `old_text` with `new_text` in order.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `path` | string | Yes | Absolute or relative file path |
| `edits` | array | Yes | Array of edit objects, each with `old_text` and `new_text` |

**Returns:** JSON with `success`, `message` (includes total edits applied).

**Example:**
```
multi_edit(path: "/Users/anibal/project/Main.java", edits: [
  { old_text: "old_value", new_text: "new_value_1" },
  { old_text: "another", new_text: "replacement" }
])
```

---

### `revert_edit`

Reverts a previous edit by finding `old_text` (the edited text) and replacing it with `new_text` (the original content to restore).

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `path` | string | Yes | Absolute or relative file path |
| `old_text` | string | Yes | The edited text to find |
| `new_text` | string | Yes | Original content to restore |

**Returns:** JSON with `success`, `message`.

---

### `delete_file`

Deletes a file at the given path.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `path` | string | Yes | Absolute or relative path to the file |

**Returns:** JSON with `success`, `message`.

**Notes:**
- Returns success if the file doesn't exist.
- Returns error if the path is a directory.

---

### `list_files`

Lists files and directories in the given directory. Returns a formatted listing with file sizes.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `path` | string | Yes | Directory path to list |

**Returns:** JSON with `success`, `message` (formatted listing with `D`/`F` prefix, file size, and filename).

**Example Output:**
```
D          4096  src
F        12345  pom.xml
F         6789  README.md
```

---

## Shell & Execution

### `execute_command`

Executes a shell command on the operating system. Runs asynchronously with abort support.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `command` | string | Yes | Shell command to execute (e.g., `git status`, `mvn test`) |
| `workingDirectory` | string | No | Working directory for execution (defaults to current directory) |

**Returns:** JSON with `success`, `exitCode`, `stdout`, `stderr`, `message`.

**Example:**
```
execute_command(command: "git log --oneline -5", workingDirectory: "/Users/anibal/project")
```

**Notes:**
- Default timeout: 60 seconds.
- On timeout: kills the process forcibly.
- Cross-platform: uses `cmd.exe /c` on Windows, `/bin/sh -c` on Unix.
- Supports task abort via `ESC` key in the TUI.

---

## Document Generation

### `pdf_generation`

Generates a PDF file from HTML, Markdown, or plain text content using Apache PDFBox.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `content` | string | Yes | Content to convert (HTML, Markdown, or plain text) |
| `outputPath` | string | Yes | Output file path for the PDF |
| `contentType` | string | Yes | Type: `"html"`, `"markdown"`, or `"text"` |

**Returns:** JSON with `success`, `message`.

**Example:**
```
pdf_generation(content: "# Report\n\nThis is the report.", outputPath: "/Users/anibal/report.pdf", contentType: "markdown")
```

**Notes:**
- Supports multi-page output with automatic page breaks.
- Markdown is converted to HTML first, then stripped to plain text for PDF.
- Uses Helvetica font at 12pt.

---

### `office_document`

Creates Word (`.docx`), PowerPoint (`.pptx`), or Excel (`.xlsx`) documents with advanced styling.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `documentType` | string | Yes | `"word"`, `"powerpoint"`, or `"excel"` |
| `outputPath` | string | Yes | Output file path |
| `title` | string | No | Document title (default: "Document") |
| `content` | string | No | Document content |
| `customization` | object | No | JSON with styling options |

**Customization options:**

| Category | Options |
|---|---|
| **Colors** | `primaryColor`, `secondaryColor`, `accentColor`, `textColor`, `backgroundColor` (hex strings) |
| **Typography** | `fontFamily`, `bodyFontSize`, `headerFontSize`, `footerFontSize`, `boldTitle`, `italicBody` |
| **Spacing** | `marginTop`, `marginBottom`, `marginLeft`, `marginRight`, `paddingHeader`, `paddingContent`, `paddingFooter`, `lineSpacing` |
| **Layout** | `layout`: `"oneColumn"`, `"twoColumn"`, `"threeColumn"`, `"grid"` |
| **Style** | `style`: `"corporate"`, `"modern"`, `"minimal"`, `"colorful"` |
| **Header** | `headerStyle`: `"solid"`, `"gradient"`, `"banner"` |
| **Borders** | `borderStyle`: `"solid"`, `"dashed"`, `"dotted"`, `"none"`, `borderWidth` (1-5) |
| **Tables** | `tableHeaderBg`, `tableRowBg`, `tableAlternateRowBg`, `tableBorderColor` |
| **Effects** | `shadowEffect` (boolean) |

**Example:**
```
office_document(documentType: "word", outputPath: "/Users/anibal/report.docx", title: "Report", content: "Content here", customization: { layout: "twoColumn", style: "corporate", primaryColor: "1A73E8" })
```

---

## Web & Information

### `web_search`

Searches the web using Bing via Playwright headless browser. No API key required.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | Yes | Search query string |
| `count` | integer | No | Number of results (1-10, default: 5) |

**Returns:** JSON with `success`, `query`, `results` array (each with `title`, `url`, `snippet`), and `error`.

**Example:**
```
web_search(query: "Spring AI tool calling documentation", count: 3)
```

**Notes:**
- Uses Playwright's Chromium in headless mode.
- Default timeout: 15 seconds per request.
- Waits 2 seconds for dynamic content loading.
- Configuration via `application.properties` (`web.search.*` keys).

---

### `system_info`

Retrieves system information including OS, CPU, memory, and storage.

**Parameters:** None.

**Returns:** JSON with `os`, `os_version`, `arch`, `hostname`, `available_processors`, `total_storage_bytes`, `free_storage_bytes`.

**Example:**
```
get_system_info()
```

**Returns:**
```json
{
  "os": "Mac OS X",
  "os_version": "26.5.2",
  "arch": "x86_64",
  "hostname": "anibals-macbook-pro",
  "available_processors": 10,
  "total_storage_bytes": 500107862016,
  "free_storage_bytes": 123456789012
}
```

---

## Tool Execution Flow

Boris executes tools in a **sequential** loop:

1. The user sends a message.
2. The model decides which tool(s) to call based on the system prompt and user request.
3. Each tool call is sent to the model via Spring AI's function calling.
4. The tool executes and returns its result.
5. The result is fed back to the model.
6. Steps 2-5 repeat until the model decides no more tools are needed.
7. The model produces the final response for the user.

All tool calls are handled through the `ChatService.sendMessage()` or `sendMessageStream()` methods. The `enforceSequentialExecution` setting ensures tools are processed one at a time.
