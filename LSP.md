c/cpp
aptitude install clangd-13
update-alternatives --install /usr/bin/clangd clangd /usr/bin/clangd-13 100
clangd --version

rust
On Linux to install the rust-analyzer binary into ~/.local/bin, these commands should work:
$ mkdir -p ~/.local/bin
$ curl -L https://github.com/rust-lang/rust-analyzer/releases/latest/download/rust-analyzer-x86_64-unknown-linux-gnu.gz | gunzip -c - > ~/.local/bin/rust-analyzer
$ chmod +x ~/.local/bin/rust-analyzer

Make sure that ~/.local/bin is listed in the $PATH variable and use the appropriate 