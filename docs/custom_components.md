---
sidebar_position: 2
---

# Create a Custom Component

You can easily implement a custom component extending the `ascii-ui.Component`.

The required methods are:

- new: constructor method to create a new instance of the component.
- render: returns a table of `ascii-ui.BufferLine` objects.

Example:

```lua

local Component = require("ascii-ui.components.component")
local BufferLine = require("ascii-ui.buffer.bufferline")
local Element = require("ascii-ui.buffer.element")

---@class MyCustomComponent : ascii-ui.Component
---@field checked boolean
---@field label string
local Checkbox = {
 __name = "MyCustomComponent",
}

---@param props? { checked?: boolean, label?: string }
---@return ascii-ui.MyCustomComponent
function Checkbox:new(props)
 props = props or {}
 props = {
  checked = props.checked or false,
  label = props.label or ""
 }
 return Component:extend(self, props)
end

---@return ascii-ui.BufferLine[]
function Checkbox:render()
 return { 
    BufferLine:new(
      Element:new(("[%s] %s"):format(self.checked and "x" or " ", self.label))
    ) 
  }
end

function Checkbox:toggle()
 self.checked = not self.checked
end

return Checkbox
```
