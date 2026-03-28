---
id: input
title: Input
sidebar_label: Input
sidebar_position: 6
description: A text input field that enables direct buffer editing when focused.
tags: [components, interactive]
---

# Input

`Input` renders a focusable text field. When the cursor lands on an `Input` segment, the buffer switches to editable mode so the user can type directly into it.

```lua
require("ascii-ui.components.input")({ value = "default text" })
```

---

## Reference

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `value` | `string` | No | The initial text shown in the input field. Defaults to an empty string. |

---

## Usage

### Basic input

```lua
local ui = require("ascii-ui")
local Input = require("ascii-ui.components.input")

local Form = ui.createComponent("Form", function()
  return {
    Input({ value = "Type here..." }),
  }
end)

ui.mount(Form)
```

Move the cursor onto the input text using `h`/`j`/`k`/`l`. The buffer will become editable, allowing you to type or modify the content with standard Neovim editing commands.

---

## Notes

- `Input` is not part of the default `ui.components` public API. Import it with `require("ascii-ui.components.input")`.
- The component uses the `INPUT` interaction type internally. When the cursor moves onto an `Input` segment, the Neovim buffer's `modifiable` option is enabled; moving off it disables it again.
- `Input` does not call any callback when the text changes. To react to input changes, read the buffer contents using Neovim API after the user interacts.
- This component is most useful for simple single-line text fields where the full Neovim editing experience is desired.
