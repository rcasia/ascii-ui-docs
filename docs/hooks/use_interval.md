---
sidebar_position: 6
id: use-interval
title: useInterval
sidebar_label: useInterval
description: Run a callback function at a specified interval inside ascii-ui components.
tags: [api, hooks, effect, interval, timer]
---

# useInterval

`useInterval` lets you run a **callback function repeatedly** at a specified time interval (in milliseconds).
It automatically handles timer creation, cleanup, and updates when the delay changes.

```lua
ui.hooks.useInterval(callback, delay_ms)
```

## Reference

### `useInterval(callback, delay_ms)`

Call `useInterval` at the top level of a component to execute a function **repeatedly** at a fixed time interval.
When the component unmounts or the delay changes, the interval is **automatically cleaned up**.

```lua
ui.hooks.useInterval(function()
  print("Tick!")
end, 1000)
```

### Parameters

| Name | Type | Description |
|------|------|--------------|
| `callback` | `function` | The function to execute at each interval. It will be called repeatedly on every tick. |
| `delay_ms` | `number \| nil` | The interval delay in **milliseconds**. If `nil` or `<= 0`, the interval is **not started**. Changing this value will restart the timer with the new delay. |

### Returns

`useInterval` does not return a value.

Its purpose is to **schedule a repeating callback** that automatically cleans up when the component unmounts or when `delay_ms` changes.

---

## Usage

### Example: Simple Interval Counter

Use `useInterval` to update state every few milliseconds.

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local useInterval = ui.hooks.useInterval
local Paragraph = ui.components.Paragraph

local Ticker = ui.createComponent("Ticker", function()
  local count, setCount = useState(0)

  useInterval(function()
    setCount(function(prev) return prev + 1 end)
  end, 1000) -- every 1 second

  return {
    Paragraph({ content = "Seconds elapsed: " .. count }),
  }
end)

return Ticker
```
