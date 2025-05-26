---

sidebar_position: 3
sidebar_label: useReducer
---

# `useReducer` Hook

The `useReducer` hook in **ascii-ui.nvim** is a powerful tool for managing complex state logic within functional components. Inspired by [React’s `useReducer`](https://react.dev/reference/react/useReducer), it is especially useful when state transitions depend on specific actions or when the next state depends on the previous one.

---

## Overview

`useReducer` enables you to handle state updates through a **reducer function**, which receives the current state and an action, returning the new state. This approach is well-suited for cases where:

- The next state depends on the previous state.
- Updates are triggered by action objects.
- State logic is complex or modular.

---

## Signature

```lua
local getValue, dispatch = useReducer(reducerFn, initialValue)
```

- `reducerFn` (*function*): `(state, action) -> newState`
- `initialValue` (*any*): The initial state value.

---

## Parameters

| Name         | Type      | Description                                                     |
|--------------|-----------|-----------------------------------------------------------------|
| reducerFn    | function  | A function `(state, action) -> newState` that determines how the state is updated. |
| initialValue | any       | The initial value for the state.                                |

The `action` object is typically structured as a table, e.g. `{ type = "increment", params = { ... } }`.

---

## Return Value

`useReducer` returns two functions:

| Name      | Type                   | Description                                           |
|-----------|------------------------|-------------------------------------------------------|
| getValue  | `function(): any`      | Immediately returns the current state value.          |
| dispatch  | `function(action: any)`| Dispatches an action to the reducer, updating state.  |

**Note:**  

- You **call** `getValue()` to get the current state value (it is not a wrapper or another function).
- This differs from React, where the state is a variable; in ascii-ui.nvim, it is a getter function.

---

## Usage

### Basic Example

```lua
local ui = require("ascii-ui")
local useReducer = ui.hooks.useReducer

-- Reducer function
local function counterReducer(state, action)
  if action.type == "increment" then
    return { value = state.value + 1 }
  elseif action.type == "decrement" then
    return { value = state.value - 1 }
  end
  return state
end

local function CounterComponent()
  local getCounter, dispatch = useReducer(counterReducer, { value = 0 })

  return function()
    local value = getCounter()
    return {
      -- Use value.value in your rendering logic
      -- Call dispatch({ type = "increment" }) to update
    }
  end
end
```

### Multiple Actions

You can define as many action types as needed in your reducer:

```lua
local function listReducer(state, action)
  if action.type == "add" then
    return vim.list_extend(state, { action.params.item })
  elseif action.type == "remove" then
    table.remove(state, action.params.index)
    return state
  end
  return state
end

local getItems, dispatch = useReducer(listReducer, { "item1", "item2" })
dispatch({ type = "add", params = { item = "item3" } })
local items = getItems()
```

### Comparison with useState

- `useReducer` is preferable when state logic is complex, involves multiple sub-values, or requires predictable state transitions based on actions.
- Use `useState` for simple, independent values.

---

## Comparison with React's `useReducer`

| Feature           | ascii-ui.nvim                                 | React                           |
|-------------------|-----------------------------------------------|---------------------------------|
| Initialization    | `useReducer(reducer, initVal)`                | `useReducer(reducer, initVal)`  |
| Access state      | Call getter: `getValue()`                     | Use variable: `state`           |
| Dispatch action   | Call: `dispatch(action)`                      | Call: `dispatch(action)`        |
| Action shape      | Any Lua value, but typically a table          | Any value, typically an object  |
| Trigger re-render | Yes                                           | Yes                             |

---

---

## Best Practices

- Define your reducer function outside the component for readability.
- Always return a new state object or table (avoid mutating the current state directly).
- Use clear, descriptive action types.
- Use the getter (e.g., `getValue()`) to read the current state within your render function.
- Do not use `useReducer` inside loops or conditionals; always call it at the top level of your component.

---

## Related

- [`useState`](./use_state): Simpler state management for primitives and independent values.
- [`useEffect`](./use_effect): Run side effects after state changes.

---

## References

- [ascii-ui.nvim source: use_reducer.lua](https://github.com/rcasia/ascii-ui.nvim/blob/main/lua/ascii-ui/hooks/use_reducer.lua)
- [React documentation: useReducer](https://react.dev/reference/react/useReducer)
- [ascii-ui.nvim README](https://github.com/rcasia/ascii-ui.nvim#readme)

---
