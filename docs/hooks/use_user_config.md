---
id: use-user-config
title: useUserConfig
sidebar_label: useUserConfig
description: Access the current user configuration inside ascii-ui components.
tags: [api, hooks, config]
---

# useUserConfig

`useUserConfig` provides **read-only access** to the current **user configuration** for ascii-ui.  
It returns a **deep copy** of the global user config so that components can safely read values without mutating shared state.

```lua
local config = ui.hooks.useUserConfig()
```

## Reference

### `useUserConfig()`

Call `useUserConfig` at the top level of a component to retrieve the current **ascii-ui user configuration**.  
The returned value is a **deep copy**, ensuring that components cannot mutate the global config accidentally.

```lua
local config = ui.hooks.useUserConfig()
print(config.characters.right_triangule)
```

Use this hook whenever you need to read user preferences, theme settings, or feature flags defined in the ascii-ui configuration.

### Parameters

`useUserConfig` does not take any parameters.
It always returns the **current user configuration** from the global ascii-ui settings.

### Returns

`useUserConfig` returns a **deep copy** of the current ascii-ui user configuration.

| Name | Type | Description |
|------|------|--------------|
| `config` | `ascii-ui.Config` | A deep copy of the global user configuration. It is **read-only** — modifying it will not affect the actual user config. Use it to safely access preferences, theme options, or feature flags. |

---

## Usage

### Example: Reading Character Configuration

Use `useUserConfig` to access user-defined characters or symbols from the global config:

```lua
local ui = require("ascii-ui")
local useUserConfig = ui.hooks.useUserConfig
local Paragraph = ui.components.Paragraph

local CharacterInfo = ui.createComponent("CharacterInfo", function()
  local config = useUserConfig()

  return {
    Paragraph({ content = "Right triangle character: " .. config.characters.right_triangule }),
  }
end)

return CharacterInfo
```
