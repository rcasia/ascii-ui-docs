---
sidebar_position: 2
id: custom-components
title: Custom Components
sidebar_label: Custom Components
description: Learn how to define components, declare props, compose smaller components, and render lists with ui.map.
tags: [components, createComponent, props, composition, map]
---

# Custom Components

The core principle of building an application with ascii-ui.nvim is defining
custom components. A custom component is a Lua function that returns a
description of what should be rendered on the screen, allowing you to combine
existing primitives (like Button and Paragraph) and manage local state.

---

## Creating a Component

Use `ui.createComponent` to register a named component:

```lua
-- Signature
ui.createComponent(name, render_fn)
ui.createComponent(name, render_fn, prop_types)
```

| Parameter    | Type     | Description |
|--------------|----------|-------------|
| `name`       | `string` | Unique identifier used internally by the reconciler. |
| `render_fn`  | `function` | The render function — called on every render cycle. Returns a node or a table of nodes. |
| `prop_types` | `table?` | Optional map of prop name → expected Lua type. Used for runtime validation. |

### Simple Counter Example

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local Button = ui.components.Button
local Paragraph = ui.components.Paragraph

local SimpleCounter = ui.createComponent("SimpleCounter", function()
    local count, setCount = useState(0)

    local function increment()
        setCount(count + 1)
    end

    return {
        Paragraph({ content = "Current Count: " .. count }),
        Button({
            label = "Click to Increment",
            on_press = increment,
        }),
    }
end)

ui.mount(SimpleCounter)
```

---

## Declaring Props

Components receive props as a Lua table. Any key in that table is a prop.

```lua
local Greeting = ui.createComponent("Greeting", function(props)
    local name = props.name or "World"
    return ui.components.Paragraph({ content = "Hello, " .. name .. "!" })
end)

-- Usage:
Greeting({ name = "Neovim" })
```

### Runtime Prop Type Validation

Pass a third argument to `createComponent` to declare the expected type of each
prop. ascii-ui will raise an error at render time if the actual type doesn't match.

```lua
local ProgressBar = ui.createComponent("ProgressBar", function(props)
    local pct = props.percent or 0
    local bar = string.rep("█", math.floor(pct / 5))
    return ui.components.Paragraph({ content = ("[%-20s] %d%%"):format(bar, pct) })
end, {
    percent = "number",   -- must be a number if provided
})

-- Correct:
ProgressBar({ percent = 75 })

-- Raises an error at runtime:
-- ProgressBar({ percent = "75" })
```

Supported type strings mirror `type()` return values: `"number"`, `"string"`,
`"boolean"`, `"table"`, `"function"`, `"nil"`.

---

## Returning `nil` and Conditional Rendering

A component may return `nil` for any child node — nil values are filtered out
automatically by the framework, enabling simple conditional rendering:

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local Button = ui.components.Button
local Paragraph = ui.components.Paragraph

local App = ui.createComponent("App", function()
    local shouldShow, setShouldShow = useState(true)

    -- Assign nil when hidden; the framework ignores nil entries
    local message = shouldShow
        and Paragraph({ content = "Visible!" })
        or nil

    return {
        message,
        Button({
            label = "Toggle",
            on_press = function() setShouldShow(not shouldShow) end,
        }),
    }
end)
```

---

## Rendering Lists with `ui.map`

`ui.map` is the idiomatic way to render a dynamic list of items. It maps over
a Lua array and returns a flat array of nodes, which can be returned directly
or embedded inside a larger table.

```lua
-- Signature
ui.map(items, render_fn)
```

| Parameter   | Type       | Description |
|-------------|------------|-------------|
| `items`     | `T[]`      | The array to iterate over. |
| `render_fn` | `fun(item: T, i: integer): U` | Called for each element. Should return a component node. |

### Example — Dynamic List

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button
local useReducer = ui.hooks.useReducer

local TodoList = ui.createComponent("TodoList", function()
    local items, dispatch = useReducer(function(state, action)
        if action.type == "add" then
            return vim.list_extend(vim.deepcopy(state), { "Item " .. (#state + 1) })
        end
        return state
    end, { "Item 1", "Item 2" })

    return {
        Paragraph({ content = "Items: " .. #items }),

        -- Map each item to a Paragraph node
        ui.map(items, function(item)
            return Paragraph({ content = "• " .. item })
        end),

        Button({
            label = "Add item",
            on_press = function() dispatch({ type = "add" }) end,
        }),
    }
end)

ui.mount(TodoList)
```

`ui.map` returns a flat array, so nesting it inside the returned table works
without any special handling — the framework flattens nested arrays automatically.

---

## Component Composition

Components are composed by calling them inside another component's render
function, just like calling any function that returns a node.

```lua
local ui = require("ascii-ui")
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button
local useState = ui.hooks.useState

-- A reusable child component
local Counter = ui.createComponent("Counter", function(props)
    local label = props.label or "Count"
    local count, setCount = useState(0)

    return {
        Paragraph({ content = label .. ": " .. count }),
        Button({
            label = "+1",
            on_press = function() setCount(count + 1) end,
        }),
    }
end, {
    label = "string",
})

-- A parent component that composes two Counter instances
local App = ui.createComponent("App", function()
    return {
        Paragraph({ content = "=== Multi Counter ===" }),
        Counter({ label = "Apples" }),
        Counter({ label = "Oranges" }),
    }
end)

ui.mount(App)
```

Each `Counter` instance manages its own independent state — composing
components is the primary way to build larger, modular UIs.

---

## Return Value Rules

A component's render function may return:

| Return value | Behaviour |
|---|---|
| A single node | Rendered as one line / block |
| A flat `table` of nodes | Each element rendered in order |
| A **nested** `table` of nodes | Automatically flattened |
| `nil` entries in a table | Silently ignored |
| A `BufferLine` or `Bufferline` | Treated as a leaf node |

---

---

## Error Reporting

When a component throws an error during rendering, ascii-ui reports the full
**component path** and the original error message, making it easy to identify
exactly which component in a nested tree caused the failure.

```
component error [App > Counter > Button]: attempt to index a nil value (local 'props')
```

The path reflects the nesting depth at the time of the error. This also applies
to errors thrown inside `useEffect` callbacks and interaction callbacks — the
component name and (for interactions) the interaction type are included in the
error message.

No special configuration is needed; error reporting is always active.

---

## See Also

- [Built-in Components](./components/button.md) — Button, Paragraph, Select, Slider, and more
- [Hooks](./hooks) — useState, useEffect, useReducer, useInterval, useTimeout
- [ui.mount](./mount) — mounting components into viewports
- [Examples](./examples) — real-world patterns including lists, animations, and clocks
