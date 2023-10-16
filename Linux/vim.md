настроить putty
настройка neovim 

cd ~/.config/nvim/

touch init.lua && \
mkdir ftplugin && \
mkdir lua && \
mkdir lua/config && \
touch lua/config/colorscheme.lua && \
touch lua/config/completition.lua && \
touch lua/config/fugitive.lua && \
touch lua/config/init.lua && \
touch lua/keymappings.lua && \
mkdir lua/lsp_lua && \
touch lua/lsp_lua/init.lua && \
touch lua/plugins.lua && lg\
touch lua/settings.lua && \
mkdir lua/utils && \
touch lua/utils/init.lua && \
mkdir plugin && \
touch plugin/packer_compiled.vim && \
touch plugin/telescope.vim



  Packs:
✓ Installed nvim-lua/plenary.nvim
✓ Installed tjdevries/nlua.nvim
✓ Installed tpope/vim-fugitive
✓ Installed nvim-telescope/telescope.nvim
✓ Installed neovim/nvim-lspconfig
✓ Installed nvim-lua/completion-nvim
✓ Installed tpope/vim-dispatch
✓ Installed sainnhe/gruvbox-material
✓ Installed nvim-lua/popup.nvim

LSP: 
lua-server:

mkdir -p /home/sasha/.cache/nvim/nlua/sumneko_lua &&\
cd /home/sasha/.cache/nvim/nlua/sumneko_lua &&\
git clone https://github.com/sumneko/lua-language-server &&\
cd lua-language-server &&\
git submodule update --init --recursive 

cd 3rd/luamake && compile/install.sh && cd ../.. && ./3rd/luamake/luamake rebuild
aptitude install clangd

###

### plugin
nvim/lua/plugins.lua
```
return require('packer').startup(function()

  -- Packer can manage itself as an optional plugin
  use {'wbthomason/packer.nvim', opt = true}

  -- Color scheme
  use { 'sainnhe/gruvbox-material' }

  -- Fuzzy finder
  use {
      'nvim-telescope/telescope.nvim',
      requires = {{'nvim-lua/popup.nvim'}, {'nvim-lua/plenary.nvim'}}
  }

  -- LSP and completion
  use { 'neovim/nvim-lspconfig' }
  use { 'nvim-lua/completion-nvim' }

  -- Lua development
  use { 'tjdevries/nlua.nvim' }


  -- Vim dispatch
  use { 'tpope/vim-dispatch' }

  -- Fugitive for Git
  use { 'tpope/vim-fugitive' }

  use {
       'kyazdani42/nvim-tree.lua',
        requires = 'kyazdani42/nvim-web-devicons'
  }

end)

```

#### смена хоткеев:
vim.api.nvim_set_keymap('n', '<Leader><Space>', '<cmd>set hlsearch!<CR>', { noremap = true, silent = true })
аналог -- :nnoremap <silent> <Leader><Space> :set hlsearch<CR>

```
-- vim.api.nvim_set_keymap('n', '<C-n>', '<cmd>NvimTreeToggle<CR>', { noremap = true, silent = true })
local utils = require('utils');
utils.map('n', '<Leader>e', '<cmd>NvimTreeToggle<CR>')

local tree_cb = require'nvim-tree.config'.nvim_tree_callback

vim.g.nvim_tree_side = 'left'
vim.g.nvim_tree_width = 30
vim.g.nvim_tree_ignore = { '.git', 'node_modules', '.cache' }
vim.g.nvim_tree_gitignore = 1
vim.g.nvim_tree_auto_open = 1
vim.g.nvim_tree_auto_close = 1
vim.g.nvim_tree_auto_ignore_ft = { 'startify', 'dashboard' }
vim.g.nvim_tree_quit_on_open = 1
vim.g.nvim_tree_follow = 1
vim.g.nvim_tree_indent_markers = 1
vim.g.nvim_tree_hide_dotfiles = 0
vim.g.nvim_tree_git_hl = 1
vim.g.nvim_tree_highlight_opened_files = 1
vim.g.nvim_tree_root_folder_modifier = ':~'
vim.g.nvim_tree_tab_open = 1
vim.g.nvim_tree_auto_resize = 1
vim.g.nvim_tree_disable_netrw = 0
vim.g.nvim_tree_hijack_netrw = 0
vim.g.nvim_tree_add_trailing = 1
vim.g.nvim_tree_group_empty = 1
vim.g.nvim_tree_lsp_diagnostics = 1
vim.g.nvim_tree_disable_window_picker = 1
vim.g.nvim_tree_hijack_cursor = 0
vim.g.nvim_tree_icon_padding = ' '
vim.g.nvim_tree_icon_padding = ' '
vim.g.nvim_tree_symlink_arrow = ' >> '
vim.g.nvim_tree_update_cwd = 1
vim.g.nvim_tree_respect_buf_cwd = 1


-- default mappings
vim.g.nvim_tree_bindings = {
      { key = {"<CR>", "o", "<2-LeftMouse>"}, cb = tree_cb("edit") },
      { key = {"<2-RightMouse>", "<C-]>"},    cb = tree_cb("cd") },
      { key = "<C-v>",                        cb = tree_cb("vsplit") },
      { key = "<C-x>",                        cb = tree_cb("split") },
      { key = "<C-t>",                        cb = tree_cb("tabnew") },
      { key = "<",                            cb = tree_cb("prev_sibling") },
      { key = ">",                            cb = tree_cb("next_sibling") },
      { key = "P",                            cb = tree_cb("parent_node") },
      { key = "<BS>",                         cb = tree_cb("close_node") },
      { key = "<S-CR>",                       cb = tree_cb("close_node") },
      { key = "<Tab>",                        cb = tree_cb("preview") },
      { key = "K",                            cb = tree_cb("first_sibling") },
      { key = "J",                            cb = tree_cb("last_sibling") },
      { key = "I",                            cb = tree_cb("toggle_ignored") },
      { key = "H",                            cb = tree_cb("toggle_dotfiles") },
      { key = "R",                            cb = tree_cb("refresh") },
      { key = "a",                            cb = tree_cb("create") },
      { key = "d",                            cb = tree_cb("remove") },
      { key = "r",                            cb = tree_cb("rename") },
      { key = "<C-r>",                        cb = tree_cb("full_rename") },
      { key = "x",                            cb = tree_cb("cut") },
      { key = "c",                            cb = tree_cb("copy") },
      { key = "p",                            cb = tree_cb("paste") },
      { key = "y",                            cb = tree_cb("copy_name") },
      { key = "Y",                            cb = tree_cb("copy_path") },
      { key = "gy",                           cb = tree_cb("copy_absolute_path") },
      { key = "[c",                           cb = tree_cb("prev_git_item") },
      { key = "]c",                           cb = tree_cb("next_git_item") },
      { key = "-",                            cb = tree_cb("dir_up") },
      { key = "s",                            cb = tree_cb("system_open") },
      { key = "q",                            cb = tree_cb("close") },
      { key = "g?",                           cb = tree_cb("toggle_help") },
};

```


