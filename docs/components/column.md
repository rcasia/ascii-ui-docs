---
id: column
title: Column
sidebar_label: Column
sidebar_position: 10
description: A vertical layout container that stacks components top to bottom.
tags: [components, layout]
---

# Column

`Column` is a layout component that arranges child components vertically, from top to bottom. Children are separated by a configurable gap.

```lua
local Column = ui.layout.Column
```

---

## Reference

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `children` | `FiberNode[]` | Yes | Child components to arrange vertically. |
| `gap` | `integer` | No | Space between children in lines. Defaults to `0`. |

### Signatures

`Column` supports two calling conventions:

```lua
-- Props table (preferred)
Column({ children = { child1, child2 }, gap = 1 })

-- Varargs
Column(child1, child2, child3)
```

---

## Usage

### Basic layout

```lua
local ui = require("ascii-ui")
local Column = ui.layout.Column
local Paragraph = ui.components.Paragraph

local Layout = ui.createComponent("Layout", function()
  return Column(
    Paragraph({ content = "Line 1" }),
    Paragraph({ content = "Line 2" }),
    Paragraph({ content = "Line 3" })
  )
end)

ui.mount(Layout)
```

Output:

```
Line 1
Line 2
Line 3
```

### With state and interaction

```lua
local ui = require("ascii-ui")
local Column = ui.layout.Column
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button
local useState = ui.hooks.useState

local Counter = ui.createComponent("Counter", function()
  local count, setCount = useState(0)

  return Column(
    Paragraph({ content = "Count: " .. count }),
    Button({
      label = "Increment",
      on_press = function()
        setCount(count + 1)
      end,
    })
  )
end)

ui.mount(Counter)
```

### Custom gap

Use the props table form to set a custom gap between children:

```lua
local Column = ui.layout.Column
local Paragraph = ui.components.Paragraph

Column({
  children = {
    Paragraph({ content = "A" }),
    Paragraph({ content = "B" }),
    Paragraph({ content = "C" }),
  },
  gap = 2,
})
```

Output:

```
A

B

C
```

### Dynamic children

```lua
local ui = require("ascii-ui")
local Column = ui.layout.Column
local Paragraph = ui.components.Paragraph
local useState = ui.hooks.useState

local DynamicColumn = ui.createComponent("DynamicColumn", function()
  local items, setItems = useState({ "alpha" })

  local children = {}
  for _, item in ipairs(items) do
    children[#children + 1] = Paragraph({ content = item })
  end

  return Column({ children = children })
end)
```

---

## Nesting

`Column` and [Row](./row.md) can be nested to create complex layouts:

```lua
local ui = require("ascii-ui")
local Row = ui.layout.Row
local Column = ui.layout.Column
local Paragraph = ui.components.Paragraph

local App = ui.createComponent("App", function()
  return Row(
    Column(
      Paragraph({ content = "A1" }),
      Paragraph({ content = "A2" })
    ),
    Column(
      Paragraph({ content = "B1" }),
      Paragraph({ content = "B2" })
    )
  )
end)
```

Output:

```
A1 B1
A2 B2
```

---

## Notes

- Available as `ui.layout.Column`.
- For horizontal layouts, see [Row](./row.md).
