---
id: use-state
title: useState
sidebar_label: useState
description: Manage local state inside ascii-ui components.
tags: [api, hooks, state]
---

# useState

`useState` lets you add local reactive state to an ascii-ui component.

```lua
local value, setValue = ui.hooks.useState(initialValue)
```

---

## Reference

### `useState(initialValue)`

Call `useState` at the top level of a component to declare a local state variable.

```lua
local value, setValue = ui.hooks.useState(initialValue)
```

### Parameters

| Name | Type | Description |
|------|------|--------------|
| `initialValue` | `T` | The initial state value. It can be any Lua type (`number`, `string`, `boolean`, `table`, etc.). The value is used only on the first render—subsequent re-renders ignore it. |

### Returns

`useState` returns a pair: the current state value and a function that lets you update it.

| Name | Type | Description |
|------|------|--------------|
| `value` | `T` | A **deep copy** of the current state. Treat it as read-only. Each render receives the latest state value. |
| `setValue` | `fun(next: T \| fun(prev: T): T)` | A function to update the state. Calling it schedules a re-render of the component. You can pass either a **new value** or an **updater function** that receives the previous value and returns the next one. |

---

Every time you call setCount, the component re-renders with the updated value.

---

## Usage

### Updating state

Use the setter function to update the state.
You can pass either a new value or a function that receives the previous value.

```lua
setCount(count + 1)
-- or
setCount(function(prev) return prev + 1 end)
```

The functional form helps avoid stale reads when updates happen rapidly.

---

### Example with Counter Component

Here’s a simple counter component using useState:

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button

local Counter = ui.createComponent("Counter", function()
  local count, setCount = useState(0)

  return {
    Paragraph({ content = "Count: " .. count }),
    Button({
      label = "Increment",
      on_press = function()
        setCount(count + 1)
      end,
    }),
  }
end)

return Counter
```
