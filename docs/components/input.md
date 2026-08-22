---
id: input
title: Input
sidebar_label: Input
sidebar_position: 6
description: A focusable text field that switches the buffer to insert mode and reports changes via callbacks.
tags: [components, interactive]
---

# Input

`Input` renders a focusable single-line text field. When the cursor lands on an `Input` segment, pressing `i` switches the buffer into insert mode so the user can type. Input can be **controlled** (its value is owned by a parent via `value`) or **uncontrolled** (it owns its value, seeded with `initial_value`), and it reports activity through `on_change`, `on_submit`, and `on_blur` callbacks.

```lua
ui.components.Input({ value = name, on_change = setName })
```

---

## Reference

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `value` | `string` | No | Controlled value. When set, the component syncs its internal state to this value on every render. |
| `initial_value` | `string` | No | Seed value for an uncontrolled input. Ignored when `value` is provided. |
| `placeholder` | `string` | No | Text shown when the field is empty and unfocused. |
| `on_change` | `fun(value: string)` | No | Fires on every text change while editing. |
| `on_submit` | `fun(value: string)` | No | Fires when the user presses `<CR>` in insert mode. |
| `on_blur` | `fun(value: string)` | No | Fires when insert mode is left (the field loses focus). |
| `password` | `boolean` | No | When `true`, the displayed text is masked with `*`. |

If neither `value` nor `initial_value` is given, the field starts empty.

---

## Usage

### Controlled input

Manage the text in a parent with `useState` and pass it back in as `value`. Because the component is controlled, it reflects the parent's state on every render.

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local Paragraph = ui.components.Paragraph
local Input = ui.components.Input

local Form = ui.createComponent("Form", function()
  local name, setName = useState("")

  return {
    Paragraph({ content = "Hello, " .. name }),
    Input({
      value = name,
      on_change = setName,
      placeholder = "Enter your name",
    }),
  }
end)

ui.mount(Form)
```

### Uncontrolled input

Seed the field with `initial_value` and let it manage its own state. Read the value through a callback.

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph
local Input = ui.components.Input

local Search = ui.createComponent("Search", function()
  return {
    Input({
      initial_value = "query",
      placeholder = "Type to search...",
      on_submit = function(query)
        vim.notify("Searching: " .. query, vim.log.levels.INFO)
      end,
    }),
  }
end)

ui.mount(Search)
```

### Password input

Set `password = true` to mask the displayed text with `*`. The real value is still passed to your callbacks.

```lua
local ui = require("ascii-ui")
local Input = ui.components.Input

local Login = ui.createComponent("Login", function()
  return {
    Input({
      password = true,
      placeholder = "Enter password",
      on_blur = function(secret)
        -- `secret` holds the real value, not the masked display
      end,
    }),
  }
end)

ui.mount(Login)
```

---

## Callbacks

| Callback | Fires when |
|----------|------------|
| `on_change` | The text changes while in insert mode (`TextChangedI`). |
| `on_submit` | The user presses `<CR>` in insert mode. Insert mode then exits. |
| `on_blur` | Insert mode is left for any reason (the field loses focus). |

Each callback receives the current line content as a `string`.

---

## Keyboard interaction

| Key | Action |
|-----|--------|
| `h` / `j` / `k` / `l` | Move focus between focusable segments |
| `i` | Enter insert mode on the focused `Input` |
| `<CR>` | Submit the input and exit insert mode |
| `<Esc>` | Leave insert mode |

Move the cursor onto an `Input` segment with the focus-navigation keys, then press `i` to begin editing.

---

## Notes

- `Input` is part of the `ui.components` public API. Import it with `ui.components.Input` or `require("ascii-ui.components.input")`.
- A controlled input (`value`) is the recommended pattern: it keeps the rendered text in sync with your component's state.
- For an uncontrolled input, use `initial_value`. The component then owns the value, and you observe it through the callbacks.
- `placeholder` is only shown while the field is empty and unfocused.
- With `password = true`, the field displays `*` characters but your callbacks still receive the real value.
- The component uses the `INPUT` interaction type internally. Pressing `i` on a focused `Input` enables insert mode; leaving insert mode disables it again.
- Focus can move between multiple `Input` segments with the standard focus-navigation keys, allowing form-style layouts.
