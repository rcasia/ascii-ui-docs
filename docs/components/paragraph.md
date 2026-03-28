---
id: paragraph
title: Paragraph
sidebar_label: Paragraph
sidebar_position: 2
description: Render plain text content with optional multi-line support.
tags: [components]
---

# Paragraph

`Paragraph` renders a block of text. It supports multi-line content by splitting on newline characters (`\n`), producing one rendered line per text line.

```lua
ui.components.Paragraph({ content = "Hello, world!" })
```

---

## Reference

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `content` | `string` | No | The text to display. Use `\n` to produce multiple lines. Defaults to an empty string. |

---

## Usage

### Single line

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph

local MyApp = ui.createComponent("MyApp", function()
  return {
    Paragraph({ content = "Hello from ascii-ui!" }),
  }
end)

ui.mount(MyApp)
```

### Multi-line content

Embed `\n` in the `content` string to render multiple lines:

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph

local Info = ui.createComponent("Info", function()
  return {
    Paragraph({
      content = "Line one\nLine two\nLine three",
    }),
  }
end)

ui.mount(Info)
```

This produces:

```
Line one
Line two
Line three
```

### Dynamic content

Use `Paragraph` together with `useState` to display reactive text:

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button

local Status = ui.createComponent("Status", function()
  local message, setMessage = useState("Idle")

  return {
    Paragraph({ content = "Status: " .. message }),
    Button({
      label = "Activate",
      on_press = function()
        setMessage("Active!")
      end,
    }),
  }
end)

ui.mount(Status)
```
