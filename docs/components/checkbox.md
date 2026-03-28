---
id: checkbox
title: Checkbox
sidebar_label: Checkbox
sidebar_position: 5
description: A display-only checkbox component showing a checked or unchecked state.
tags: [components]
---

# Checkbox

`Checkbox` renders a checkbox indicator with an optional label. It is a **display-only** component — it renders the state you pass in but does not manage its own checked/unchecked state. Combine it with `useState` in a parent component to make it interactive.

```lua
ui.components.Checkbox({ active = true, label = "Enable feature" })
```

---

## Reference

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `active` | `boolean` | No | Whether the checkbox appears checked (`[x]`) or unchecked (`[ ]`). Defaults to `false`. |
| `label` | `string` | No | The text displayed next to the checkbox indicator. Defaults to an empty string. |

---

## Usage

### Static checkbox

```lua
local ui = require("ascii-ui")
local Checkbox = require("ascii-ui.components.checkbox")

local MyApp = ui.createComponent("MyApp", function()
  return {
    Checkbox({ active = true,  label = "Auto-save enabled" }),
    Checkbox({ active = false, label = "Dark mode" }),
  }
end)

ui.mount(MyApp)
```

This renders:

```
[x] Auto-save enabled
[ ] Dark mode
```

### Interactive checkbox with state

Because `Checkbox` is a controlled component, manage its state in the parent and toggle it with a `Button`:

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local Button = ui.components.Button
local Checkbox = require("ascii-ui.components.checkbox")

local ToggleFeature = ui.createComponent("ToggleFeature", function()
  local enabled, setEnabled = useState(false)

  return {
    Checkbox({ active = enabled, label = "Feature flag" }),
    Button({
      label = "Toggle",
      on_press = function()
        setEnabled(not enabled)
      end,
    }),
  }
end)

ui.mount(ToggleFeature)
```

---

## Notes

- `Checkbox` is not part of the default `ui.components` public API. Import it directly with `require("ascii-ui.components.checkbox")`.
- The component does not handle focus or keyboard interaction on its own. Pair it with a `Button` or another interactive element to toggle the state.
