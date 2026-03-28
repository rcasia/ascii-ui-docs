---
id: box
title: Box
sidebar_label: Box
sidebar_position: 8
description: A bordered box container with centered text content.
tags: [components]
---

# Box

`Box` renders a rectangular bordered container with text centered inside it. The border uses the box-drawing characters from the user configuration (`╭─╮`, `│`, `╰─╯` by default).

```lua
require("ascii-ui.components.box")({ width = 20, height = 5, content = "Hello" })
```

---

## Reference

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `width` | `integer` | No | The total width of the box in characters, including the borders. Defaults to `15`. |
| `height` | `integer` | No | The total height of the box in lines, including the borders. Minimum `3`. Defaults to `3`. |
| `content` | `string` | No | The text to display centered inside the box. Defaults to an empty string. |

---

## Usage

### Basic box

```lua
local ui = require("ascii-ui")
local Box = require("ascii-ui.components.box")

local MyApp = ui.createComponent("MyApp", function()
  return {
    Box({ width = 20, height = 5, content = "ascii-ui" }),
  }
end)

ui.mount(MyApp)
```

This renders:

```
╭──────────────────╮
│                  │
│    ascii-ui      │
│                  │
╰──────────────────╯
```

### Multiple boxes side by side

Use `ui.layout.Row` to place boxes next to each other:

```lua
local ui = require("ascii-ui")
local Box = require("ascii-ui.components.box")
local Row = ui.layout.Row

local Dashboard = ui.createComponent("Dashboard", function()
  return {
    Row(
      Box({ width = 14, height = 3, content = "CPU 42%" }),
      Box({ width = 14, height = 3, content = "MEM 68%" })
    ),
  }
end)

ui.mount(Dashboard)
```

---

## Notes

- `Box` is not part of the default `ui.components` public API. Import it with `require("ascii-ui.components.box")`.
- The border characters are read from the user configuration. Customize them with `ui.setup({ characters = { top_left = "┌", ... } })`.
- The content text is centered horizontally and vertically within the box. Only plain string content is supported.
