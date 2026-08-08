---
sidebar_position: 3
id: e2e-testing
title: E2E Testing
sidebar_label: E2E Testing
description: Test components in a real Neovim window with keyboard interactions.
tags: [testing, e2e, integration]
---

# E2E Testing

Use E2E testing when you need to test keyboard interactions, cursor movement, or real Neovim buffer behavior.

## Basic Example

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

## Mounting

```lua
local screen = testing_e2e.mount(Component)
```

The `mount()` function opens a real Neovim window and returns an `E2EScreen` object.

## E2E Methods

### `screen:bufferContains(text)`

Checks if the Neovim buffer contains the given text.

```lua
assert.is_true(screen:bufferContains("hello"))
```

### `screen:waitForText(text, timeout?)`

Waits for text to appear in the buffer (useful for async operations).

```lua
assert.is_true(screen:waitForText("loading...", 2000))
```

### `screen:cursorIsAt(line, col?)`

Asserts the cursor is at the given position.

```lua
assert.is_true(screen:cursorIsAt(2, 5))
```

### `screen:press(keys)`

Simulates a Neovim keypress.

```lua
screen:press("j")      -- move down
screen:press("<CR>")   -- press Enter
screen:press("lll")    -- move right 3 times
```

## Inherited Methods

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
