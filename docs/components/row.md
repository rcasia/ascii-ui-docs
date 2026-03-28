---
id: row
title: Row
sidebar_label: Row
sidebar_position: 9
description: A horizontal layout container that arranges components side by side.
tags: [components, layout]
---

# Row

`Row` is a layout component that places two or more components side by side horizontally. It merges their rendered lines column by column, aligning shorter components with blank lines to match the height of the tallest one.

```lua
ui.layout.Row(
  ComponentA(...),
  ComponentB(...)
)
```

---

## Reference

### Signature

```lua
ui.layout.Row(...components) -> FiberNode
```

`Row` accepts any number of component fiber nodes as arguments and returns a single fiber node representing the horizontal layout.

---

## Usage

### Two components side by side

```lua
local ui = require("ascii-ui")
local Row = ui.layout.Row
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button

local Layout = ui.createComponent("Layout", function()
  return {
    Row(
      Paragraph({ content = "Left column\nSecond line" }),
      Paragraph({ content = "Right column" })
    ),
  }
end)

ui.mount(Layout)
```

This renders:

```
Left column  Right column
Second line
```

### Dashboard with boxes

```lua
local ui = require("ascii-ui")
local Row = ui.layout.Row
local Box = require("ascii-ui.components.box")

local Dashboard = ui.createComponent("Dashboard", function()
  return {
    Row(
      Box({ width = 14, height = 3, content = "CPU 42%" }),
      Box({ width = 14, height = 3, content = "MEM 68%" }),
      Box({ width = 14, height = 3, content = "DISK 55%" })
    ),
  }
end)

ui.mount(Dashboard)
```

### Three columns

`Row` accepts any number of arguments:

```lua
Row(
  Paragraph({ content = "Column 1" }),
  Paragraph({ content = "Column 2" }),
  Paragraph({ content = "Column 3" })
)
```

---

## Notes

- `Row` is available as `ui.layout.Row` (accessible via `require("ascii-ui").layout.Row`).
- Columns are separated by a single space. The left column is padded to its maximum line width before the next column begins.
- If columns have different heights, shorter columns are padded with blank lines to match the tallest.
