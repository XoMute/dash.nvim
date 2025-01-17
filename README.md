![Build](https://github.com/mrjones2014/dash.nvim/actions/workflows/lint-check-test.yml/badge.svg) [![Lua](https://img.shields.io/badge/Made%20With-Lua-blue)](https://www.lua.org) [![Rust](https://img.shields.io/badge/Made%20With-Rust-red)](https://www.rust-lang.org)

# Dash.nvim

Query [Dash.app](https://kapeli.com/dash) within Neovim with a Telescope picker!

![demo](./images/demo.gif)

Note: Dash is a Mac-only app, so you'll only find this plugin useful on Mac.

## Usage

### Editor Commands

This plugin has two editor commands, `:Dash` and `:DashWord`, each of which accept a bang (`!`). By default, it will
search Dash.app with keywords based on config (see `file_type_keywords` in [configuration](#configuration)). The bang (`!`)
will search without this keyword filtering.

`:Dash [query]` will open the Telescope picker, and if `[query]` is passed, it will pre-fill the prompt with `[query]`.

`:DashWord` will open the Telescope picker and pre-fill the prompt with the word under the cursor.

### Lua API

The public API consists of two main functions.

```lua
-- See lua/dash/utils/config.lua for full DashConfig type definition
-- Also described in configuration section below
---@param config DashConfig
require('dash').setup(config)
```

```lua
---@param bang boolean @bang searches without any filtering
---@param initial_text string @pre-fill text into the telescope picker
require('dash').search(bang, initial_text)
```

## Install

Using Packer:

```lua
use({ 'mrjones2014/dash.nvim', requires = { 'nvim-telescope/telescope.nvim' } })
```

## Configuration

`dash.nvim` can be configured in your Telescope config. Options and defaults are described below:

```lua
require('telescope').setup({
  extensions = {
    dash = {
      -- configure path to Dash.app if installed somewhere other than /Applications/Dash.app
      dash_app_path = '/Applications/Dash.app',
      -- debounce while typing, in milliseconds, defaults to 750 (0.75 seconds)
      debounce = 750,
      -- map filetype strings to the keywords you've configured for docsets in Dash
      -- setting to false will disable filtering by filetype for that filetype
      -- filetypes not included in this table will not filter the query by filetype
      -- check lua/dash/utils/config.lua to see all defaults
      -- the values you pass for file_type_keywords are merged with the defaults
      -- to disable filtering for all filetypes,
      -- set file_type_keywords = false
      file_type_keywords = {
        dashboard = false,
        NvimTree = false,
        TelescopePrompt = false,
        terminal = false,
        packer = false,
        -- a table of strings will search on multiple keywords
        javascript = { 'javascript', 'nodejs' },
        typescript = { 'typescript', 'javascript', 'nodejs' },
        typescriptreact = { 'typescript', 'javascript', 'react' },
        javascriptreact = { 'javascript', 'react' },
        -- you can also do a string, for example,
        -- bash = 'sh'
      },
    }
  }
})
```

If you notice an issue with the default `file_type_keywords` or would like a new filetype added, please file an issue or submit a PR!

## Rust Backend

If you'd like to use the Rust backend directly to build something else, see [BACKEND.md](./BACKEND.md).

---

## Contributing

### Git Hooks

If you plan on changing Rust code, you will need to install the git hooks via `make install-hooks`.
The git hooks require you have a Rust toolchain installed. You can install a Rust toolchain from
[rustup.rs](https://rustup.rs).

### Running Tests

This uses [busted](https://github.com/Olivine-Labs/busted), [luassert](https://github.com/Olivine-Labs/luassert) (both through
[plenary.nvim](https://github.com/nvim-lua/plenary.nvim)) and [matcher_combinators](https://github.com/m00qek/matcher_combinators.lua) to
define tests in `spec/` directory. These dependencies are required only to run
tests, that's why they are installed as git submodules.

To run tests, run `make test`. This runs tests in Neovim with a minimal profile,
[spec.vim](./spec/spec.vim). This runs Neovim with only this plugin, and the testing dependencies.

If you have [entr(1)](https://eradman.com/entrproject/) installed, you can run the tests in watch mode
using `make watch`.

### Code Style

Use `snake_case` for everything. Ensure you use [EmmyLua Annotations](https://github.com/sumneko/lua-language-server/wiki/EmmyLua%2DAnnotations)
for any public-facing API, and optionally for non-public functions, if the function is non-trivial or the types are not obvious.
Other than that, running `luacheck` and `stylua` should cover it.
