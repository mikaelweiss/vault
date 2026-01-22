---
{"dg-publish":true,"permalink":"/vault/linux/computer-setup/nix-os/setting-up-nix-os/"}
---

# Opening on another computer

```zsh
cd /etc/nixos
nix-shell -p neovim
nvim configuration.nix
# Update hostName to whatever you want
# Got to the environment.systemPackages = with pkgs line
# add
neovim
git
# Replace the line "services.openssh.enable = true;" with:
services.openssh = {
  enable = true;
  settings = {
    PasswordAuthentication = true;
  };
};
# :wq
cd
exit # exits the nix-shell
sudo nixos-rebuild switch
sudo reboot # To reset the hostname
hostname -I # to get your private ip address
# Move to your computer of choice
ssh-copy-id 192.168.0.xxx
ssh 192.168.0.xxx
# You're in
# You can also run
sudo tailscale up
# And then connect tailscale
```

> [!NOTE]
> If you want to change the hostname, you'll need to enter the directory of your flake.nix and update it > ``` > cd code/dotfiles/nixos > sudo nixos-rebuild switch --flake .#charmander > ```
## More Setup
```zsh
ssh-keygen # then enter enter enter to select all the defaults
cat ~/.ssh/id_ed25519.pub
# Copy to github https://github.com/settings/keys
mkdir code
cd code
git clone git@github.com:MikaelWeiss/dotfiles.git
cd
mkdir ~/.config
sudo mkdir /root/.config
ln -s /home/mikaelweiss/code/dotfiles/nixos/.zshrc /home/mikaelweiss/
ln -s /home/mikaelweiss/code/dotfiles/nixos/.p10k.zsh /home/mikaelweiss/
ln -s /home/mikaelweiss/code/dotfiles/nixos/nvim /home/mikaelweiss/.config/nvim
ln -s /home/mikaelweiss/code/dotfiles/ghostty /home/mikaelweiss/.config/ghostty
ln -s /home/mikaelweiss/code/dotfiles/hypr /home/mikaelweiss/.config/hypr
ln -s /home/mikaelweiss/code/dotfiles/waybar /home/mikaelweiss/.config/waybar
sudo ln -s /home/mikaelweiss/code/dotfiles/nixos/nvim /root/.config/nvim
```

## Set up Flakes
```zsh
# Enable flakes
sudo nvim /etc/nixos/configuration.nix
nix.settings.experimental-features = [ "nix-command" "flakes" ];
# Make sure environment.systemPackages has git since flakes require git
:wq
sudo nixos-rebuild switch
cd code/dotfiles
mkdir nixos
cd nixos
sudo nvim flake.nix
```

Add this to `flake.nix`:

```nix
{
  description = "My NixOS config";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-25.11";
  };

  outputs = { self, nixpkgs, ... }: {
    nixosConfigurations.YOURHOSTNAME = nixpkgs.lib.nixosSystem {
      system = "x86_64-linux";
      modules = [ ./configuration.nix ];
    };
  };
}
```

Replace `YOURHOSTNAME` with your actual hostname (check with `hostname`).

```zsh
git add .
cd
sudo ln -s /home/mikaelweiss/code/dotfiles/nixos/flake.nix /etc/nixos/
sudo nixos-rebuild switch
sudo mv /etc/nixos/configuration.nix /home/mikaelweiss/code/dotfiles/nixos/
sudo mv /etc/nixos/hardware-configuration.nix /home/mikaelweiss/code/dotfiles/nixos/
sudo ln -s /home/mikaelweiss/code/dotfiles/nixos/configuration.nix /etc/nixos
sudo ln -s /home/mikaelweiss/code/dotfiles/nixos/hardware-configuration.nix /etc/nixos
cd code/dotfiles

git add .
git commit -m "Added nixos stuff"
```

