# Terminal UI Reference

Boris features a full-screen terminal interface powered by [Lanterterna](https://github.com/MartinTou/lanterna), a Java TUI library. The interface supports mouse interaction, keyboard shortcuts, and real-time streaming.

---

## Interface Layout

```
┌────────────────────────────────────────────────────────────────┐
│  HEADER BAR                                                     │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  CHAT PANEL (scrollable)                                        │
│                                                                 │
│  user: Refactor the auth module                                 │
│                                                                 │
│  ●boris Applying edits to AuthController...                     │
│  [thinking] Analyzing dependencies and import statements...     │
│  auth/AuthenticationManager.java edited successfully            │
│  auth/SecurityConfig.java edited successfully                   │
│                                                                 │
│  Here are the changes I made:                                   │
│                                                                 │
│  1. Updated the token service...                                │
│                                                                 │
│  ❯ <input area>                                                 │
├────────────────────────────────────────────────────────────────┤
│  STATUS BAR  |  Tokens: 1234/10000  |  ESC:abort  Tab:focus    │
└────────────────────────────────────────────────────────────────┘
```

### Components

| Component | Description |
|---|---|
| **Header Bar** | Top bar of the window (Lanterna window decorations) |
| **Chat Panel** | Main scrollable area showing the conversation history. Renders user messages, assistant responses, and thinking blocks. |
| **Input Area** | Bottom text input for typing messages. Supports command history (up/down arrows), Enter to submit. |
| **Hint Bar** | Displays available keyboard shortcuts and commands |
| **Status Bar** | Shows token usage, abort status, and thinking indicator |

---

## Keyboard Shortcuts

| Key / Keys | Action |
|---|---|
| `Enter` | Submit message to the model |
| `ESC` | Abort the current running task (interrupts LLM call or shell command) |
| `Tab` | Move focus between input area and chat panel |
| `↑` / `↓` | Navigate command history (previous/next message) |
| `Page Up` / `Page Down` | Scroll chat panel by full page |
| `Scroll Up` / `Scroll Down` | Scroll chat panel by 3 lines (mouse wheel) |
| `Click + Drag` | Select text in the chat panel (selection highlight) |

---

## Mouse Support

The TUI captures mouse events for:

- **Scroll wheel**: Scrolls the chat panel up/down by 3 lines per tick.
- **Click**: Focuses the input area or selects text in the chat panel.
- **Drag**: Creates text selection with visual highlight in the chat panel.
- **Click + Release**: Finalizes text selection in the chat panel.

`ESC` cancels an active selection.

---

## Streaming Display

When the model responds, Boris streams the output in real-time:

1. **Prefix** — `●boris` appears in the status bar with a thinking indicator.
2. **Thinking blocks** — If the model produces `thinking` content, it's displayed as:
   ```
   <thinking>
   Analyzing the request...
   </thinking>
   ```
3. **Main response** — Streamed text appears after the thinking block.
4. **Tool calls** — Results of tool executions are shown inline.
5. **Completion** — Status bar updates with token usage; input area is re-enabled.

The input area remains visible during streaming. If the user types a new message while the model is still responding, it's queued and sent after the current response completes.

---

## Token Counter

The status bar displays real-time token estimation:

```
Tokens: 1234/10000
```

The denominator (`contextWindow`) comes from `settings.json`. When the limit is reached, Boris shows a warning and refuses new messages until `/clear` is used.

The token counter uses character length (`chunk.length()`) as an approximation. Actual token counts depend on the model's tokenizer.

---

## Thinking Spinner

When a message is being processed, the status bar shows a thinking spinner animation. The spinner:
- Appears when a message is submitted.
- Disappears when the response completes or is aborted.
- Updates to show token count after completion.

---

## Visual Theme

Boris uses a custom dark theme based on Lanterna's dark palette:

```java
DefaultTheme darkTheme = new DefaultTheme();
darkTheme.setTextStyle(TextStyle.NORMAL).setTextDecoration(TextDecoration.NONE);
darkTheme.setTextColor(TextColor.ANSI.Blue);
// ... all colors customized
```

The theme applies to all UI components: chat panel, input area, status bar, and separators.

---

## Window Behavior

- **Full screen**: The window launches in full-screen mode (`Window.HINT.FULL_SCREEN`).
- **No decorations**: Window borders are minimal (`Window.HINT.NO_DECORATIONS`).
- **Resize handling**: On window resize, the chat transcript is re-rendered to fit the new dimensions.
- **Close**: Close the window or type `/exit` to quit Boris.
