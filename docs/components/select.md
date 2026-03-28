---
id: select
title: Select
sidebar_label: Select
sidebar_position: 3
description: A list of selectable options rendered as a focusable menu.
tags: [components, interactive]
---

# Select

`Select` renders a vertical list of options. The currently selected item is highlighted, and pressing `<CR>` on any option selects it and calls the `on_select` callback.

```lua
ui.components.Select({
  options = { "Option A", "Option B", "Option C" },
  on_select = function(selected) print(selected) end,
})
```

---

## Reference

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `options` | `string[]` | Yes | The list of option labels to display. |
| `title` | `string` | No | An optional title rendered above the list. |
| `on_select` | `fun(selected: string)` | No | Callback invoked with the selected option's label when the user presses `<CR>`. |

---

## Usage

### Basic select

```lua
local ui = require("ascii-ui")
local Select = ui.components.Select

local Menu = ui.createComponent("Menu", function()
  return {
    Select({
      title = "Choose a language",
      options = { "Lua", "Python", "JavaScript" },
      on_select = function(selected)
        print("Selected: " .. selected)
      end,
    }),
  }
end)

ui.mount(Menu)
```

This renders:

```
Choose a language
[x] Lua
[ ] Python
[ ] JavaScript
```

### Reacting to selection

Use `useState` to store the selection and display it elsewhere in your UI:

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local Select = ui.components.Select
local Paragraph = ui.components.Paragraph

local LanguagePicker = ui.createComponent("LanguagePicker", function()
  local selected, setSelected = useState("none")

  return {
    Select({
      title = "Pick a language",
      options = { "Lua", "Python", "Go" },
      on_select = setSelected,
    }),
    Paragraph({ content = "You picked: " .. selected }),
  }
end)

ui.mount(LanguagePicker)
```

---

## Keyboard interaction

| Key | Action |
|-----|--------|
| `j` / `k` | Move focus down / up through the options |
| `<CR>` | Select the focused option and call `on_select` |

---

## Notes

- The selected item is displayed as `[x] label` with the `AsciiUISelection` highlight (gold text).
- Unselected items are displayed as `[ ] label`.
- The selection state is managed internally; the first option is selected by default.