# More configurations
```zsh
# Git
programs.git = {
  enable = true;
  config = {
    user = {
      email = "email@email.com";
      name = "Mikael Weiss";
    };
  };
};

# ZSH
users.users.yourusername = {
  shell = pkgs.zsh;
};

programs.zsh = {
  enable = true;
  promptInit = "source ${pkgs.zsh-powerlevel10k}/share/zsh-powerlevel10k/powerlevel10k.zsh-theme";

  ohMyZsh = {
    enable = true;
    plugins = [ "git" "sudo" "rsync" "systemd" ];
  };
};

environment.variables = {
  TERM = "xterm-256color";
  EDITOR = "nvim";
};

# Make it so that if you make new users, they default to zsh
users.defaultUserShell = pkgs.zsh;

# Passwordless sudo
security.sudo.wheelNeedsPassword = false;

# Set up tailscale
services.tailscale.enable = true;
sudo tailscale up # To authenticate

# Packages
neovim
gcc # Dependancy of LazyVim
vim
git
oh-my-zsh # Better zsh
lazygit # Better git
zoxide # Better cd
zsh-powerlevel10k # ZSH theme
meslo-lgs-nf # Nerd font
fzf # Fuzy find files
btop # Visualization of hardware status
yazi # File browser
restic # Backup software
elixir
erlang
postgresql
ripgrep
unzip #Neovim dependancy
claude-code
tmux # Split screen and windows in the terminal
tldr # Run tldr tmux to see the tldr for the tmux docs
# Hyprland tools
hyprland
wofi
waybar
hyprpaper
# Apps
ghostty
signal-desktop
tailscale

# Other
programs = {
  nano.enable = false;
  neovim.enable = true;
};


```

## Set up samba
```zsh
cd
mkdir share

{
  services.samba = {
    enable = true;
    openFirewall = true;
    settings = {
      global = {
        workgroup = "SAMBA";
        security = "user";
        "passdb backend" = "tdbsam";
        "server min protocol" = "SMB3";
        "smb encrypt" = "required";
        "hosts allow" = "192.168.0.0/24 100.64.0.0/10 EXCEPT 192.168.0.xxx 100.xxx.xxx.xx";
        "hosts deny" = "ALL";
        "load printers" = "no";
        printing = "bsd";
        "printcap name" = "/dev/null";
        "disable spoolss" = "yes";
      };
      share = {
        comment = "My Share";
        path = "/home/mikaelweiss/share";
        writeable = "yes";
        browseable = "yes";
        public = "no";
        "valid users" = "mikaelweiss";
        "create mask" = "0644";
        "directory mask" = "0755";
      };
    };
  };

  # Trust tailscale interface
  networking.firewall.trustedInterfaces = [ "tailscale0" ];
}

:wq
sudo smbpasswd -a mikaelweiss
```

## Mounting drives
```zsh
sudo mkdir -p /mnt/backup
# Connect the drives
sudo mdadm --assemble --scan
# Get the details to put in the config file
sudo mdadm --detail --scan

  # Filesystem stuff
  fileSystems."/mnt/backup" = {
    device = "/dev/disk/by-uuid/xxxxxx";
    fsType = "exfat";
    options = [ "defaults" "nofail" "uid=1000" "gid=100" "umask=0022" ];
  };

  # Enable software RAID support
  boot.swraid = {
    enable = true;
    mdadmConf = ''
      The stuff from that step earlier
    '';
  };

  # Mount the RAID array
  fileSystems."/mnt/raid" = {
    device = "/dev/md0";
    fsType = "exfat";
    options = [ "defaults" "nofail" "uid=1000" "gid=100" "umask=0022" ];
  };
```

