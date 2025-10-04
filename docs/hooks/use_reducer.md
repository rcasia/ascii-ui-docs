---
sidebar_position: 3
id: use-reducer
title: useReducer
sidebar_label: useReducer
description: Manage complex state logic inside ascii-ui components with a reducer function.
tags: [api, hooks, reducer, state]
---

# useReducer

`useReducer` lets you manage **complex state logic** by applying a reducer function to the current state and dispatched actions inside an ascii-ui component.

```lua
local state, dispatch = ui.hooks.useReducer(reducerFn, initialValue)
```

## Reference

### `useReducer(reducerFn, initialValue)`

Call `useReducer` at the top level of a component to manage state transitions based on **dispatched actions**.

It’s useful when your state logic is complex or when the next state depends on the **previous state** and an **action**.

```lua
local state, dispatch = ui.hooks.useReducer(reducerFn, initialValue)
```

Each time you call dispatch(action), useReducer runs your reducerFn(state, action) and updates the component state with its result.

### Parameters

| Name | Type | Description |
|------|------|--------------|
| `reducerFn` | `fun(state: T, action: A): T` | A **pure function** that takes the current state and an action, and returns the **next state**. It should not mutate the existing state. |
| `initialValue` | `T` | The initial state value. Can be any Lua type (`number`, `string`, `boolean`, `table`, etc.). Used only during the first render. |

### Returns

`useReducer` returns a pair: the current state value and a dispatch function.

| Name | Type | Description |
|------|------|--------------|
| `state` | `T` | The current state value. Each render receives the latest state after the reducer is applied. |
| `dispatch` | `fun(action: A)` | A function that sends an **action** to the reducer. When called, it computes the next state using `reducerFn(state, action)` and triggers a re-render with the updated value. |

---

## Usage

### Example: Counter with Reducer

Use `useReducer` when state transitions depend on **action types** or when you want to centralize state logic.

```lua
local ui = require("ascii-ui")
local useReducer = ui.hooks.useReducer
local Button = ui.components.Button

local function reducer(state, action)
  if action.type == "increment" then
    return state + 1
  elseif action.type == "decrement" then
    return state - 1
  end
  return state
end

local Counter = ui.createComponent("Counter", function()
  local count, dispatch = useReducer(reducer, 0)

  return {
    Button({
      label = "Count: " .. count,
      on_press = function()
        dispatch({ type = "increment" })
      end,
    }),
  }
end)

return Counter
```

Each time you call `dispatch({ type = "increment" })`, the reducer function runs and returns the next state.
The component re-renders automatically with the new value.
