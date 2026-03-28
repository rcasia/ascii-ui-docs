---
id: slider
title: Slider
sidebar_label: Slider
sidebar_position: 4
description: A horizontal slider component with keyboard-driven value control.
tags: [components, interactive]
---

# Slider

`Slider` renders a horizontal slider that the user can move left or right using the `h` and `l` keys. The value ranges from `0` to `100` in steps of `10`.

```lua
ui.components.Slider({ title = "Volume", value = 50, on_change = function(v) ... end })
```

---

## Reference

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `title` | `string` | No | A label rendered above the slider track. Defaults to no title. |
| `value` | `integer` | No | The initial value of the slider (0–100). Defaults to `0`. |
| `on_change` | `fun(value: integer)` | No | Callback invoked with the new value whenever the slider position changes. |

---

## Usage

### Basic slider

```lua
local ui = require("ascii-ui")
local Slider = ui.components.Slider

local VolumeControl = ui.createComponent("VolumeControl", function()
  return {
    Slider({
      title = "Volume",
      value = 40,
      on_change = function(v)
        print("Volume: " .. v .. "%")
      end,
    }),
  }
end)

ui.mount(VolumeControl)
```

This renders something like:

```
Volume
────●────── 40%
```

### Storing slider value in state

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local Slider = ui.components.Slider
local Paragraph = ui.components.Paragraph

local Settings = ui.createComponent("Settings", function()
  local brightness, setBrightness = useState(70)

  return {
    Slider({
      title = "Brightness",
      value = brightness,
      on_change = setBrightness,
    }),
    Paragraph({ content = "Current: " .. brightness .. "%" }),
  }
end)

ui.mount(Settings)
```

---

## Keyboard interaction

Focus the slider thumb (the `●` character), then:

| Key | Action |
|-----|--------|
| `l` | Increase the value by 10 |
| `h` | Decrease the value by 10 |

---

## Notes

- The slider range is always `0`–`100` in increments of `10`.
- The track characters (`─`) and thumb (`●`) are read from the user configuration and can be changed via `ui.setup({ characters = { ... } })`.
- The `on_change` callback fires on every value change, including the initial render.