## Restic restore and setup
```zsh
ssh elm
cat .ssh/id_ed25519.pub
ssh oak
sudo nvim .ssh/authorized_keys

sudo mkdir -p /etc/restic
sudo nvim /etc/restic/password
sudo chown mikaelweiss /etc/restic/password
sudo chmod 600 /etc/restic/password

restic -r /mnt/backup/share-backup restore latest --target / --password-file='/etc/restic/password'

restic -r /mnt/backup/minecraft-backup restore latest --target / --password-file='/etc/restic/password'

nix-config

{ config, pkgs, ... }:

{
  environment.systemPackages = [ pkgs.restic ];

  # Share backup service
  systemd.services.share-backup = {
    description = "Restic backup of share folder";
    after = [ "network-online.target" ];
    wants = [ "network-online.target" ];
    serviceConfig = {
      Type = "oneshot";
      User = "mikaelweiss";
      Nice = 19;
      IOSchedulingClass = "idle";
    };
    environment.RESTIC_PASSWORD_FILE = "/etc/restic/password";
    path = [ pkgs.restic pkgs.openssh ];
    script = ''
      restic -r /mnt/backup/share-backup backup /home/mikaelweiss/share
      restic -r sftp:mikaelweiss@oak:/home/mikaelweiss/backups/share-backup backup /home/mikaelweiss/share
      restic -r /mnt/backup/share-backup forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune
      restic -r sftp:mikaelweiss@oak:/home/mikaelweiss/backups/share-backup forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune
    '';
  };

  systemd.timers.share-backup = {
    wantedBy = [ "timers.target" ];
    timerConfig = {
      OnCalendar = "daily";
      RandomizedDelaySec = "15m";
      Persistent = true;
    };
  };

  # Minecraft backup service
  systemd.services.minecraft-backup = {
    description = "Restic backup of minecraft server data";
    after = [ "network-online.target" ];
    wants = [ "network-online.target" ];
    serviceConfig = {
      Type = "oneshot";
      User = "mikaelweiss";
      Nice = 19;
      IOSchedulingClass = "idle";
    };
    environment.RESTIC_PASSWORD_FILE = "/etc/restic/password";
    path = [ pkgs.restic pkgs.openssh pkgs.podman ];
    script = ''
      restic -r /mnt/backup/minecraft-backup backup /home/mikaelweiss/.minecraft-server/data
      restic -r sftp:mikaelweiss@oak:/home/mikaelweiss/backups/minecraft-backup backup /home/mikaelweiss/.minecraft-server/data
      restic -r /mnt/backup/minecraft-backup forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune
      restic -r sftp:mikaelweiss@oak:/home/mikaelweiss/backups/minecraft-backup forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune
    '';
  };

  systemd.timers.minecraft-backup = {
    wantedBy = [ "timers.target" ];
    timerConfig = {
      OnCalendar = "daily";
      RandomizedDelaySec = "15m";
      Persistent = true;
    };
  };
}
```
## Set up minecraft again
```zsh
# Restore backup
cd
sudo nvim /etc/restic/password
sudo chown mikaelweiss:mikaelweiss /etc/restic/password
sudo chmod 600 /etc/restic/password

virtualisation.podman.enable = true;

virtualisation.oci-containers = {
  backend = "podman";
  containers.minecraft-server = {
    image = "docker.io/itzg/minecraft-server:latest";
    ports = [ "25565:25565" ];
    volumes = [ "/home/mikaelweiss/.minecraft-server/data:/data" ];
	autoStart = true;
    environment = {
      EULA = "TRUE";
      TYPE = "FABRIC";
      MEMORY = "2G";
	  VERSION = "1.21.10";
      UID = "1000";
      GID = "100";
      REMOVE_OLD_MODS = "FALSE";
    };
  };
};
```

## Cockpit
```zsh
sudo nvim /etc/nixos/configuration.nix
# Update the inputs at the top to include lib
{ config, pkgs, lib, ... };
# Add 'cockpit' to your systemPackages array
# Add this:
  # Cockpit
  services.cockpit = {
    enable = true;
    port = 9090;
    openFirewall = true;
    settings = {
      WebService = {
        AllowUnencrypted = true;
        Origins = lib.mkForce "https://localhost:9090 http://localhost:9090 https://elm:9090 http://elm:9090 https://100.xx.xxx.xx:9090 http://100.xx.xxx.xx:9090 https://192.168.0.xxx http://192.168.0.xxx";
      };
    };
  };
```

## SDDM
```zsh
services.displayManager.sddm = {
  enable = true;
  wayland.enable = true;
  theme = "sddm-astronaut-theme";
  extraPackages = [ pkgs.sddm-astronaut ];
};
programs.hyprland.enable = true;
```
