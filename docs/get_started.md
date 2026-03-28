---
sidebar_position: 1
id: get-started
title: Get Started
sidebar_label: Get Started
description: Install ascii-ui.nvim and build your first ASCII UI component.
tags: [getting-started, setup, installation, hello-world]
---

# 🚀 Get Started

Welcome to **ascii-ui.nvim** — a Lua framework for building **reactive ASCII
user interfaces** directly inside Neovim buffers.

These docs are still a work in progress, but you can already start creating components today 💡

---

## 📋 Requirements

- Neovim 0.11+

## 📦 Installation

Install **ascii-ui.nvim** with your favorite plugin manager.  
Here’s an example using **lazy.nvim**:

```lua
return {
  "rcasia/ascii-ui.nvim",
  opts = {},
}
```

## 🧱 Create Your First Neovim App

Let’s create a simple **Neovim app** that renders a message inside a buffer.

1. Open Neovim and create a new file for your app:

   ```bash
   nvim hello_world.lua
   ```

2. Add the following code:

    ```lua
    -- hello_world.lua
    local ui = require("ascii-ui")

    -- Define your root component
    local HelloWorld = ui.createComponent("HelloWorld", function()
      return ui.components.Paragraph({ content = "Hello, ascii-ui! 👋" })
    end)

    -- Mount the app into a new buffer
    ui.mount(HelloWorld)
    ```

3. Save the file (:w), exit Neovim (:q), and run the script:

    ```bash
    nvim -c "luafile hello_world.lua"
    ```

You’ll see “Hello, ascii-ui! 👋” rendered directly inside Neovim ✨

This is your first ascii-ui Neovim app — from here, you can add components, manage state, and build interactive UIs directly inside Neovim 🚀

---

## 🚀 Next Steps

Now that you've built your first app, explore the rest of the framework:

- [Custom Components](./custom_components) — learn how to define and compose your own components
- [Components](./components/button.md) — browse all built-in components (Button, Paragraph, Select, Slider, and more)
- [Hooks](./hooks) — manage state, side effects, timers, and configuration
- [ui.mount](./mount) — learn about viewports and the full mount lifecycle
- [Configuration](./configuration) — customize characters, keymaps, and log level

