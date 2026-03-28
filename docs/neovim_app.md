---
sidebar_position: 2.1
id: neovim-app
title: What is a Neovim App?
sidebar_label: What is a Neovim App?
description: Understand the concept of a Neovim App and how ascii-ui.nvim lets you build self-contained interactive applications inside Neovim.
tags: [concepts, neovim, app]
---

# What is a Neovim App?

Imagine a new kind of application that lives inside Neovim — not just a plugin
that adds a feature here and there, but a fully-formed, interactive experience.
A **Neovim App** is exactly that: a self-contained application that runs inside
your editor, renders its own UI using ASCII characters, and reacts to user input
in real time.

Unlike traditional plugins, a Neovim App is designed to feel like its own
world. You can launch it from the command line, from another plugin, or as
part of your workflow.

In short: a Neovim App isn't just extending Neovim — it's **building on top
of it**. It's a new way to think about what's possible inside your editor.

---

## Anatomy of a Neovim App

Every ascii-ui.nvim application has the same three parts:

```
1. Setup (optional)     → ui.setup({ ... })   configure theme, keymaps, log level
2. Root component       → ui.createComponent  declare what to render
3. Mount                → ui.mount(App)        open the window and start the loop
```

```lua
local ui = require("ascii-ui")

-- Optional: global configuration
ui.setup({})

-- Root component: the top of your UI tree
local App = ui.createComponent("App", function()
    return ui.components.Paragraph({ content = "Hello from a Neovim App!" })
end)

-- Mount: opens a floating window and starts the reactive loop
ui.mount(App)
```

---

## How it Runs

When you call `ui.mount(App)`:

1. ascii-ui renders the component tree into an in-memory **Buffer**.
2. A **floating window** (or any custom viewport) is opened and filled with the
   rendered content.
3. The framework listens for **state changes**. When a hook like `useState`
   updates, the tree is re-rendered and the window is refreshed automatically.
4. Built-in keybindings (`h`, `j`, `k`, `l`) let the cursor move between
   interactive segments (buttons, inputs, selects).
5. When the window is closed, all effects are cleaned up and the event loop stops.

---

## Running from the Terminal

You can run a Neovim App headlessly from the terminal without a full editor UI:

```bash
# Run inside a Neovim floating window
nvim -c "luafile my_app.lua"

# Run with stdout rendering (no window, outputs ANSI to terminal)
nvim --headless -c "luafile my_app.lua"
```

For stdout rendering, use `ui.viewports.StdoutViewport`:

```lua
local ui = require("ascii-ui")
local StdoutViewport = ui.viewports.StdoutViewport

local App = ui.createComponent("App", function()
    return ui.components.Paragraph({ content = "Hello from stdout!" })
end)

ui.mount(App, StdoutViewport.new())
```

---

## Next Steps

- [Get Started](./get_started.md) — install ascii-ui.nvim and build your first app
- [Custom Components](./custom_components.md) — define components, manage props, and compose UIs
- [Hooks](./hooks) — add state, effects, and timers to your components
- [ui.mount](./mount) — learn about viewports and the full mount lifecycle
- [Examples](./examples) — real-world apps including clocks, charts, and maps
