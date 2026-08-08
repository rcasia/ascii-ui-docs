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
  color        = { fg = "#ff0000", bg = "#001122" },  -- optional hex colors (table or string)
  is_focusable = true,            -- optional, make the segment focusable
  interactions = {                -- optional interaction callbacks
    HOVER = function() print("hovered") end,
  },
})
```

#### Options

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `content` | `string` | Yes | The text to render. Must not contain newlines. |
| `highlight` | `string` | No | A Neovim highlight group name applied to this segment when rendered in a floating window. |
| `color` | `{ fg?: string, bg?: string }` or `string` | No | Hex color for foreground and/or background. Accepts a table `{ fg = "#ff0000", bg = "#001122" }` or a string shorthand `"#ff0000"` (foreground only). Used by the `StdoutViewport` for ANSI colors and by the floating window via extmarks. |
| `is_focusable` | `boolean` | No | Whether the cursor can land on this segment. Automatically `true` when the segment has any interactions. |
| `interactions` | `table` | No | A table mapping interaction type keys to callbacks. See interaction types below. |

#### Interaction types

All interaction type keys use `UPPER_SNAKE_CASE` and are available as constants from `require("ascii-ui.interaction_type")`.

| Key | Constant | Triggered by |
|-----|----------|-------------|
| `"HOVER"` | `interaction_type.HOVER` | Cursor moves onto the segment |
| `"CURSOR_MOVE_RIGHT"` | `interaction_type.CURSOR_MOVE_RIGHT` | `l` key |
| `"CURSOR_MOVE_LEFT"` | `interaction_type.CURSOR_MOVE_LEFT` | `h` key |
| `"CURSOR_MOVE_DOWN"` | `interaction_type.CURSOR_MOVE_DOWN` | `j` key |
| `"CURSOR_MOVE_UP"` | `interaction_type.CURSOR_MOVE_UP` | `k` key |
| `"INPUT"` | `interaction_type.INPUT` | Enables buffer editing when focused (used by `Input`) |

Using the constants is recommended over raw strings:

```lua
local interaction_type = require("ascii-ui.interaction_type")

Segment({
  content = "→",
  interactions = {
    [interaction_type.CURSOR_MOVE_RIGHT] = function() ... end,
    [interaction_type.CURSOR_MOVE_LEFT]  = function() ... end,
  },
})
```

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

## Color

The `ui.Color` class provides a unified API for creating and managing colors. It handles both Neovim highlight groups (for floating windows) and ANSI escape sequences (for stdout/terminal output).

### `ui.Color.new(opts)`

Creates a new Color instance. Accepts either a table or a string shorthand:

```lua
local ui = require("ascii-ui")

-- Table form (both fg and bg optional)
local red_on_white = ui.Color.new({ fg = "#ff0000", bg = "#ffffff" })

-- String shorthand (treated as foreground only)
local red = ui.Color.new("#ff0000")

-- Use with segments
local segment = ui.blocks.Segment({ content = "hello", color = red })
-- or with string shorthand:
local segment2 = ui.blocks.Segment({ content = "hello", color = "#ff0000" })
```

#### Color Methods

##### `color:to_hl_group()`

Returns a Neovim highlight group name for this color. Creates the group on first call and caches it for subsequent calls.

```lua
local hl_group = red_on_white:to_hl_group()
-- Returns something like "AsciiUI_fgff0000_bgffffff"
```

##### `color:to_ansi()`

Returns ANSI truecolor escape sequences for terminal output. Produces foreground and/or background codes as appropriate.

```lua
local ansi = red:to_ansi()
-- Returns "\027[38;2;255;0;0m" (foreground red)
```

##### `color:is_empty()`

Returns `true` if neither fg nor bg is set.

```lua
local empty = ui.Color.new({})
assert.is_true(empty:is_empty())
```

##### `ui.Color.is_color(obj)`

Checks if an object is a Color instance.

```lua
assert.is_true(ui.Color.is_color(red))
```

### HSL Color Support

The Color API also supports HSL (Hue, Saturation, Lightness) color manipulation.

#### `ui.Color.from_hsl(h, s, l)`

Creates a Color from HSL values. Hue is 0-360, saturation and lightness are 0-100. Values are clamped to valid ranges.

```lua
-- Create a color from HSL values
local blue = ui.Color.from_hsl(240, 100, 50)

-- Create a pastel red
local pastel_red = ui.Color.from_hsl(0, 50, 75)
```

#### `color:to_hsl()`

Returns the HSL representation of the color's foreground. Returns `nil` for all three values if fg is not set.

```lua
local color = ui.Color.new("#ff0000")
local h, s, l = color:to_hsl()
-- h = 0, s = 100, l = 50
```

#### Color Manipulation Methods

All manipulation methods return new Color instances (immutable). Values are clamped to valid ranges.

##### `color:lighten(amount)`

Returns a new Color with lightness increased by the given amount (0-100).

```lua
local base = ui.Color.new("#ff0000")
local lighter = base:lighten(20)
```

##### `color:darken(amount)`

Returns a new Color with lightness decreased by the given amount (0-100).

```lua
local base = ui.Color.new("#ff0000")
local darker = base:darken(20)
```

##### `color:saturate(amount)`

Returns a new Color with saturation increased by the given amount (0-100).

```lua
local base = ui.Color.new("#ff6666")
local more_saturated = base:saturate(30)
```

##### `color:desaturate(amount)`

Returns a new Color with saturation decreased by the given amount (0-100).

```lua
local base = ui.Color.new("#ff0000")
local muted = base:desaturate(50)
```

##### `color:complement()`

Returns the complement color (hue rotated 180 degrees).

```lua
local red = ui.Color.new("#ff0000")
local cyan = red:complement()
```

### Color Caching

Colors are cached by their fg/bg combination. Creating a Color with the same values returns the same instance:

```lua
local c1 = ui.Color.new("#ff0000")
local c2 = ui.Color.new("#ff0000")
assert.are.same(c1, c2)  -- same instance
```

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

Create a segment that reacts to cursor movement:

```lua
local ui = require("ascii-ui")
local interaction_type = require("ascii-ui.interaction_type")
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
          [interaction_type.CURSOR_MOVE_RIGHT] = function() setClicked(true) end,
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
