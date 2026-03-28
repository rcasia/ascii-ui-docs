---
sidebar_position: 1
id: hooks
title: Hooks in ascii-ui.nvim
sidebar_label: Overview
description: An overview of the hooks system in ascii-ui.nvim — how to manage state, side effects, timers, and configuration inside components.
tags: [hooks, useState, useEffect, useReducer, useConfig, useInterval, useTimeout]
---

# Hooks in ascii-ui.nvim

**ascii-ui.nvim** provides hooks as the primary mechanism for managing state,
side effects, and reusable behavior inside functional UI components — drawing
inspiration from React's hooks model.

Hooks keep your component logic clean, local, and composable without any
class-like boilerplate.

---

## Available Hooks

| Hook | Import | Description |
|------|--------|-------------|
| [`useState`](./use_state.md) | `ui.hooks.useState` | Local component state with a getter and a setter. |
| [`useEffect`](./use_effect.md) | `ui.hooks.useEffect` | Run side effects after render; supports cleanup. |
| [`useReducer`](./use_reducer.md) | `ui.hooks.useReducer` | Manage complex state with a reducer function. |
| [`useConfig`](./use_user_config.md) | `ui.hooks.useConfig` | Read the global ascii-ui configuration. |
| [`useInterval`](./use_interval.md) | `ui.hooks.useInterval` | Call a function repeatedly on a timer. |
| [`useTimeout`](./use_timeout.md) | `ui.hooks.useTimeout` | Call a function once after a delay. |

---

## Quick Start

```lua
local ui = require("ascii-ui")
local useState    = ui.hooks.useState
local useEffect   = ui.hooks.useEffect
local useInterval = ui.hooks.useInterval

local Clock = ui.createComponent("Clock", function()
    local time, setTime = useState(os.date("%H:%M:%S"))

    -- Update the time every second
    useInterval(function()
        setTime(os.date("%H:%M:%S"))
    end, 1000)

    -- Log to the debug file once on mount
    useEffect(function()
        vim.notify("Clock mounted")
    end, {})

    return ui.components.Paragraph({ content = "Time: " .. time })
end)
```

---

## Usage Rules

These rules mirror React's rules of hooks and are **required** for correct
behaviour:

1. **Call hooks only at the top level** of your render function or another hook.
   Never inside loops, conditionals, or nested functions.

2. **Never call hooks outside a component**. Hooks rely on the current fiber
   context set by the work loop.

3. **The order of hook calls must be stable** between renders. The number and
   sequence of calls must not change from render to render.

```lua
-- CORRECT
local App = ui.createComponent("App", function()
    local count, setCount = useState(0)         -- always called
    local doubled = count * 2                   -- plain Lua, fine
    return ui.components.Paragraph({ content = tostring(doubled) })
end)

-- WRONG — hook called inside a condition
local Bad = ui.createComponent("Bad", function()
    if something then
        local x, setX = useState(0)             -- breaks hook ordering
    end
end)
```

---

## Hook Reference

### `useState`

Stores a single value. Returns the current value and a setter.

```lua
local count, setCount = useState(0)

-- Update with a new value
setCount(count + 1)

-- Update with a function (receives current value)
setCount(function(prev) return prev + 1 end)
```

[Full reference →](./use_state.md)

---

### `useEffect`

Runs a side effect after render. The second argument is a dependency list; pass
`{}` to run only on mount, or omit it to run on every render.

```lua
useEffect(function()
    vim.notify("component mounted")

    -- Return a cleanup function (optional)
    return function()
        vim.notify("component unmounted")
    end
end, {})
```

[Full reference →](./use_effect.md)

---

### `useReducer`

Manages complex state using a reducer pattern. Useful when the next state
depends on the previous one or on multiple action types.

```lua
local state, dispatch = useReducer(function(s, action)
    if action.type == "increment" then
        return { count = s.count + 1 }
    end
    return s
end, { count = 0 })

dispatch({ type = "increment" })
```

[Full reference →](./use_reducer.md)

---

### `useConfig`

Returns the current global ascii-ui configuration (set via `ui.setup`).

```lua
local config = useConfig()
-- config.characters.button_left, config.keymaps.press, etc.
```

[Full reference →](./use_user_config.md)

---

### `useInterval`

Calls a function repeatedly, every `interval_ms` milliseconds. The timer is
automatically cleared when the component unmounts.

```lua
useInterval(function()
    setCount(count + 1)
end, 500)
```

[Full reference →](./use_interval.md)

---

### `useTimeout`

Calls a function once after `delay_ms` milliseconds. Cleaned up on unmount.

```lua
useTimeout(function()
    vim.notify("Done!")
end, 3000)
```

[Full reference →](./use_timeout.md)

---

## See Also

- [Custom Components](../custom_components.md) — how to write components that use hooks
- [Configuration](../configuration) — the config schema read by `useConfig`
- [Examples](../examples) — real apps that combine multiple hooks
