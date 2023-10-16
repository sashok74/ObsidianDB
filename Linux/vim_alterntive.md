update-alternatives --config editor
There are 4 choices for the alternative editor (providing /usr/bin/editor).

  Selection    Path               Priority   Status
------------------------------------------------------------
* 0            /bin/nano           40        auto mode
  1            /bin/nano           40        manual mode
  2            /usr/bin/mcedit     25        manual mode
  3            /usr/bin/nvim       30        manual mode
  4            /usr/bin/vim.tiny   15        manual mode
  
update-alternatives --config view
There are 3 choices for the alternative view (providing /usr/bin/view).

  Selection    Path                      Priority   Status
------------------------------------------------------------
* 0            /usr/libexec/neovim/view   30        auto mode
  1            /usr/bin/mcview            25        manual mode
  2            /usr/bin/vim.tiny          15        manual mode
  3            /usr/libexec/neovim/view   30        manual mode  
  
update-alternatives --query  vim
Name: vim
Link: /usr/bin/vim
Status: auto
Best: /usr/bin/nvim
Value: /usr/bin/nvim  