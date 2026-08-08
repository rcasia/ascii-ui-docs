---
sidebar_position: 1
id: testing
title: Testing Overview
sidebar_label: Overview
description: Test your ascii-ui components with the built-in testing library.
tags: [testing, overview]
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

## Which Mode Should I Use?

- **Unit tests** are faster and don't require a Neovim window. Use them for most component testing.
- **E2E tests** are slower but test real keyboard interactions, cursor movement, and buffer behavior. Use them when you need to verify actual user interactions.

See the detailed guides:
- [Unit Testing](./unit.md) — Fast, isolated component tests
- [E2E Testing](./e2e.md) — Full integration tests with keyboard interactions

## Best Practices

1. **Prefer unit tests** — Use `testing.render()` for most tests. They're faster and don't require a Neovim window.

2. **Use E2E for interactions** — Use `testing_e2e.mount()` when testing keyboard navigation, cursor movement, or real buffer behavior.

3. **Query like a user** — Use `getByText()` to find elements the way a user would see them, not by internal IDs.

4. **Use `waitForText` for async** — When testing state changes or async operations, use `waitForText()` instead of `bufferContains()`.

5. **Assert on output, not implementation** — Test what the user sees (`hasLines`), not internal state.
