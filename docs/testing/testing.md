---
sidebar_position: 1
---

# Testing Library

ascii-ui provides a testing library inspired by [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/) to help you test your components. The library provides user-centric queries and assertions that focus on what the user sees and interacts with.

## Overview

The testing library has two modes:

- **Unit Testing** (`require("ascii-ui.testing")`) — Test components in isolation without a Neovim window
- **E2E Testing** (`require("ascii-ui.testing.e2e")`) — Test components in a real Neovim window with keyboard interactions

## Installation

The testing library is included with ascii-ui:

```lua
local testing = require("ascii-ui.testing")
local testing_e2e = require("ascii-ui.testing.e2e")
```

## Unit Testing

Use unit testing to test components in isolation. This is faster and doesn't require a Neovim window.

### Basic Example

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

### Rendering

```lua
local screen = testing.render(Component)
```

The `render()` function takes a component and returns a `Screen` object with query and assertion methods.

### Queries

Queries find segments in the rendered buffer. They throw an error if the element is not found (except `queryByText` which returns `nil`).

#### `screen:getByText(text)`

Finds a segment containing the given text.

```lua
local segment = screen:getByText("apple")
assert.are.same("apple", segment.content)
```

#### `screen:queryByText(text)`

Like `getByText`, but returns `nil` instead of throwing an error.

```lua
local segment = screen:queryByText("missing")
assert.is_nil(segment)
```

#### `screen:getAllByText(text)`

Finds all segments containing the given text.

```lua
local segments = screen:getAllByText("item")
assert.are.same(3, #segments)
```

#### `screen:getByHighlight(highlight)`

Finds a segment with the given highlight group.

```lua
local segment = screen:getByHighlight("Selection")
```

#### `screen:getFocusable()`

Returns the first focusable segment.

```lua
local focusable = screen:getFocusable()
assert.is_true(focusable:is_focusable())
```

#### `screen:getAllFocusable()`

Returns all focusable segments.

```lua
local focusables = screen:getAllFocusable()
assert.are.same(3, #focusables)
```

### Assertions

Assertions check properties of the rendered buffer.

#### `screen:hasText(text)`

Checks if the buffer contains the given text anywhere.

```lua
assert.is_true(screen:hasText("hello"))
```

#### `screen:hasLine(line)`

Checks if the buffer contains an exact line.

```lua
assert.is_true(screen:hasLine("[x] apple"))
```

#### `screen:hasLines(lines)`

Checks if the buffer exactly matches the given lines.

```lua
assert.is_true(screen:hasLines({
    "[x] apple",
    "[ ] banana"
}))
```

#### `screen:hasFocusable(text)`

Checks if a focusable segment with the given text exists.

```lua
assert.is_true(screen:hasFocusable("Submit"))
```

#### `screen:hasHighlight(highlight)`

Checks if a segment with the given highlight exists.

```lua
assert.is_true(screen:hasHighlight("Selection"))
```

### Interactions

Simulate user interactions with segments.

#### `screen:select(text)`

Triggers the SELECT interaction on a segment and re-renders.

```lua
screen:select("apple")
assert.is_true(screen:hasLine("[x] apple"))
```

#### `screen:trigger(text, interaction)`

Triggers a custom interaction on a segment and re-renders.

```lua
local interaction_type = require("ascii-ui.interaction_type")
screen:trigger("button", interaction_type.SELECT)
```

#### `screen:focus(text)`

Moves focus to a focusable segment (unit test mode).

```lua
screen:focus("Submit")
```

### Buffer Inspection

#### `screen:toLines()`

Returns the buffer as an array of lines.

```lua
local lines = screen:toLines()
-- { "[x] apple", "[ ] banana" }
```

#### `screen:toSnapshot()`

Returns the buffer as a single string.

```lua
local snapshot = screen:toSnapshot()
-- "[x] apple\n[ ] banana"
```

## E2E Testing

Use E2E testing when you need to test keyboard interactions, cursor movement, or real Neovim buffer behavior.

### Basic Example

```lua
local testing_e2e = require("ascii-ui.testing.e2e")
local ui = require("ascii-ui")
local Select = ui.components.Select
local it = require("plenary.async.tests").it

describe("MyComponent E2E", function()
    it("navigates with keyboard", function()
        local App = ui.createComponent("Test", function()
            return Select({ options = { "a", "b", "c" } })
        end)

        local screen = testing_e2e.mount(App)

        -- Wait for initial render
        assert.is_true(screen:waitForText("[x] a"))

        -- Simulate keypress
        screen:press("j")

        -- Assert cursor moved
        assert.is_true(screen:cursorIsAt(2))
    end)
end)
```

### Mounting

```lua
local screen = testing_e2e.mount(Component)
```

The `mount()` function opens a real Neovim window and returns an `E2EScreen` object.

### E2E Methods

#### `screen:bufferContains(text)`

Checks if the Neovim buffer contains the given text.

```lua
assert.is_true(screen:bufferContains("hello"))
```

#### `screen:waitForText(text, timeout?)`

Waits for text to appear in the buffer (useful for async operations).

```lua
assert.is_true(screen:waitForText("loading...", 2000))
```

#### `screen:cursorIsAt(line, col?)`

Asserts the cursor is at the given position.

```lua
assert.is_true(screen:cursorIsAt(2, 5))
```

#### `screen:press(keys)`

Simulates a Neovim keypress.

```lua
screen:press("j")      -- move down
screen:press("<CR>")   -- press Enter
screen:press("lll")    -- move right 3 times
```

### Inherited Methods

`E2EScreen` inherits all methods from `Screen`, so you can use the same queries and assertions:

```lua
screen:hasText("hello")
screen:hasLine("[x] apple")
screen:toLines()
```

## Migration Guide

If you have existing tests with duplicated helpers, you can migrate to the testing library:

### Before

```lua
local function feed(keys)
    vim.api.nvim_feedkeys(keys, "mtx", true)
end

local function buffer_contains(bufnr, pattern)
    return vim.wait(1000, function()
        local lines = vim.api.nvim_buf_get_lines(bufnr, 0, -1, false)
        local content_str = vim.iter(lines):join("\n")
        return string.find(content_str, pattern, 1, true) ~= nil
    end)
end

describe("MyComponent", function()
    it("works", function()
        local bufnr = ui.mount(App)
        assert(buffer_contains(bufnr, "hello"))
        feed("j")
        assert(buffer_contains(bufnr, "world"))
    end)
end)
```

### After

```lua
local testing_e2e = require("ascii-ui.testing.e2e")

describe("MyComponent", function()
    it("works", function()
        local screen = testing_e2e.mount(App)
        assert.is_true(screen:waitForText("hello"))
        screen:press("j")
        assert.is_true(screen:waitForText("world"))
    end)
end)
```

## Best Practices

1. **Prefer unit tests** — Use `testing.render()` for most tests. They're faster and don't require a Neovim window.

2. **Use E2E for interactions** — Use `testing_e2e.mount()` when testing keyboard navigation, cursor movement, or real buffer behavior.

3. **Query like a user** — Use `getByText()` to find elements the way a user would see them, not by internal IDs.

4. **Use `waitForText` for async** — When testing state changes or async operations, use `waitForText()` instead of `bufferContains()`.

5. **Assert on output, not implementation** — Test what the user sees (`hasLines`), not internal state.
