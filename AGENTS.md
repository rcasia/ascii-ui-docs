# AGENTS.md

This file provides guidance to AI agents working on the ascii-ui documentation.

## Project Overview

This is a Docusaurus-based documentation site for [ascii-ui.nvim](https://github.com/rcasia/ascii-ui.nvim), a Neovim UI framework inspired by React.

## Build and Development Commands

```bash
# Install dependencies
npm install

# Start local development server (http://localhost:3000/ascii-ui-docs/)
npm start

# Build for production
npm run build

# Type check
npm run typecheck

# Serve production build locally
npm run serve
```

## Documentation Structure

- `docs/` - Main documentation files (Markdown/MDX)
- `docs/components/` - Component-specific documentation
- `docs/hooks/` - Hook API documentation
- `docs/testing/` - Testing library documentation
- `docs/viewports/` - Viewport implementation docs
- `blog/` - Blog posts (optional)

Sidebar is auto-generated from the `docs/` folder structure via `sidebars.ts`.

## Updating Documentation from Upstream

When updating docs based on changes to [ascii-ui.nvim](https://github.com/rcasia/ascii-ui.nvim):

1. **Check recent commits** in the upstream repo:
   ```bash
   cd /path/to/ascii-ui.nvim
   git log --oneline -20
   ```

2. **Review the diff** for relevant changes:
   - `lua/ascii-ui/` - Core framework code
   - `doc/ascii-ui.txt` - Vimdoc reference (source of truth for API)
   - `examples/` - Example usage patterns

3. **Key areas to update**:
   - `docs/blocks.md` - Segment/Bufferline API and Color class
   - `docs/mount.md` - Mount lifecycle and error handling
   - `docs/architecture.md` - Framework internals
   - `docs/testing/testing.md` - Testing library API
   - Component docs in `docs/components/`
   - Hook docs in `docs/hooks/`

4. **Code examples** should use the latest API patterns from upstream examples.

## Documentation Conventions

### Frontmatter

Every doc file must have frontmatter:

```yaml
---
sidebar_position: <number>
id: <optional-custom-id>
title: <page-title>
sidebar_label: <short-label>
description: <one-line-description>
tags: [<tag1>, <tag2>]
---
```

### Code Examples

- Use Lua code blocks with `lua` language tag
- Show complete, runnable examples when possible
- Use `local ui = require("ascii-ui")` at the top
- Follow the naming conventions from upstream examples

### Cross-References

- Use relative links for internal docs: `[Link Text](./other-doc.md)`
- Link to upstream repo: `https://github.com/rcasia/ascii-ui.nvim`
- Link to vimdocs: `https://github.com/rcasia/ascii-ui.nvim/blob/main/doc/ascii-ui.txt`

## Code Style

- **Language**: American English
- **Tone**: Technical, concise, example-driven
- **Headings**: Use `#`, `##`, `###` (avoid `####` when possible)
- **Code blocks**: Always specify language (`lua`, `bash`, etc.)
- **Line length**: Keep prose under 100 characters when practical

## Common Workflows

### Adding a New Doc Page

1. Create the Markdown file in the appropriate `docs/` subdirectory
2. Add required frontmatter with `sidebar_position`
3. Update `sidebars.ts` if using manual sidebar (currently auto-generated)
4. Test locally with `npm start`

### Updating API Documentation

1. Check the vimdoc in `doc/ascii-ui.txt` for the authoritative API reference
2. Update the corresponding doc page in `docs/`
3. Ensure code examples match current upstream patterns
4. Verify with `npm run build` (catches broken links)

### Documenting a New Feature

1. Review the upstream PR/commit for the feature
2. Understand the API from the source code and tests
3. Write conceptual explanation + API reference + example
4. Add to appropriate section (or create new page if needed)

## Verification Checklist

Before committing documentation changes:

- [ ] `npm run build` passes (no broken links or markdown errors)
- [ ] `npm run typecheck` passes (TypeScript config is valid)
- [ ] Code examples are syntactically correct Lua
- [ ] Cross-references use correct relative paths
- [ ] Frontmatter is complete and accurate
- [ ] Tested locally with `npm start` (visual check)

## Key Concepts to Understand

- **Components**: Created with `ui.createComponent(name, render_fn)`
- **Hooks**: `useState`, `useEffect`, `useReducer`, `useInterval`, `useTimeout`
- **Blocks**: Low-level `Segment` and `Bufferline` primitives
- **Viewports**: Render targets (Neovim window, stdout, custom)
- **Fiber reconciler**: Internal tree-diffing algorithm (inspired by React)
- **Event bus**: Per-mount isolated event system for state changes

## Related Repositories

- **Documentation site**: https://github.com/rcasia/ascii-ui-docs (this repo)
- **Framework source**: https://github.com/rcasia/ascii-ui.nvim
- **Examples**: https://github.com/rcasia/ascii-ui.nvim/tree/main/examples
