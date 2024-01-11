# Neovim Setup

Hit more bugs than I expected trying to get this setup.

## Bug 1
Elixirls from mason ended up not working well at all for me.

It would have some definitions for the standard library but would not be able to find anything in other files of my mix project.

## Solution

Build .elixir-ls myself.
https://github.com/elixir-lsp/elixir-ls is the source code and has instructions
```bash
mix deps.get
MIX_ENV=prod mix compile
MIX_ENV=prod mix elixir_ls.release2 -o <release_dir>
```

```lua
require('mason').setup({})
require('mason-lspconfig').setup({
    ensure_installed = { 'tsserver', 'eslint', 'lua_ls', 'rust_analyzer', 'gopls', 'golangci_lint_ls', 'bufls' },
    handlers = {
        lsp.default_setup,
        lua_ls = function()
            local lua_opts = lsp.nvim_lua_ls()
            require('lspconfig').lua_ls.setup(lua_opts)
        end,
    },
})

require('lspconfig').elixirls.setup {
    cmd = { "/Users/david/.elixir-ls/release/language_server.sh" },
    on_attach = on_attach
}

```

Some helpful additional tips are using these two commands from within VIM to see if an LSP is attached and see any error logs. The bug I was seeing when using mason's elixir lsp was that it would show an attached LSP, but wouldn't actually go to definition or provide autocompletes.

:LspInfo
:LspLog

## Bug 2

Wanted elixir formatting. Found 2 ways to do it.

In "after/plugin/lsp.lua" using `vim.lsp.buf.format()` was able to format the code.
Note: This required a working elixir-ls LSP to be attached
```lua
local on_attach = lsp.on_attach(function(client, bufnr)
    local opts = { buffer = bufnr, remap = false }
    -- see :help lsp-zero-keybindings
    -- to learn the available actions
    lsp.default_keymaps({ buffer = bufnr })

    vim.keymap.set("n", "<leader>ff", function() vim.lsp.buf.format() end, opts)
end)

```

Alternative way that works even without an LSP. Sending a command to mix format
```lua
-- mix format easily
vim.keymap.set("n", "<leader>ef", "<cmd>!mix format<CR>", { silent = true })
```

## Bug 3

Couldn't step into standard library code. Maybe not so much a bug as much as something I saw online that seemed really useful. Being able to jump into the source code makes it so much easier to learn from a project.

Especially since the Elixir source code is great + has really good documentation. In my opinion seeing the documentation and the implementation really helps me understand what the documentation is saying.


## Solution

Build elixir from scratch then rebuild .elixir-ls.

Unisntall elixir. For me this was

```bash
brew uninstall elixir
```

Elixir source and instructions: https://github.com/elixir-lang/elixir?tab=readme-ov-file#compiling-from-source
```bash
git clone https://github.com/elixir-lang/elixir.git
cd elixir
make
```

Add this to your .zshrc or .bashrc pointed at wherever you installed elixir.
```sh
export PATH="/Users/david/elixir/language_source/elixir/bin:$PATH"
```

Refresh your terminal
```bash
source ~/.zshrc
```

Rebuild elixir-ls as seen in #Bug 1. 

---

Reopen vim and you should be able to jump to definition of the Elixir source code!

---

It's a couple extra steps, but 100% feels worth it.
