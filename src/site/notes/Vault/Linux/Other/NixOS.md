---
{"dg-publish":true,"permalink":"/vault/linux/other/nix-os/"}
---

To clean your Nix store:

**Basic garbage collection:**

```bash
nix-collect-garbage
```

**Delete old generations and collect garbage:**

```bash
nix-collect-garbage -d
```

**Delete generations older than X days:**

```bash
nix-collect-garbage --delete-older-than 30d
```

**If using flakes/new CLI:**

```bash
nix store gc
```

**Optimize (deduplicate) the store:**

```bash
nix-store --optimise
```

Run with `sudo` if you want to clean system profiles too.


# Install NixOS
```zsh
nix-shell -p git vim
mkdir code
cd code
git clone https://github.com/mikaelweiss/dotfiles
sudo rm /etc/nixos/configuration.nix
rm /home/mikaelweiss/code/dotfiles/nixos/hardware-configuration.nix
sudo mv /etc/nixos/hardware-configuration.nix /home/mikaelweiss/code/dotfiles/nixos/
sudo ln -s /home/mikaelweiss/code/dotfiles/nixos/configuration.nix /etc/nixos
sudo ln -s /home/mikaelweiss/code/dotfiles/nixos/hardware-configuration.nix /etc/nixos
sudo ln -s /home/mikaelweiss/code/dotfiles/nixos/flake.nix /etc/nixos
sudo ln -s /home/mikaelweiss/code/dotfiles/nixos/.zshrc /home/mikaelweiss
sudo ln -s /home/mikaelweiss/code/dotfiles/nixos/.p10k.zsh /home/mikaelweiss
sudo vim /etc/nixos/configuration.nix # Update hostname to match your hostname
sudo vim /etc/nixos/flake.nix # Update hostname to match your hostname
exit
sudo nixos-rebuild switch
```

> [!NOTE]
> If you want to change the hostname, you'll need to enter the directory of your flake.nix and rebuild there instead of from the /etc/nixos directory or it won't work
> ```
> cd code/dotfiles/nixos > sudo nixos-rebuild switch --flake .#charmander
> ```
