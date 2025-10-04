---
id: use-timeout
title: useTimeout
sidebar_label: useTimeout
description: Run a callback function once after a specified delay inside ascii-ui components.
tags: [api, hooks, effect, timeout, timer]
---

# useTimeout

`useTimeout` lets you run a **callback function once** after a specified delay (in milliseconds).  
It automatically handles timer creation and cleanup when the component unmounts or when the delay changes.

```lua
ui.hooks.useTimeout(callback, delay_ms)
```

## Reference

### `useTimeout(callback, delay_ms)`

Call `useTimeout` at the top level of a component to execute a function **once** after a given delay.  
If `delay_ms` is `nil` or negative, the timeout will **not be set**.

```lua
ui.hooks.useTimeout(function()
  print("Executed after delay!")
end, 2000)
```

### Parameters

| Name | Type | Description |
|------|------|--------------|
| `callback` | `function` | The function to execute once after the specified delay. |
| `delay_ms` | `number \| nil` | The delay in **milliseconds** before executing the callback. If `nil` or negative, the timeout is **not started**. Changing this value restarts the timer with the new delay. |

### Returns

`useTimeout` does not return a value.

Its purpose is to **schedule a one-time callback** after a specified delay and automatically **clean up** the timer when the component unmounts or when `delay_ms` changes.

---

## Usage

### Example: Run Once After a Delay

Use `useTimeout` to trigger an action once after a specified delay.

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local useTimeout = ui.hooks.useTimeout
local Paragraph = ui.components.Paragraph

local DelayedMessage = ui.createComponent("DelayedMessage", function()
  local visible, setVisible = useState(false)

  useTimeout(function()
    setVisible(true)
  end, 2000) -- show message after 2 seconds

  if not visible then
    return Paragraph({ content = "Waiting..." })
  end

  return Paragraph({ content = "Hello after 2 seconds!" })
end)

return DelayedMessage
