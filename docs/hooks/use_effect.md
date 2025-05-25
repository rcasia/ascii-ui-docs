---

sidebar_position: 2
---

# `useEffect` Hook

The `useEffect` hook in **ascii-ui.nvim** is a mechanism for running side effects in your functional components. Inspired by [React’s `useEffect`](https://react.dev/reference/react/useEffect), it allows you to perform operations such as logging, subscriptions, or updating external resources in response to state changes or component rendering.

---

## Overview

`useEffect` lets you run a function (“effect”) after your component is rendered, or whenever certain state values change. This is essential for:

- Performing side effects after rendering (e.g., logging, updating external state)
- Subscribing and unsubscribing to events
- Reacting to changes in specific state variables

The effect function runs once after the initial render. If you specify observed values (“dependencies”), the function will re-run whenever any of those values change.

---

## Signature

```lua
useEffect(effectFn [, observedValues])
```

- `effectFn` (*function*): The function to run as a side effect.
- `observedValues` (*table of getter functions*, optional): State getter functions to watch. The effect runs again if any of these values change.

---

## Parameters

| Name           | Type                    | Description                                                                |
|----------------|-------------------------|----------------------------------------------------------------------------|
| effectFn       | function                | The side-effect callback function to execute.                              |
| observedValues | table of functions (optional) | List of state getter functions to observe for changes.              |

---

## Usage

### Basic Example

Run a function after the component renders:

```lua
local useEffect = require("ascii-ui.hooks.use_effect")

useEffect(function()
  print("Component mounted or rendered!")
end)
```

### Watching State

Run a function only when certain state changes:

```lua
local useState = require("ascii-ui.hooks.use_state")
local useEffect = require("ascii-ui.hooks.use_effect")

local name, setName = useState("ascii-ui")

useEffect(function()
  print("Name changed to: " .. name())
end, { name })
```

- The effect prints a message only when `name` changes (including the first render).

### Multiple Dependencies

You can observe multiple state getters:

```lua
useEffect(function()
  print("Either count or name changed!")
end, { count, name })
```

---

## Comparison with React's `useEffect`

| Feature                 | ascii-ui.nvim                                     | React                                      |
|-------------------------|---------------------------------------------------|---------------------------------------------|
| Invocation              | `useEffect(fn, {dep1, dep2})`                    | `useEffect(fn, [dep1, dep2])`              |
| Observed values         | Table of getter functions                         | Array of variables/values                  |
| Initial run             | Always runs after first render                    | Always runs after first render              |
| Re-run on change        | Yes, when any observed getter returns a new value | Yes, when any dependency value changes      |
| Cleanup support         | Not built-in                                      | Return function from effectFn for cleanup   |

---

## Best Practices

- Pass only the getter functions you want to observe in the dependency table.
- Avoid causing side effects that update the observed state in a way that causes infinite loops.
- Use a single `useEffect` for related logic; use multiple for unrelated effects.
- If you want to run an effect only once (on mount), omit the dependency table.

---

## Related

- [`useState`](./use_state.md): For managing local state.
- [`useReducer`](./use_reducer.md): For more complex state updates.

---

## References

- [ascii-ui.nvim source: use_effect.lua](https://github.com/rcasia/ascii-ui.nvim/blob/main/lua/ascii-ui/hooks/use_effect.lua)
- [React documentation: useEffect](https://react.dev/reference/react/useEffect)
- [ascii-ui.nvim README](https://github.com/rcasia/ascii-ui.nvim#readme)

---
