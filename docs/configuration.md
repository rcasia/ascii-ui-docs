---
id: configuration
title: Configuration
sidebar_label: Configuration
sidebar_position: 3
description: Configure ascii-ui.nvim behavior, characters, keymaps, and log level.
tags: [api, configuration]
---

# Configuration

Call `ui.setup` once (typically in your `lazy.nvim` spec or your plugin's setup function) to configure ascii-ui globally. All options are optional — the defaults work out of the box.

```lua
require("ascii-ui").setup({
  log_level = "INFO",
  characters = { ... },
  keymaps    = { ... },
})
```

---

## Reference

### `ui.setup(config?)`

Merges `config` into the global ascii-ui configuration. Returns the `AsciiUI` module so calls can be chained.

```lua
local ui = require("ascii-ui").setup({
  log_level = "WARN",
})
-- ui is the ascii-ui module, ready to use
```

Alternatively, `require("ascii-ui")` is callable directly:

```lua
local ui = require("ascii-ui")({ log_level = "WARN" })
```

#### Config fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `log_level` | `"DEBUG" \| "INFO" \| "WARN" \| "ERROR"` | `"INFO"` | Minimum log severity written to the ascii-ui log file. Set to `"DEBUG"` for verbose output during development, `"ERROR"` in production to suppress noise. |
| `characters` | `table` | See below | Box-drawing and UI characters used by built-in components. |
| `keymaps` | `table` | See below | Keys for quitting and selecting. |

---

## Default characters

These characters are used by `Box`, `Slider`, `Tree`, and other components that draw UI primitives:

| Key | Default | Used by |
|-----|---------|---------|
| `top_left` | `╭` | `Box` border |
| `top_right` | `╮` | `Box` border |
| `bottom_left` | `╰` | `Box` border, `Tree` leaf prefix |
| `bottom_right` | `╯` | `Box` border |
| `horizontal` | `─` | `Box` border, `Slider` track |
| `vertical` | `│` | `Box` border, `Tree` continuation |
| `left_tree` | `├` | `Tree` non-last child prefix |
| `thumb` | `●` | `Slider` thumb |
| `whitespace` | ` ` | Spacing between tree elements |
| `right_triangule` | `▸` | `Tree` collapsed node indicator |
| `down_triangule` | `▾` | `Tree` expanded node indicator |

---

## Default keymaps

| Key | Default | Action |
|-----|---------|--------|
| `quit` | `q` | Close the ascii-ui floating window |
| `select` | `<CR>` | Trigger the SELECT interaction on the focused element |

---

## Examples

### Minimal setup (defaults)

When installed with `lazy.nvim`, passing `opts = {}` calls `setup({})` automatically:

```lua
return {
  "rcasia/ascii-ui.nvim",
  opts = {},
}
```

### Custom characters

Replace the default box-drawing characters with ASCII-safe alternatives (useful in environments that do not support Unicode):

```lua
require("ascii-ui").setup({
  characters = {
    top_left     = "+",
    top_right    = "+",
    bottom_left  = "+",
    bottom_right = "+",
    horizontal   = "-",
    vertical     = "|",
    left_tree    = "+",
    thumb        = "o",
    whitespace   = " ",
    right_triangule = ">",
    down_triangule  = "v",
  },
})
```

### Custom keymaps

Change the key used to close the window:

```lua
require("ascii-ui").setup({
  keymaps = {
    quit   = "<Esc>",
    select = "<CR>",
  },
})
```

### Silent logging

Suppress all logs except errors:

```lua
require("ascii-ui").setup({
  log_level = "ERROR",
})
```

---

## Reading config in components

Use the [`useConfig`](./hooks/use_user_config.md) hook inside a component to read the current user configuration:

```lua
local ui = require("ascii-ui")
local useConfig = ui.hooks.useConfig

local MyComponent = ui.createComponent("MyComponent", function()
  local config = useConfig()
  -- config.characters.horizontal, config.keymaps.quit, etc.
  ...
end)
```
