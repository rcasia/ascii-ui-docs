---
sidebar_position: 4
id: use-function-registry
title: useFunctionRegistry [experimental]
sidebar_label: useFunctionRegistry [experimental]
description: Register Lua functions in a global registry for later invocation within ascii-ui components.
tags: [api, hooks, registry, callbacks, experimental]
---

# useFunctionRegistry [experimental]

`useFunctionRegistry` lets you **register a Lua function** in a global registry and returns a **string key reference**.  
This reference can be used inside component markup (e.g. `on_press = ref`) to invoke the function later through the ascii-ui runtime.

```lua
local ref = ui.hooks.useFunctionRegistry(fn)
```

## Reference

### `useFunctionRegistry(fn)`

Call `useFunctionRegistry` at the top level of a component to register a **Lua function** globally and obtain a **string reference** for it.

This is useful when you need to attach callbacks (like `on_press`) to UI components that are rendered as markup or resolved later by the runtime.

```lua
local ref = ui.hooks.useFunctionRegistry(function()
  print("Button pressed!")
end)
```

You can then pass the returned ref string to a component prop:

```lua
ui.components.Button({ label = "Click Me", on_press = ref })
```

### Parameters

| Name | Type | Description |
|------|------|--------------|
| `fn` | `function` | The Lua function to register in the **global function registry**. This function can later be invoked indirectly through its string reference. |

### Returns

`useFunctionRegistry` returns a **string reference** to the registered function.

| Name | Type | Description |
|------|------|--------------|
| `reference` | `string` | A unique string key that identifies the registered function. You can pass this key to UI component props (e.g. `on_press = reference`) so the ascii-ui runtime can invoke the original function later. |

---

## Usage

### Example: Registering a Callback for XML Components

When rendering **XML-based components** or markup strings, you can’t pass a Lua function directly.  
Instead, use `useFunctionRegistry` to register the function globally and pass a **string reference**.

```lua
local ui = require("ascii-ui")
local useFunctionRegistry = ui.hooks.useFunctionRegistry

local MyComponent = ui.createComponent("MyComponent", function()
  local onPressRef = useFunctionRegistry(function()
    print("Button pressed!")
  end)

  return ([[
    <Button label="Press Me" on_press="%s" />
  ]]):format(onPressRef)
end)

return MyComponent
```

Each time the user interacts with the XML-rendered button,
the ascii-ui runtime uses the string key (onPressRef) to look up and call the registered function.
