---
sidebar_position: 2
id: use-effect
title: useEffect
sidebar_label: useEffect
description: Run side effects inside ascii-ui components.
tags: [api, hooks, effect]
---

# useEffect

`useEffect` lets you run a **side effect** after a component renders, and when specific observed values change.

```lua
ui.hooks.useEffect(effect_fn, dependencies)
```

## Reference

### `useEffect(effect_fn, dependencies?)`

Call `useEffect` at the top level of a component to perform side effects like logging, timers, or subscriptions.

If you provide **dependencies**, the effect will re-run only when those values change.  
If you omit them, the effect runs after **every render**.

```lua
ui.hooks.useEffect(function()
  -- side effect
  return function()
    -- optional cleanup
  end
end, { dep1, dep2 })
```

### Parameters

| Name | Type | Description |
|------|------|--------------|
| `effect_fn` | `fun(): (fun() \| nil)` | The callback function to run as a side effect. It executes after the component renders. Optionally, it can return a **cleanup function** that runs before the next effect or when the component unmounts. |
| `dependencies?` | `any[]` | *(Optional)* A table of **observed values**. The effect re-runs only when one of these values changes. If omitted, the effect runs after **every render**. If you pass an empty table `{}`, it runs only **once on mount**. |

### Returns

`useEffect` does not return a value.

Its purpose is to register a **side effect** and manage **cleanup logic** tied to the component’s lifecycle.  
If the `effect_fn` returns a function, that function will be called as a **cleanup** before the next effect runs or when the component unmounts.

---

## Usage

### Example: Run When Value Changes

Provide dependencies so the effect runs **only** when they change:

```lua
useEffect(function()
  print("Count changed to", count)
end, { count })
```

### Example: Run on Every Render

Call `useEffect` without dependencies to run the effect after **every render**:

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local useEffect = ui.hooks.useEffect
local Paragraph = ui.components.Paragraph
local Button = ui.components.Button

local MyComponent = ui.createComponent("MyComponent", function()
  local count, setCount = useState(0)

  useEffect(function()
    print("Rendered!")
  end)

  return {
    Paragraph({ content = "Count: " .. count }),
    Button({
      label = "Increment",
      on_press = function() setCount(count + 1) end,
    }),
  }
end)

return MyComponent
```

Every time the component re-renders, the effect runs again.

### Example: Cleanup Function

Return a function from the effect to clean up resources when the component unmounts or before the next run:

```lua
useEffect(function()
  local timer = vim.loop.new_timer()
  timer:start(1000, 1000, function()
    print("Tick!")
  end)

  return function()
    timer:stop()
    timer:close()
    print("Timer stopped")
  end
end, {})
```

This cleanup function is useful for timers, subscriptions, or event listeners, ensuring they are properly released when the component unmounts.
