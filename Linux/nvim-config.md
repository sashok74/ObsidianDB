настроить putty
https://putty.org.ru/articles/putty-256-colors-and-themes.html
connection - data  - terminal-type screen-256color


настройка neovim 
cd ~/.config
mkdir -p nvim/lua/{settings,mappings,collorshemes-config,packer-config,nvim-tree-config}
cd nvim/

### список опций вима
  vim lua/settings/init.lua
  :help option-list
 
### основной инит.
  vim init.lua 
  -- указываем на папку с чатьсю конфига.
 
  
 
перечитывает конфиг :luafile %
 
### управление плагинами.
git clone --depth 1 https://github.com/wbthomason/packer.nvim ~/.local/share/nvim/site/pack/packer/start/packer.nvim  
сперва ставим этот плагин затием правим файлы
nvim/init.lua  
require("packer-config")

nvim/lua/packer-config/init.lua 
return require('packer').startup(function()  
   use 'wbthomason/packer.nvim'
end)
перегружаем конфиги -- :luafile %
загружаем плагины   -- :PackerSync  

ставим дальше все необходимое
для иконок не забываем про шрифты.
