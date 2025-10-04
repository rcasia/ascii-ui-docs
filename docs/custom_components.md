---
sidebar_position: 2
---

# Custom Components

The core principle of building an application with ascii-ui.nvim is defining
custom components. A custom component is essentially a Lua function that
returns a description of what should be rendered on the screen, allowing you
to combine existing primitives (like Button and Paragraph) and manage local
state.

## 🛠️ Creating a Simple Counter Component

This example shows the core pattern: defining a component, managing its state,
and rendering interactive elements.

The goal is to create a simple counter that displays a number and has a button
to increment it.

```lua
-- 1. Import the necessary modules
local ui = require("ascii-ui")
local useState = ui.hooks.useState
local Button = ui.components.Button
local Paragraph = ui.components.Paragraph

-- 2. Define the custom component using ui.createComponent
local SimpleCounter = ui.createComponent("SimpleCounter", function()
    -- 3. Use the useState hook to manage the counter's state
    -- It returns the current value (`count`) and an update function (`setCount`)
    local count, setCount = useState(0)

    -- 4. Define the logic for handling button press
    local function increment()
        -- The update function takes the new value and triggers a re-render
        setCount(count + 1)
    end

    -- 5. Return a table of components to be rendered
    return {
        -- The first line shows the current value using the built-in Paragraph component
        Paragraph({
            content = "Current Count: " .. count
        }),

        -- The second line is an interactive Button component
        Button({
            label = "Click to Increment",
            on_press = increment, -- Connects the button press to our logic
        }),
    }
end)
```
