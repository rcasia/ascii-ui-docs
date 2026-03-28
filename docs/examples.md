---
sidebar_position: 7
id: examples
title: Examples & Recipes
sidebar_label: Examples
description: Real-world ascii-ui.nvim examples covering state, timers, lists, animations, conditional rendering, and low-level buffer output.
tags: [examples, recipes, patterns, useState, useInterval, useReducer, animation, list, map]
---

# Examples & Recipes

The following examples cover the most common patterns in ascii-ui.nvim. All
source files are available in the
[`examples/` directory](https://github.com/rcasia/ascii-ui.nvim/tree/main/examples)
of the ascii-ui.nvim repository.

---

## Basic State — Button That Changes Text

The simplest interactive app: a `Paragraph` whose content is controlled by
state, updated on button press.

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button
local useState = ui.hooks.useState

local App = ui.createComponent("App", function()
    local content, setContent = useState("initial content")

    return {
        Paragraph({ content = content }),
        Button({
            label = "change",
            on_press = function()
                setContent("changed content")
            end,
        }),
    }
end)

ui.mount(App)
```

**Key patterns:** `useState`, button `on_press` callback, simple state update.

---

## Conditional Rendering

Return different nodes based on state. `nil` entries are silently ignored by
the framework, so you can use plain Lua `if` expressions.

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button
local useState = ui.hooks.useState

local App = ui.createComponent("App", function()
    local shouldShow, setShouldShow = useState(true)

    local message = shouldShow
        and Paragraph({ content = "Visible!" })
        or Paragraph({ content = "Hidden." })

    return {
        message,
        Button({
            label = "Toggle",
            on_press = function()
                setShouldShow(not shouldShow)
            end,
        }),
    }
end)

ui.mount(App)
```

**Key patterns:** conditional rendering, toggling boolean state.

---

## Rendering Lists with `ui.map`

Use `ui.map` to render a dynamic collection of items. `useReducer` is a good
fit when items can be added or modified through distinct action types.

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button
local useReducer = ui.hooks.useReducer

local App = ui.createComponent("App", function()
    local items, dispatch = useReducer(function(state, action)
        if action.type == "add" then
            return vim.list_extend(vim.deepcopy(state), { "Item " .. #state + 1 })
        end
        return state
    end, { "Item 1", "Item 2" })

    return {
        Paragraph({ content = "Total: " .. #items }),

        ui.map(items, function(item)
            return Paragraph({ content = "• " .. item })
        end),

        Button({
            label = "Add item",
            on_press = function() dispatch({ type = "add" }) end,
        }),
    }
end)

ui.mount(App)
```

**Key patterns:** `useReducer`, `ui.map`, nested array flattening.

---

## Timer — Live Clock with `useInterval`

`useInterval` fires a callback every N milliseconds. Combine it with
`useState` to build animations or live-updating displays.

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph
local useState = ui.hooks.useState
local useInterval = ui.hooks.useInterval

local Clock = ui.createComponent("Clock", function()
    local time, setTime = useState(os.date("%H:%M:%S"))

    useInterval(function()
        setTime(os.date("%H:%M:%S"))
    end, 1000)

    return Paragraph({ content = "Current time: " .. time })
end)

ui.mount(Clock)
```

**Key patterns:** `useInterval`, live state updates.

---

## Scrolling Text (Train Station Board)

A marquee-style component that scrolls text horizontally. Demonstrates using a
function in the `useState` setter to derive the next value from the previous one.

```lua
local Segment = require("ascii-ui.buffer.segment")
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local useInterval = ui.hooks.useInterval

local function ScrollingText(props)
    local text   = props.text
    local width  = props.width  or 50
    local speed  = props.speed  or 100

    local total_cycle = width + #text
    local offset, setOffset = useState(#text)

    useInterval(function()
        setOffset(function(current)
            return (current + 1) % total_cycle
        end)
    end, speed)

    -- calculate the visible slice
    local content
    if offset == 0 then
        content = (" "):rep(width)
    elseif offset < #text then
        content = (" "):rep(width - offset) .. text:sub(1, offset)
    else
        local start = offset - #text + 1
        local visible = text:sub(start, math.min(start + width - 1, #text))
        content = visible .. (" "):rep(width - #visible)
    end

    return { Segment:new({ content = content }):wrap() }
end

local ScrollingTextComponent = ui.createComponent("ScrollingText", ScrollingText, {
    text  = "string",
    width = "number",
    speed = "number",
})

local App = ui.createComponent("App", function()
    return {
        ui.components.Paragraph({ content = "=== Station Board ===" }),
        ScrollingTextComponent({ text = "Next train: Central Station — 5 min", width = 50, speed = 150 }),
    }
end)

ui.mount(App)
```

**Key patterns:** functional setter form `setOffset(fn)`, low-level `Segment`,
reusable component with prop types.

---

## File Tree with the Tree Component

Use the built-in `Tree` component to display a collapsible file/folder tree.

```lua
local Tree = require("ascii-ui.components.tree")
local ui = require("ascii-ui")

local tree = {
    text = "./",
    children = {
        {
            text = "src",
            expanded = false,
            children = { { text = "main.lua" } },
        },
        { text = ".gitignore" },
        { text = "README.md" },
    },
}

local App = ui.createComponent("App", function()
    return Tree({ tree = tree })
end)

ui.mount(App)
```

**Key patterns:** `Tree` component, nested data, `expanded` flag.

---

## StdoutViewport — Bouncing Ball Animation

Render to terminal stdout instead of a Neovim window. Useful for headless
scripts, CI pipelines, or terminal-only demos.

```lua
local Bufferline = require("ascii-ui.buffer.bufferline")
local Segment    = require("ascii-ui.buffer.segment")
local ui         = require("ascii-ui")
local useState   = ui.hooks.useState
local useInterval = ui.hooks.useInterval
local StdoutViewport = ui.viewports.StdoutViewport

local WIDTH, HEIGHT = 40, 16

local App = ui.createComponent("App", function()
    local x, setX = useState(1)
    local y, setY = useState(1)
    local dx, setDx = useState(1)
    local dy, setDy = useState(1)

    useInterval(function()
        setX(function(cx)
            local nx = cx + dx
            if nx < 1 then nx = 1; setDx(1)
            elseif nx > WIDTH then nx = WIDTH; setDx(-1) end
            return nx
        end)
        setY(function(cy)
            local ny = cy + dy
            if ny < 1 then ny = 1; setDy(1)
            elseif ny > HEIGHT then ny = HEIGHT; setDy(-1) end
            return ny
        end)
    end, 50)

    local border = "+" .. ("-"):rep(WIDTH) .. "+"
    local lines  = { Bufferline.new(Segment:new({ content = border })) }

    for row = 1, HEIGHT do
        local row_str = ""
        for col = 1, WIDTH do
            row_str = row_str .. (row == y and col == x and "O" or " ")
        end
        lines[#lines + 1] = Bufferline.new(Segment:new({ content = "|" .. row_str .. "|" }))
    end

    lines[#lines + 1] = Bufferline.new(Segment:new({ content = border }))
    return lines
end)

ui.mount(App, StdoutViewport.new())
```

**Key patterns:** `StdoutViewport`, raw `BufferLine`/`Segment` output,
`useInterval`, functional state setters.

---

## Colored Output with StdoutViewport

Segments accept a `color` table with `fg` and `bg` hex color strings. This
works both in Neovim windows (via extmarks) and in stdout (via ANSI truecolor).

```lua
local Bufferline = require("ascii-ui.buffer.bufferline")
local Segment    = require("ascii-ui.buffer.segment")
local ui         = require("ascii-ui")
local StdoutViewport = ui.viewports.StdoutViewport

local App = ui.createComponent("App", function()
    return {
        Bufferline.new(
            Segment:new({ content = "Red",   color = { fg = "#ff4466" } }),
            Segment:new({ content = " | " }),
            Segment:new({ content = "Green", color = { fg = "#44ff88" } }),
            Segment:new({ content = " | " }),
            Segment:new({ content = "Blue",  color = { fg = "#00ccff" } })
        ),
    }
end)

ui.mount(App, StdoutViewport.new())
```

**Key patterns:** `Segment` color object `{ fg, bg }`, multi-segment `BufferLine`.

---

## Component Composition — Reusable Sub-Components

Build larger apps by composing small, independently-stateful components.

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph
local Button    = ui.components.Button
local useState  = ui.hooks.useState

-- Reusable child
local Counter = ui.createComponent("Counter", function(props)
    local count, setCount = useState(0)
    return {
        Paragraph({ content = (props.label or "Count") .. ": " .. count }),
        Button({ label = "+1", on_press = function() setCount(count + 1) end }),
    }
end, { label = "string" })

-- Parent composes multiple Counters, each with independent state
local App = ui.createComponent("App", function()
    return {
        Paragraph({ content = "=== Dashboard ===" }),
        Counter({ label = "Commits" }),
        Counter({ label = "Issues" }),
        Counter({ label = "PRs" }),
    }
end)

ui.mount(App)
```

**Key patterns:** `createComponent` with prop types, isolated state per
instance, component composition.

---

## See Also

- [Custom Components](./custom_components.md) — `createComponent`, props, `ui.map`, return rules
- [Hooks](./hooks) — full reference for all six hooks
- [Blocks](./blocks) — `Segment` and `BufferLine` low-level API
- [Viewports — StdoutViewport](./viewports/stdout.md) — stdout rendering details
- [Configuration](./configuration) — customise characters, keymaps, and log level
