---
sidebar_position: 4
id: architecture
title: How Everything Works
sidebar_label: Architecture
description: An overview of ascii-ui.nvim's declarative model, fiber reconciler, reactive state loop, and viewport system.
tags: [architecture, fiber, reconciler, reactive, state, viewport]
---

# How Everything Works

ascii-ui.nvim is built on a declarative and reactive model, taking inspiration
from modern component-based frameworks like React. Instead of manually updating
the screen, you declare the desired state, and the framework handles the rest.

---

## 1. The Declarative Core

You declare **what** you want your UI to look like at any point in time, rather
than writing sequential steps to manipulate a terminal buffer.

```
+------------------+         +-----------------+
| Your Component   |         | ascii-ui.nvim   |
| (Render Function)| ------->| (The Renderer)  |
|                  |         |                 |
| return {         |         |  Produces the   |
|   Button(...),   |         |  correct output |
|   Paragraph(...),|         |  automatically. |
| }                |         |                 |
+------------------+         +-----------------+
```

- **Render function as blueprint**: Every component's render function returns a
  new description (a tree of components and blocks) based on current state.
- **No direct manipulation**: You never call Neovim APIs to change text, colors,
  or window positions inside a component. You return a definition; the framework
  ensures the output matches.

---

## 2. The Fiber Reconciler

Internally, ascii-ui.nvim uses a **fiber tree** — a linked data structure where
each node (`FiberNode`) represents one component or one leaf buffer line.

```
RootFiberNode (App)
├── FiberNode (Paragraph)   ← leaf: holds a BufferLine
├── FiberNode (Counter)     ← non-leaf: has a closure + child
│   ├── FiberNode (Paragraph)
│   └── FiberNode (Button)
└── FiberNode (Paragraph)
```

### Render pass

When `ui.mount` is called, the fiber performs a **depth-first work loop**:

1. Each `FiberNode` with a `closure` is executed — the closure calls the
   component render function and returns child nodes.
2. Children are **reconciled** against the previous output:
   - If a node's type and props are unchanged, the old node is **reused**
     (avoiding unnecessary work).
   - If the type or props changed, a new node is created (`REPLACEMENT`).
3. Leaf nodes accumulate `BufferLine` objects that are collected into a
   `Buffer` and written to the viewport.

### Re-render pass

When state changes (a `useState` setter is called), the framework:

1. Emits a `state_change` event.
2. Runs the work loop again from the root (`rerender`).
3. Runs any pending `useEffect` cleanups, then re-runs effects.
4. Calls `viewport:update(buffer)` with the new buffer.

---

## 3. The Reactive State Loop

```
+-------------+    setCount()      +-----------------+    re-render    +-----------+
| User Input  | -----------------> | Component State | --------------> | Render Fn |
|             |    triggers        | (via useState)  |                 |           |
+-------------+                    +-----------------+                 +-----v-----+
                                                                             |
                                                                    Returns new UI tree
                                                                             |
                                                                             v
                                                            +-------------------------+
                                                            | Fiber Reconciler        |
                                                            | Diffs old vs new tree,  |
                                                            | updates the viewport.   |
                                                            +-------------------------+
```

- **`useState`** gives each component instance local memory (the state).
- **The setter** (e.g. `setCount(newValue)`) triggers a `state_change` event.
- **Automatic re-render**: ascii-ui schedules the entire tree to re-run.
  Only the changed parts of the buffer are written to the viewport.

---

## 4. Component Composition

UIs are built by composing small, isolated components — each managing its own
state and rendering its own piece of the output.

```
       +-----+
       | App |
       +--+--+
          |
    +-----+------+
    |             |
+---+----+   +---+---+
| Header |   | Body  |
+--------+   +---+---+
                 |
           +-----+-----+
           |           |
       +---+---+   +---+----+
       | List  |   | Button |
       +-------+   +--------+
```

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button
local useState = ui.hooks.useState

local Counter = ui.createComponent("Counter", function(props)
    local count, setCount = useState(0)
    return {
        Paragraph({ content = (props.label or "Count") .. ": " .. count }),
        Button({ label = "+1", on_press = function() setCount(count + 1) end }),
    }
end)

local App = ui.createComponent("App", function()
    return {
        Paragraph({ content = "=== Dashboard ===" }),
        Counter({ label = "Commits" }),
        Counter({ label = "Issues" }),
    }
end)

ui.mount(App)
```

Each `Counter` instance has its own independent state. Composition is the
primary pattern for building modular, reusable UIs.

---

## 5. Viewports

A **viewport** is any Lua object that satisfies the `ascii-ui.Viewport`
interface. It is responsible for displaying the rendered `Buffer`.

```
ascii-ui.nvim
    │
    │  ui.mount(App, viewport)
    │
    ▼
┌──────────────────────────────┐
│  Fiber render loop           │
│  → produces a Buffer         │
└──────────────┬───────────────┘
               │ viewport:update(buffer)
               ▼
  ┌────────────────────────────┐
  │  Viewport implementation   │
  │  • Window (default)        │  ← Neovim floating window
  │  • StdoutViewport          │  ← Terminal stdout / ANSI
  │  • (custom)                │  ← Implement the interface
  └────────────────────────────┘
```

### Viewport interface

| Method | Called when |
|--------|-------------|
| `viewport:open()` | Once, immediately after the first render |
| `viewport:update(buffer)` | On every state-change re-render |
| `viewport:close()` | When the `WinClosed` autocmd fires |
| `viewport:is_focused()` | On every key event — gates keybinding handling |
| `viewport:get_id()` | To identify the Neovim window (`-1` for non-Neovim targets) |
| `viewport:get_bufnr()` | To identify the Neovim buffer (`-1` for non-Neovim targets) |

The default viewport (used when no second argument is passed to `ui.mount`) is
a centered Neovim **floating window** sized to fit the rendered content.

See [Viewports — StdoutViewport](./viewports/stdout.md) for the built-in
non-Neovim viewport, and [ui.mount](./mount) for the full mount lifecycle.

---

## 6. Lifecycle Summary

```
ui.mount(App)
    │
    ├─ 1. Fiber render pass        (builds the initial tree)
    ├─ 2. viewport:open()          (opens the window / stdout)
    ├─ 3. viewport:update(buffer)  (writes the first frame)
    ├─ 4. useEffect runs           (side effects execute)
    │
    │  [user interacts / timer fires]
    │
    ├─ 5. setState called          (state_change event emitted)
    ├─ 6. Fiber re-render pass     (diffs the tree)
    ├─ 7. viewport:update(buffer)  (writes changed lines)
    ├─ 8. useEffect cleanup + re-run
    │
    │  [window closed]
    │
    └─ 9. viewport:close() + useEffect cleanups + EventListener cleared
```

---

## See Also

- [Custom Components](./custom_components.md) — how to write render functions and compose components
- [Hooks](./hooks) — useState, useEffect, useReducer, useInterval, useTimeout
- [ui.mount](./mount) — detailed mount API and viewport interface
- [Blocks](./blocks) — low-level BufferLine / Segment primitives
