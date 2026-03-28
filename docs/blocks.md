---
id: blocks
title: Blocks (Segment / Bufferline)
sidebar_label: Blocks
sidebar_position: 7
description: Low-level building blocks for constructing custom rendered output.
tags: [api, advanced]
---

# Blocks

`ui.blocks` exposes the low-level primitives used internally by all components. Use them directly when you need fine-grained control over rendering, colors, or interactions that built-in components do not expose.

```lua
local Segment    = ui.blocks.Segment
local Bufferline = ui.blocks.Bufferline
```

---

## Segment

A `Segment` is the smallest renderable unit — a styled string that cannot contain newlines. Every piece of text displayed by ascii-ui ultimately comes from one or more segments.

### `ui.blocks.Segment(opts)`

```lua
local seg = ui.blocks.Segment({
  content      = "Hello",         -- required string (no newlines)
  highlight    = "MyHighlight",   -- optional Neovim highlight group
  color        = { fg = "#ff0000", bg = "#001122" },  -- optional hex colors
  is_focusable = true,            -- optional, make the segment focusable
  interactions = {                -- optional interaction callbacks
    SELECT = function() print("selected") end,
  },
})
```

#### Options

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `content` | `string` | Yes | The text to render. Must not contain newlines. |
| `highlight` | `string` | No | A Neovim highlight group name applied to this segment when rendered in a floating window. |
| `color` | `{ fg?: string, bg?: string }` | No | Hex color strings for foreground and/or background (e.g. `"#ff0000"`). Used by the `StdoutViewport` for ANSI colors and by the floating window via extmarks. |
| `is_focusable` | `boolean` | No | Whether the cursor can land on this segment. Automatically `true` when the segment has any interactions. |
| `interactions` | `table` | No | A table mapping interaction type keys to callbacks. See interaction types below. |

#### Interaction types

| Key | Triggered by |
|-----|-------------|
| `SELECT` | `<CR>` on the focused segment |
| `on_cursor_move_right` | `l` key |
| `on_cursor_move_left` | `h` key |
| `cursor_move_down` | `j` key |
| `cursor_move_up` | `k` key |
| `ON_INPUT` | Enables buffer editing when focused (used by `Input`) |

---

## Bufferline

A `Bufferline` is a single horizontal line composed of one or more segments.

### `ui.blocks.Bufferline(...segments)`

```lua
local line = ui.blocks.Bufferline(
  ui.blocks.Segment({ content = "Name: " }),
  ui.blocks.Segment({ content = "Alice", color = { fg = "#00ff00" } })
)
```

Pass any number of `Segment` objects as arguments. The segments are rendered left to right on the same line.

---

## Usage

### Custom colored line

```lua
local ui = require("ascii-ui")
local Segment    = ui.blocks.Segment
local Bufferline = ui.blocks.Bufferline

local ColorBanner = ui.createComponent("ColorBanner", function()
  return {
    Bufferline(
      Segment({ content = "Status: " }),
      Segment({ content = "OK",    color = { fg = "#00ff00" } }),
      Segment({ content = " | " }),
      Segment({ content = "ERROR", color = { fg = "#ff0000" } })
    ),
  }
end)

ui.mount(ColorBanner)
```

### Interactive custom segment

Create a segment with a custom `SELECT` interaction:

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local Segment    = ui.blocks.Segment
local Bufferline = ui.blocks.Bufferline

local ClickableText = ui.createComponent("ClickableText", function()
  local clicked, setClicked = useState(false)

  return {
    Bufferline(
      Segment({
        content = clicked and "[clicked!]" or "[click me]",
        interactions = {
          SELECT = function() setClicked(true) end,
        },
      })
    ),
  }
end)

ui.mount(ClickableText)
```

### Analog clock hands (advanced)

The `examples/analog-clock.lua` in the plugin repository is a good example of building complex layouts using only `Segment` and `Bufferline` with per-cell color values.

---

## Notes

- `Segment` and `Bufferline` are the building blocks that all built-in components (`Button`, `Paragraph`, etc.) use internally.
- A component's render function must return a table of `Bufferline` objects (or a nested table that resolves to one).
- `Segment:wrap()` is a convenience method that wraps a single segment into a `Bufferline`.
