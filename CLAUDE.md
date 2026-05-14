# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A personal Neovim configuration based on [kickstart.nvim](https://github.com/nvim-lua/kickstart.nvim). It is a starting point, not a distribution — the intent is to be readable and directly editable rather than abstracted.

## Code Style

Lua code is formatted with **stylua**. Configuration is in `.stylua.toml`:
- Column width: 160
- Indent: 2 spaces
- Quote style: AutoPreferSingle

CI (`.github/workflows/stylua.yml`) checks formatting on PRs. To check locally:

```sh
stylua --check init.lua lua/
```

To format:

```sh
stylua init.lua lua/
```

## Architecture

### Entry Point

`init.lua` is a single ~1,100-line file that contains the entire core configuration. It is intentionally monolithic and well-commented. Sections in order:

1. **Git root detection** — changes `cwd` to the git root on startup
2. **Build system detection** — sets `makeprg` to `ninja` if `build.ninja` exists, otherwise `make`
3. **Options** — editor settings including custom C indentation (`cino`)
4. **Keymaps** — `<space>` is the leader key; `<C-h/j/k/l>` for split navigation
5. **Autocommands** — yank highlight, cursor restore, `.test` → SQL filetype
6. **Plugin bootstrap** — lazy.nvim is auto-installed from GitHub if missing
7. **Plugin specs + configs** — all plugin configuration lives inline in the spec table

### Plugin Management

**[lazy.nvim](https://github.com/folke/lazy.nvim)** manages plugins. Lock file: `lazy-lock.json`.

- Run `:Lazy` inside Neovim to open the plugin manager UI
- Run `:Lazy update` to update all plugins
- Run `:Lazy sync` to install/clean/update to match the lock file

### Optional Plugins

`lua/kickstart/plugins/` contains optional plugin files that are **not loaded by default**. To enable one, uncomment its entry near the bottom of `init.lua` (around line 1053–1058):

```lua
-- { import = 'kickstart.plugins.autopairs' },
-- { import = 'kickstart.plugins.neo-tree' },
-- { import = 'kickstart.plugins.debug' },
```

Available optional plugins: `autopairs`, `debug` (DAP/Delve), `gitsigns`, `indent_line`, `lint`, `neo-tree`.

### Custom Plugins

Add personal plugins to `lua/custom/plugins/init.lua` (currently an empty stub). This file is always imported.

### LSP

Configured via **mason.nvim** + **nvim-lspconfig**. Currently enabled servers:

- `clangd` — C/C++/CUDA/Proto (with custom filetypes)
- `cmake` — CMake
- `lua_ls` — Lua

To add a new LSP server, add it to the `servers` table inside the `mason-lspconfig` setup block in `init.lua`.

### Formatting

**conform.nvim** handles formatting with LSP fallback. Format-on-save is explicitly **disabled for C/C++** files. `stylua` is configured for Lua files.

## Key Custom Behaviors

- **`:Checkmake`** — user command that prints the current `makeprg` setting (useful to verify ninja vs make detection)
- **`.test` files** — treated as SQL syntax
- **Solarized** colorscheme (`nvim-solarized-lua`)
