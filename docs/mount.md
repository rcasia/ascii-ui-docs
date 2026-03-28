---
id: mount
title: ui.mount
sidebar_label: ui.mount
sidebar_position: 5
description: Mount a root component into a viewport and start the render loop.
tags: [api]
---

# ui.mount

`ui.mount` starts the render loop for a root component. It performs the initial render, opens the viewport, binds keyboard interactions, and starts listening for state changes.

```lua
ui.mount(RootComponent)
-- or with an explicit viewport:
ui.mount(RootComponent, viewport)
```

---

## Reference

### `ui.mount(RootComponent, viewport?)`

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `RootComponent` | `FunctionalComponent` | Yes | The root component to render. This is a component created with `ui.createComponent`. |
| `viewport` | `ascii-ui.Viewport` | No | Where to render the UI. Defaults to a centered Neovim floating window. |

#### Returns

| Name | Type | Description |
|------|------|-------------|
| `bufnr` | `integer` | The Neovim buffer number used by the viewport. Returns `-1` for non-Neovim viewports (e.g. `StdoutViewport`). |

---

## Usage

### Default: Neovim floating window

The simplest call opens a centered, rounded floating window automatically sized to fit the rendered content:

```lua
local ui = require("ascii-ui")

local MyApp = ui.createComponent("MyApp", function()
  return {
    ui.components.Paragraph({ content = "Hello from a floating window!" }),
  }
end)

ui.mount(MyApp)
```

### StdoutViewport (headless / CLI)

Pass a `StdoutViewport` to render to the terminal instead of a Neovim window. Useful for headless scripts, CI pipelines, or animations:

```lua
local ui = require("ascii-ui")

local MyApp = ui.createComponent("MyApp", function()
  return {
    ui.components.Paragraph({ content = "Rendered to stdout!" }),
  }
end)

ui.mount(MyApp, ui.viewports.StdoutViewport.new())
```

---

## Lifecycle

`ui.mount` manages the full lifecycle of the UI:

1. **Initial render** — Calls `fiber.render(RootComponent)` to build the fiber tree and produce the first buffer.
2. **Open viewport** — Calls `viewport:open()`. For the default floating window this creates the Neovim buffer and window.
3. **Update viewport** — Calls `viewport:update(buffer)` to write the rendered content.
4. **Keyboard bindings** — Registers `vim.on_key` for `h`/`j`/`k`/`l` navigation between focusable segments.
5. **State change listener** — On every `useState` setter call, rerenders and refreshes the viewport.
6. **Cleanup** — On `WinClosed`, detaches all bindings, calls `viewport:close()`, and runs `useEffect` cleanups.

---

## Default keyboard bindings

These bindings are active while the ascii-ui window is focused:

| Key | Action |
|-----|--------|
| `h` | Move focus left / to the previous focusable element |
| `l` | Move focus right / to the next focusable element |
| `k` | Move focus up |
| `j` | Move focus down |
| `<CR>` | Trigger the `SELECT` interaction on the focused element |
| `q` | Close the window (configurable via `ui.setup`) |

---

## Custom viewport

Any object satisfying the `ascii-ui.Viewport` interface can be passed as the second argument. The interface requires these methods:

| Method | Description |
|--------|-------------|
| `open()` | Initialize/open the output target |
| `close()` | Tear down |
| `update(buffer)` | Render the buffer frame |
| `is_focused()` | Returns `true` if the viewport has user focus |
| `enable_edits()` | Allow text input (for `Input` widgets) |
| `disable_edits()` | Revert to read-only |
| `get_id()` | Neovim window id, or `-1` |
| `get_bufnr()` | Neovim buffer number, or `-1` |
| `get_ns_id()` | Neovim namespace id, or `-1` |

See the [StdoutViewport](./viewports/stdout.md) documentation for a built-in example.
