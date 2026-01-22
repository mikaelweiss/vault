---
{"dg-publish":true,"permalink":"/vault/linux/computer-setup/raspberry-pi/"}
---

Download the installer
https://www.raspberrypi.com/software/

set username and password and locale
Enable SSH and add my public key
sound when done, disable telemetry

curl -fsSL https://tailscale.com/install.sh | sh
### Clone dotfiles
mkdir code
cd code
git clone https://github.com/mikaelweiss/dotfiles
### Set up NeoVim
git clone https://github.com/neovim/neovim
cd neovim
make CMAKE_EXTRA_FLAGS="-DCMAKE_INSTALL_PREFIX=$HOME/neovim"
git checkout stable 
sudo make install
update .bashrc:
```
export PATH="$HOME/neovim/bin:$PATH"
```

sudo apt install neovim
mkdir .config
ln -s /home/mikaelweiss/code/dotfiles/nvim /home/mikaelweiss/.config/nvim
nvim

### Set up terminal
update .bashrc
export TERM=xterm-256color
sudo apt install zoxide
curl -sS https://starship.rs/install.sh | sh
- Add 'eval "$(starship init bash)"'
- Add 'eval "$(zoxide init bash)"'
source .bashrc
nvim .config/starship.toml
```
add_newline = true
command_timeout = 200
format = "[$directory$git_branch]($style)$character"
```

### Setting up wifi
raspi-config

#### Other stuff to install
fzf
yazi
### Yazi
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup update
git clone https://github.com/sxyazi/yazi.git
cd yazi
cargo build --release --locked
mv target/release/yazi target/release/ya /usr/local/bin/

#### btop
sudo apt install btop