# Command Reference

Boris supports in-app commands entered in the input area (type and press Enter). These commands control the agent's behavior without needing to edit configuration files.

---

## Message Commands

| Command | Description |
|---|---|
| `/exit` | Close Boris and exit the application |
| `/quit` | Alias for `/exit` |
| `/clear` | Clear the conversation history, reset token counter, and clear the chat panel |
| `/thinking` | Toggle reasoning (thinking) on/off |
| `/think` | Alias for `/thinking` |
| `/reasoning` | Alias for `/thinking` |
| `/thinking on` | Enable reasoning |
| `/thinking off` | Disable reasoning |
| `/think on` | Alias for `/thinking on` |
| `/think off` | Alias for `/thinking off` |
| `/reasoning on` | Alias for `/thinking on` |
| `/reasoning off` | Alias for `/thinking off` |

---

## Keyboard Shortcuts

| Key | Action |
|---|---|
| `Enter` | Submit message |
| `ESC` | Abort current task (LLM call or shell command) |
| `Tab` | Toggle focus between input area and chat panel |
| `↑` | Previous message in history |
| `↓` | Next message in history |
| `Page Up` | Scroll chat panel up (full page) |
| `Page Down` | Scroll chat panel down (full page) |
| `Mouse Wheel Up` | Scroll chat panel up (3 lines) |
| `Mouse Wheel Down` | Scroll chat panel down (3 lines) |
| `Click + Drag` | Select text in chat panel |
| `ESC` (during selection) | Cancel text selection |

---

## Exit Commands

The following inputs also terminate the application:

| Input | Description |
|---|---|
| `q` | Shorthand exit |
| `exit` | Full exit command |
| `EXIT` | Case-insensitive exit |

When Boris receives an EXIT command from the model (e.g., as a tool result), it also triggers a graceful shutdown.

---

## Token Limit

Boris enforces a token limit based on the `contextWindow` setting (default: 10,000 characters). When the limit is reached:

- A warning message is displayed in the chat.
- New messages are rejected until `/clear` is used.
- The token counter resets after clearing.

This is an approximate limit based on character count, not actual token count.

---

## Command History

Boris remembers your previously sent messages. Use the `↑` and `↓` arrow keys to navigate through your message history. Commands are stored in memory for the current session.

---

## Abort Behavior

Pressing `ESC` during processing:

1. Sets an abort flag in the `TaskAborter`.
2. Interrupts the current thread (LLM call or shell command).
3. Shows "aborted" in the status bar.
4. The abort flag resets after ~100ms for the next message.

If a shell command is running (e.g., `execute_command`), it receives a thread interrupt. If the command doesn't handle the interrupt, it terminates. The `execute_command` tool also has a hard 60-second timeout.
