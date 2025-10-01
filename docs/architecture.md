---

sidebar_position: 4
---

# How Everything Works

ascii-ui.nvim is built on a declarative and reactive model, taking inspiration
from modern component-based frameworks like React. This approach fundamentally
changes how you think about building your UI: instead of manually updating the
screen, you declare the desired state, and the framework handles the rest.

## 1. The Declarative Core

You declare what you want your UI to look like at any point in time, rather than
writing sequential steps to manipulate the terminal buffer.

```
+------------------+         +-----------------+
| Your Component   |         | ascii-ui.nvim   |
| (Render Function)| ------->| (The Renderer)  |
|                  |         |                 |
| return {         |         |  Declares what  |
|   Button(...),   |         |  the UI should  |
|   Paragraph(...),|         |  look like, not |
| }                |         |  how to draw it.|
+------------------+         +-----------------+
```

- The Render Function as a Blueprint: Every custom component's render function is
a blueprint. When it executes, it returns a new description (a tree of components
and blocks) based on the current data.

- No Direct Manipulation: You never directly call Neovim APIs to change text,
colors, or window positions within your components. You simply return a
definition, and the framework ensures the final ASCII output matches that definition.

## 2. The Reactive Loop (State is King)

The system is reactive, meaning the UI automatically responds to changes in
data. This is governed by the state management system:

```

+-------------+    `setCount()`     +-----------------+    Re-render   +-----------+
| User        | ------------------> | Component State | -------------> | Component |
| Interaction |    triggers         | (e.g., `count`) |    (e.g., `count` updated)  | (Render Fn)
+-------------+                     +-----------------+                 +-----v-----+
                                                                              | Returns New
                                                                              | UI Description
                                                                              v
                                                           +-------------------------+
                                                           | ascii-ui.nvim Renderer  |
                                                           | (Calculates & Applies   |
                                                           |  Changes to Neovim UI)  |
                                                           +-------------------------+
```

- **The Source of Truth**: useState: The `ui.hooks.useState()` hook gives your
component a local memory (the state).

- **The State Setter**: When a user interacts with the UI (e.g., clicks a
button) and your logic calls the state setter function
(e.g., setCount(new_value)), this signals the change to the framework.

- **Automatic Re-render**: ascii-ui.nvim automatically schedules the
component's render function to run again. This starts the reactive loop:
  - State Change →
  Render Function Re-runs→New UI Description →
  Framework Updates Screen

  - The framework calculates the most efficient way to apply the changes to the
  Neovim buffer, updating only what is necessary.

## 3. Component Composition

Just as in React, UIs are built by composing small, isolated components.

```
      +-----+
      | App |
      +--+--+
         |
         v
    +---------------+
    | SimpleCounter |
    +-----+---------+
          |
    +-----+-----+
    | Paragraph |
    +-----------+
    +-----------+
    |   Button  |
    +-----------+
```

- A complex component (like an App) is simply a parent that renders several
smaller components (SimpleCounter, Button, etc.).

- Each component manages its own state and renders its own piece of the UI,
making every part of your application modular and reusable.
