---

sidebar_position: 1
---

# `useState` Hook

The `useState` hook in **ascii-ui.nvim** is a fundamental building block for managing state within functional components. Inspired by [React's `useState`](https://react.dev/reference/react/useState), it allows you to store and update values that persist across component re-renders. This makes it easy to build interactive and dynamic user interfaces.

---

## Overview

`useState` is a hook designed for use within **ascii-ui.nvim** functional components. It lets you associate a piece of state (any Lua value) with a component instance. When the state changes, the component is notified and re-renders with the new value.

**Key features:**

- Simple state management for functional components.
- Triggers UI updates when the state changes.
- Encapsulates state logic within a component, without relying on global variables.

---

## Signature

```lua
local getValue, setValue = useState(initialValue)
```

- `initialValue` (*any*): The initial state value.

---

## Parameters

| Name         | Type    | Description                  |
|--------------|---------|------------------------------|
| initialValue | any     | The initial value for state. |

---

## Return Value

`useState` returns two functions:

| Name     | Type               | Description                                                   |
|----------|--------------------|---------------------------------------------------------------|
| getValue | `function(): any`  | Returns the current value of the state.                       |
| setValue | `function(any): ()`| Updates the state value and schedules the component to rerun. |

---
---

## Usage

### Basic Example

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState

local function MyComponent()
  local count, setCount = useState(0)

  return function()
      -- Render logic using count()
  end
end
```

- `count()` gets the current value.
- `setCount(newValue)` updates the value and triggers a UI update.

### Multiple State Variables

You can use `useState` multiple times in a component to manage separate state values:

```lua
local count, setCount = useState(0)
local name, setName = useState("initial value")
```

### Updating State

Call the setter function with the new value:

```lua
setCount(count() + 1)
setName("new value")
```

### Functional Updates

Unlike React's `useState`, the ascii-ui version does not have a built-in functional update form (like passing a function to setState). However, you can implement logic yourself if needed:

```lua
setCount(function(prev) return prev + 1 end) -- Not supported directly
setCount(count() + 1) -- Correct usage
```

---

## Comparison with React's `useState`

| Feature            | ascii-ui.nvim                  | React                                      |
|--------------------|-------------------------------|---------------------------------------------|
| Initialization     | `useState(initialValue)`      | `useState(initialValue)`                    |
| Get current value  | Call getter: `count()`        | Use variable: `count`                       |
| Set value          | Call setter: `setCount(val)`  | Call setter: `setCount(val)`                |
| Triggers rerender  | Yes                           | Yes                                         |
| Functional updates | Not built-in                  | Built-in: `setCount(prev => prev + 1)`      |

---

## Best Practices

- Always call `useState` at the top level of your component (not inside loops or conditionals).
- Use unique `useState` calls for each independent piece of state.
- Use the getter function (e.g., `count()`) to access the current value inside the render function.
- Remember to call the setter (e.g., `setCount(newValue)`) to update the state and trigger rerendering.

---

## Related

- [`useReducer`](./use_reducer): For managing complex state logic.
- [`useEffect`](./use_ffect): For running side effects after state updates.

---

## References

- [ascii-ui.nvim source: use_state.lua](https://github.com/rcasia/ascii-ui.nvim/blob/main/lua/ascii-ui/hooks/use_state.lua)
- [React documentation: useState](https://react.dev/reference/react/useState)
- [ascii-ui.nvim README](https://github.com/rcasia/ascii-ui.nvim#readme)

---
