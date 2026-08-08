---
id: row
title: Row
sidebar_label: Row
sidebar_position: 9
description: A horizontal layout container that arranges components side by side.
tags: [components, layout]
---

# Row

`Row` is a layout component that arranges child components horizontally, from left to right. Children are separated by a configurable gap.

```lua
local Row = ui.layout.Row
```

---

## Reference

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `children` | `FiberNode[]` | Yes | Child components to arrange horizontally. |
| `gap` | `integer` | No | Space between children in characters. Defaults to `1`. |

### Signatures

`Row` supports two calling conventions:

```lua
-- Props table (preferred)
Row({ children = { child1, child2 }, gap = 2 })

-- Varargs (legacy)
Row(child1, child2, child3)
```

---

## Usage

### Basic layout

```lua
local ui = require("ascii-ui")
local Row = ui.layout.Row
local Paragraph = ui.components.Paragraph

local Layout = ui.createComponent("Layout", function()
  return Row(
    Paragraph({ content = "Left" }),
    Paragraph({ content = "Right" })
  )
end)

ui.mount(Layout)
```

Output:

```
Left Right
```

### Dashboard with boxes

```lua
local ui = require("ascii-ui")
local Row = ui.layout.Row
local Box = require("ascii-ui.components.box")

local Dashboard = ui.createComponent("Dashboard", function()
  return Row(
    Box({ width = 14, height = 3, content = "CPU 42%" }),
    Box({ width = 14, height = 3, content = "MEM 68%" }),
    Box({ width = 14, height = 3, content = "DISK 55%" })
  )
end)

ui.mount(Dashboard)
```

### Custom gap

Use the props table form to set a custom gap between children:

```lua
local Row = ui.layout.Row
local Paragraph = ui.components.Paragraph

Row({
  children = {
    Paragraph({ content = "A" }),
    Paragraph({ content = "B" }),
    Paragraph({ content = "C" }),
  },
  gap = 3,
})
```

Output:

```
A   B   C
```

### Dynamic children

```lua
local ui = require("ascii-ui")
local Row = ui.layout.Row
local Paragraph = ui.components.Paragraph
local useState = ui.hooks.useState

local DynamicRow = ui.createComponent("DynamicRow", function()
  local items, setItems = useState({ "X" })

  local children = {}
  for _, item in ipairs(items) do
    children[#children + 1] = Paragraph({ content = item })
  end

  return Row({ children = children })
end)
```

---

## Nesting

`Row` and [Column](./column.md) can be nested to create complex layouts:

```lua
local ui = require("ascii-ui")
local Row = ui.layout.Row
local Column = ui.layout.Column
local Paragraph = ui.components.Paragraph

local App = ui.createComponent("App", function()
  return Column(
    Paragraph({ content = "Header" }),
    Row(
      Paragraph({ content = "Left" }),
      Paragraph({ content = "Right" })
    ),
    Paragraph({ content = "Footer" })
  )
end)
```

---

## Notes

- Available as `ui.layout.Row`.
- Children with different heights are padded with blank lines to match the tallest.
- For vertical layouts, see [Column](./column.md).
