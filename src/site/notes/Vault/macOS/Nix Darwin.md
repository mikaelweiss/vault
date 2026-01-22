---
{"dg-publish":true,"permalink":"/vault/mac-os/nix-darwin/"}
---

Setup Instructions: https://mynixos.com/nix-darwin

```zsh
# Install with Nix Lix because vanila instalation is hard to uninstall
curl -sSf -L https://install.lix.systems/lix | sh -s -- install
nix-shell -p git neovim
ssh-keygen
cat .ssh/id_ed25519.pub
mkdir code
git clone git@github.com:MikaelWeiss/dotfiles.git
scutil --get LocalHostName
nvim flake.nix
# Add this
darwinConfigurations."YOUR-MAC-MINI-HOSTNAME" = nix-darwin.lib.darwinSystem }
  modules = [ configuration ];
};
sudo mkdir -p /etc/nix-darwin
sudo chown $(id -nu):$(id -ng) /etc/nix-darwin
sudo ln -s $(pwd)/flake.nix /etc/nix-darwin/flake.nix
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
sudo nix run nix-darwin/nix-darwin-25.11#darwin-rebuild -- switch
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git "${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k"

# Set up the flake ---- FIRST TIME NIX SETUP
cd /Users/mikaelweiss/code/dotfiles
mkdir nix-darwin
cd nix-darwin
nix flake init -t nix-darwin/nix-darwin-25.11
git add flake.nix
sed -i '' "s/simple/$(scutil --get LocalHostName)/" flake.nix
sudo mkdir -p /etc/nix-darwin
sudo chown $(id -nu):$(id -ng) /etc/nix-darwin
sudo ln -s /Users/mikaelweiss/code/dotfiles/nix-darwin/flake.nix /etc/nix-darwin/flake.nix
sudo nix run nix-darwin/nix-darwin-25.11#darwin-rebuild -- switch
# There will probably be a warning "warning: $HOME ('/Users/mikaelweiss') is not owned by you, falling back to the one defined in the 'passwd' file ('/var/root')" and you don't need to worry about it

# This is how you apply the changes to your system
sudo darwin-rebuild switch
nvim ~/.zshrc
alias nix-rebuild='sudo darwin-rebuild switch'
alias nix-config='nvim /Users/mikaelweiss/code/dotfiles/nix-darwin/flake.nix'
```
