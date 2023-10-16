Date: 2023-02-12 - Time: 18:12
___
Tags: #MAIN 
___
# neovim_from_source
git clone https://github.com/neovim/neovim

sudo aptitude install gperf luajit libluajit-5.1-dev lua-mpack lua-lpeg libunibilium-dev libmsgpack-dev libtermkey-dev
sudo aptitude install ninja-build gettext libtool libtool-bin autoconf automake cmake g++ pkg-config unzip curl

mkdir .deps 
cd .deps
-- посмотреть ключи. например для установки в папку. /usr/bin/
-- make CMAKE_INSTALL_PREFIX=/full/path/
-- make install

cmake -G Ninja ../cmake.deps/ -DUSE_BUNDLED=OFF -DUSE_BUNDLED_LIBUV=ON -DUSE_BUNDLED_LUV=ON -DUSE_BUNDLED_LIBVTERM=ON -DUSE_BUNDLED_TS=ON
ninja
cd ..
mkdir build
cd build
cmake -G Ninja  ..
ninja
ninja install

--настроить линки.
ln -s /usr/local/bin/nvim /usr/local/bin/vim
update-alternatives --install /usr/bin/editor editor /usr/bin/nvim 100

## Установка lunarvim: (config)
pip обновить
nodejs обновить
python3-venv
npm 

bash <(curl -s https://raw.githubusercontent.com/lunarvim/lunarvim/master/utils/installer/install.sh)

## nviastra vim

Optional Requirements:
    ripgrep - live grep telescope search (<leader>fw)
    lazygit - git ui toggle terminal (<leader>tl or <leader>gg)
    go DiskUsage() - disk usage toggle terminal (<leader>tu)
    bottom - process viewer toggle terminal (<leader>tt)
    Python - python repl toggle terminal (<leader>tp)
    Node - node repl toggle terminal (<leader>tn)

```bash
git clone https://github.com/AstroNvim/AstroNvim ~/.config/nvim
nvim +PackerSync
```

Install LSP

Enter :LspInstall followed by the name of the server you want to install
Example: :LspInstall pyright
Install language parser

Enter :TSInstall followed by the name of the language you want to install
Example: :TSInstall python
Manage plugins

Run :PackerClean to remove any disabled or unused plugins
Run :PackerSync to update and clean plugins


___
###Zero-Links
[00_Editors](__Z_CORE/00_Editors.md)
___
###Links
