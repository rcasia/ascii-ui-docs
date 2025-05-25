---

sidebar_position: 1
---

# Hooks in ascii-ui.nvim

**ascii-ui.nvim** introduces hooks as a core mechanism for managing state, side effects, and behavioral reusability within functional UI components—drawing inspiration from [React's hooks](https://react.dev/reference/react/hooks). Hooks enable powerful, modular, and declarative UI development, allowing your components to be interactive and dynamic while keeping logic clean and localized.

---

## What Are Hooks?

**Hooks** are special functions that let you “hook into” ascii-ui’s internal state and lifecycle features from functional components.

- They allow you to store and update state, trigger effects, and register functions for dynamic UI behaviors.
- Hooks are only usable inside functional components or other hooks.

**Why use hooks?**

- Encapsulate logic in a reusable, composable way.
- Avoid complexity and repetition in your UI components.
- Enable side effects and advanced patterns without writing class-like boilerplate.

---

## Usage Guidelines

- **Call hooks only at the top level** of your component or other hooks; never inside loops, conditionals, or nested functions.
- **Always use the getter function** to read state (e.g., `count()`), and the setter to update (e.g., `setCount(newValue)`).
- **Each hook call should be deterministic**—the order and number of calls must not change between renders.

---

## References

- [ascii-ui.nvim source: hooks directory](https://github.com/rcasia/ascii-ui.nvim/tree/main/lua/ascii-ui/hooks)
- [React documentation: Hooks](https://react.dev/reference/react/hooks)
- [ascii-ui.nvim README](https://github.com/rcasia/ascii-ui.nvim#readme)

---
