---

sidebar_position: 4
sidebar_label: useFunctionRegistry
---

# `useFunctionRegistry` Hook

The `useFunctionRegistry` hook in **ascii-ui.nvim** allows you to register a Lua function and receive a reference string that can be used as a callback in declarative UI markup or passed as a prop to components that expect a function reference. This is especially useful in scenarios where your UI definition is dynamic (such as when using XML-like syntax) or where functions need to be referenced indirectly.

---

## Overview

`useFunctionRegistry` solves the problem of referring to functions in a declarative UI context where you may not be able to directly pass a Lua function as a prop (for example, when using string-based markup or when serializing/deserializing UI definitions). It registers your function in a global registry and returns a string reference key that can be used to look up and call the function later.

---

## Signature

```lua
local ref = useFunctionRegistry(fn)
```

- `fn` (*function*): The Lua function you want to register.

---

## Parameters

| Name | Type     | Description                      |
|------|----------|----------------------------------|
| fn   | function | The function to be registered.   |

---

## Return Value

| Name | Type   | Description                                                |
|------|--------|------------------------------------------------------------|
| ref  | string | A unique string reference to the registered function.      |

- This reference can be passed as a prop (e.g., as an event handler in markup).
- The reference can be resolved by the ascii-ui runtime to call the function.

---

## Usage

### Example using with XML Markup

When rendering UI with XML-like syntax, use the reference string as an event handler:

```lua
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local useFunctionRegistry = ui.hooks.useFunctionRegistry

local function App()
  local message, setMessage = useState("Hello World")
  local ref = useFunctionRegistry(function()
    setMessage("Button Pressed!")
  end)

  return function()
    return ([[

      <Layout>
        <Paragraph content="%s" />
        <Button label="Press me" on_press="%s" />
      </Layout>

    ]]):format(message(), ref)
  end
end

ui.mount(App())
```

- The `on_press` attribute receives the function reference as a string, and the UI system looks it up and calls it when the button is pressed.

---

## Best Practices

- Use `useFunctionRegistry` when you need to reference a function in markup or when passing callbacks indirectly.
- Avoid using it for standard in-code callbacks—prefer direct function references when possible.
- Clean up any registered functions if your application creates/destroys many dynamic callbacks to avoid memory leaks.

---

## Related

- [`useState`](./use_state.md): For managing local component state.
- [`useEffect`](./use_effect.md): For running side effects after rendering.

---

## References

- [ascii-ui.nvim source: use_function_registry.lua](https://github.com/rcasia/ascii-ui.nvim/blob/main/lua/ascii-ui/hooks/use_function_registry.lua)
- [ascii-ui.nvim README](https://github.com/rcasia/ascii-ui.nvim#readme)

---
