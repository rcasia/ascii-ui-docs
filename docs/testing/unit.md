---
sidebar_position: 2
id: unit-testing
title: Unit Testing
sidebar_label: Unit Testing
description: Test components in isolation without a Neovim window.
tags: [testing, unit]
---

# Unit Testing

Use unit testing to test components in isolation. This is faster and doesn't require a Neovim window.

## Basic Example

```lua
local testing = require("ascii-ui.testing")
local ui = require("ascii-ui")
local Select = ui.components.Select

describe("MyComponent", function()
    it("renders options", function()
        local App = ui.createComponent("Test", function()
            return Select({ options = { "apple", "banana", "cherry" } })
        end)

        local screen = testing.render(App)

        -- Assert the rendered output
        assert.is_true(screen:hasLines({
            "[x] apple",
            "[ ] banana",
            "[ ] cherry"
        }))
    end)
end)
```

## Rendering

```lua
local screen = testing.render(Component)
```

The `render()` function takes a component and returns a `Screen` object with query and assertion methods.

## Queries

Queries find segments in the rendered buffer. They throw an error if the element is not found (except `queryByText` which returns `nil`).

### `screen:getByText(text)`

Finds a segment containing the given text.

```lua
local segment = screen:getByText("apple")
assert.are.same("apple", segment.content)
```

### `screen:queryByText(text)`

Like `getByText`, but returns `nil` instead of throwing an error.

```lua
local segment = screen:queryByText("missing")
assert.is_nil(segment)
```

### `screen:getAllByText(text)`

Finds all segments containing the given text.

```lua
local segments = screen:getAllByText("item")
assert.are.same(3, #segments)
```

### `screen:getByHighlight(highlight)`

Finds a segment with the given highlight group.

```lua
local segment = screen:getByHighlight("Selection")
```

### `screen:getFocusable()`

Returns the first focusable segment.

```lua
local focusable = screen:getFocusable()
assert.is_true(focusable:is_focusable())
```

### `screen:getAllFocusable()`

Returns all focusable segments.

```lua
local focusables = screen:getAllFocusable()
assert.are.same(3, #focusables)
```

## Assertions

Assertions check properties of the rendered buffer.

### `screen:hasText(text)`

Checks if the buffer contains the given text anywhere.

```lua
assert.is_true(screen:hasText("hello"))
```

### `screen:hasLine(line)`

Checks if the buffer contains an exact line.

```lua
assert.is_true(screen:hasLine("[x] apple"))
```

### `screen:hasLines(lines)`

Checks if the buffer exactly matches the given lines.

```lua
assert.is_true(screen:hasLines({
    "[x] apple",
    "[ ] banana"
}))
```

### `screen:hasFocusable(text)`

Checks if a focusable segment with the given text exists.

```lua
assert.is_true(screen:hasFocusable("Submit"))
```

### `screen:hasHighlight(highlight)`

Checks if a segment with the given highlight exists.

```lua
assert.is_true(screen:hasHighlight("Selection"))
```

## Interactions

Simulate user interactions with segments.

### `screen:select(text)`

Triggers the SELECT interaction on a segment and re-renders.

```lua
screen:select("apple")
assert.is_true(screen:hasLine("[x] apple"))
```

### `screen:trigger(text, interaction)`

Triggers a custom interaction on a segment and re-renders.

```lua
local interaction_type = require("ascii-ui.interaction_type")
screen:trigger("button", interaction_type.SELECT)
```

### `screen:focus(text)`

Moves focus to a focusable segment (unit test mode).

```lua
screen:focus("Submit")
```

## Buffer Inspection

### `screen:toLines()`

Returns the buffer as an array of lines.

```lua
local lines = screen:toLines()
-- { "[x] apple", "[ ] banana" }
```

### `screen:toSnapshot()`

Returns the buffer as a single string.

```lua
local snapshot = screen:toSnapshot()
-- "[x] apple\n[ ] banana"
```
