---
id: tree
title: Tree
sidebar_label: Tree
sidebar_position: 7
description: A collapsible tree view component for hierarchical data.
tags: [components, interactive]
---

# Tree

`Tree` renders a collapsible hierarchical tree view. Each node can have children, and nodes with children can be expanded or collapsed by pressing `<CR>`.

```lua
require("ascii-ui.components.tree")({
  tree = {
    text = "root",
    children = {
      { text = "child 1" },
      { text = "child 2", children = { { text = "grandchild" } } },
    },
  },
})
```

---

## Reference

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `tree` | `TreeNode` | Yes | The root node of the tree (see `TreeNode` definition below). |
| `level` | `integer` | No | The nesting depth of this node. Used internally for recursive rendering. Defaults to `0` (root). |
| `has_siblings` | `boolean` | No | Whether this node has siblings at the same level. Used internally. Defaults to `false`. |
| `is_last` | `boolean` | No | Whether this is the last child in its parent's children list. Used internally. Defaults to `false`. |

### TreeNode

```lua
---@class TreeNode
---@field text string          The label displayed for this node.
---@field children? TreeNode[] Child nodes. If absent or empty, this is a leaf.
---@field expanded? boolean    Whether this node starts expanded. Defaults to true.
```

---

## Usage

### Basic tree

```lua
local ui = require("ascii-ui")
local Tree = require("ascii-ui.components.tree")

local FileTree = ui.createComponent("FileTree", function()
  return {
    Tree({
      tree = {
        text = "src/",
        children = {
          { text = "init.lua" },
          {
            text = "components/",
            children = {
              { text = "button.lua" },
              { text = "paragraph.lua" },
            },
          },
        },
      },
    }),
  }
end)

ui.mount(FileTree)
```

This renders:

```
src/
│─ ▾ components/
│  ╰─ button.lua
│  ╰─ paragraph.lua
╰─ init.lua
```

### Collapsed by default

Set `expanded = false` on any node to start it collapsed:

```lua
Tree({
  tree = {
    text = "vendor/",
    expanded = false,
    children = {
      { text = "dependency-a" },
      { text = "dependency-b" },
    },
  },
})
```

The node will show `▸ vendor/` and its children will be hidden until the user expands it.

---

## Keyboard interaction

| Key | Action |
|-----|--------|
| `j` / `k` | Move focus to the next / previous focusable node |
| `<CR>` | Toggle expand/collapse on the focused node |

---

## Notes

- `Tree` is not part of the default `ui.components` public API. Import it with `require("ascii-ui.components.tree")`.
- The tree-drawing characters (`╰─`, `├─`, `▸`, `▾`, `│`) are read from the user configuration and can be customized via `ui.setup({ characters = { ... } })`.
- Each node manages its own expanded/collapsed state internally using `useState`.
- Leaf nodes (nodes without children) are rendered as focusable but do not toggle on `<CR>`.
