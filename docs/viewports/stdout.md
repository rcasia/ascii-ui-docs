---
id: stdout-viewport
title: StdoutViewport
sidebar_label: StdoutViewport
sidebar_position: 1
description: Render ascii-ui output to terminal stdout with ANSI truecolor support.
tags: [api, viewports]
---

# StdoutViewport

`StdoutViewport` is a viewport implementation that renders the UI to terminal stdout using ANSI truecolor escape codes. It is useful for headless scripts, CI pipelines, terminal animations, or any context where Neovim's windowing API is not available.

```lua
local viewport = ui.viewports.StdoutViewport.new()
ui.mount(MyComponent, viewport)
```

---

## Reference

### `StdoutViewport.new(writer?)`

Creates a new `StdoutViewport`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `writer` | `fun(s: string)` | No | A custom output function. Defaults to `io.write`. Override this in tests or to redirect output to a file or buffer. |

#### Returns

A new `StdoutViewport` instance that satisfies the `ascii-ui.Viewport` interface.

---

## Usage

### Basic stdout rendering

```lua
local ui = require("ascii-ui")

local MyApp = ui.createComponent("MyApp", function()
  return {
    ui.components.Paragraph({ content = "Hello from stdout!" }),
  }
end)

ui.mount(MyApp, ui.viewports.StdoutViewport.new())
```

### Animated output with useInterval

`StdoutViewport` works with all hooks, including `useInterval` for animations:

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local useInterval = ui.hooks.useInterval
local Paragraph = ui.components.Paragraph

local Clock = ui.createComponent("Clock", function()
  local tick, setTick = useState(0)

  useInterval(function()
    setTick(tick + 1)
  end, 1000)

  return {
    Paragraph({ content = "Tick: " .. tick }),
  }
end)

ui.mount(Clock, ui.viewports.StdoutViewport.new())
```

Each second the screen clears and rerenders with the updated tick count.

### Colored output

Segments with a `color` field are rendered with ANSI truecolor sequences:

```lua
local ui = require("ascii-ui")
local Segment = ui.blocks.Segment
local Bufferline = ui.blocks.Bufferline

local ColorDemo = ui.createComponent("ColorDemo", function()
  return {
    Bufferline(
      Segment({ content = "Red text",   color = { fg = "#ff0000" } }),
      Segment({ content = " | " }),
      Segment({ content = "Blue bg",    color = { bg = "#0000ff" } })
    ),
  }
end)

ui.mount(ColorDemo, ui.viewports.StdoutViewport.new())
```

### Custom writer (for testing or file output)

```lua
local lines = {}
local viewport = ui.viewports.StdoutViewport.new(function(s)
  table.insert(lines, s)
end)
ui.mount(MyApp, viewport)
-- lines now contains the rendered output
```

---

## Limitations

| Feature | Support |
|---------|---------|
| Color (truecolor) | Supported via ANSI SGR escape codes |
| Keyboard interactions | Not supported (`is_focused()` always returns `false`) |
| `Input` widget edits | Not applicable |
| `WinClosed` cleanup | Not triggered (no Neovim window) |
| `get_id()` / `get_bufnr()` / `get_ns_id()` | Always return `-1` |

---

## How it works

On each `update(buffer)` call, `StdoutViewport`:

1. Clears the terminal (`ESC[H ESC[2J`).
2. Iterates all buffer lines.
3. For each colored segment, wraps the text with ANSI SGR truecolor sequences (`ESC[38;2;r;g;bm` for foreground, `ESC[48;2;r;g;bm` for background) followed by a reset (`ESC[0m`).
4. Writes all lines separated by newlines.
