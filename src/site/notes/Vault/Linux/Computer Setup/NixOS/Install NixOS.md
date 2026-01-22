---
{"dg-publish":true,"permalink":"/vault/linux/computer-setup/nix-os/install-nix-os/"}
---

Prepare your computer for a migration
You can probably just ask Claude Code what you should do to prepare to migrate as well, but here's a starter list:
```zsh
# GPG keys if you have any ~/.gnupg/
# Document your current setup
dnf list intalled > ~/fedora-packages.txt # You can do this if you want, but I prefer to install things as I need them to make sure I only install what I need/know why I installed it
# Capture your current partition layout
lsblk -f > ~/disk-layout.txt
# Export any important service configs you may have made
ls /etc/systemd/system
# Back up minecraft
sudo systemctl start minecraft-backup.service
sudo systemctl start share-backup.service
```

Go to https://nixos.org/download/#nixos-iso and scroll down to "NixOS: The Linux Distrobution" and download the graphical installer for x86 (or arm if your computer has arm architecture)
The difference between the graphical installer and the minimal is that the graphical will have a gui to help walk you through the install process and minimal won't. You'd have to do everything manually with minimal.

```zsh
cd desktop # Or wherever you downloaded the iso to
sha256sum nixos-graphical-25.11.1948.c6f52ebd45e5-x86_64-linux.iso
# click SHA-256 on the link from earlier next to the iso you downloaded to make sure they match
```
I'm using a ventoy USB, so I put my iso in there, but you can also use the terminal or belenaetcher to flash a USB stick with just NixOS
For me, the iso was about 4GB
Plug the flashed USB into your computer
Power off
Power on and click F10 as it turns on to open the setup menu
Security > Secure Boot Configuration > uncheck "Secure Boot"
Advanced > Boot Options > uncheck "Fast Boot"
If you see settings for "Secure Boot" turn that off too
Main > Save Changes and Exit
Yes
The computer will show a screen asking you to verify that you want to turn off Secure Boot
> Note: Make sure you use the number keys above they keyboard and NOT the number pad
> You can also click "num lock" on the number pad and type the numbers
> Either way, you must make sure that you see the numbers under it
> I noticed that in the setup menu you can toggle a setting to have numlock on by default, but I find it simpler to just use the number keys above the letters and not the numpad

Power off
Power on and click F9 to enter the Boot Menu
Select the USB
If using Ventoy, select the iso you just installed and boot normally
Select a installer GNOME or Plasma (whichever desktop environment you prefer, it makes no difference)
Go through the install process
Select Use the same password for the administrator account (if that's what you want)
Select desktop environment (I chose "No Desktop" I'll install later)
Check allow unfree software




  What You Need to Do

  For the RAID, secondary NVMe, and external drive - nothing special. They'll survive the install untouched as long as you:

  1. Only format nvme1n1 during NixOS installation
  2. After install, add mount entries to your NixOS config:

fileSystems."/mnt/raid" = {}
    device = "/dev/mdX";
    fsType = "exfat";
  };

  fileSystems."/mnt/nvme" = {
    device = "/dev/nvmeXnXpX";
    fsType = "xfs";
  };

  # External drive - optional, can mount manually
  fileSystems."/mnt/backup" = {
    device = "/dev/disk/by-uuid/xxxx-xxxx";
    fsType = "exfat";
    options = [ "nofail" ];  # Don't fail boot if unplugged
  };

  3. For the RAID array, NixOS needs mdadm enabled:

  boot.swraid.enable = true;
  boot.swraid.mdadmConf = "ARRAY /dev/md0 UUID=xxxxx-xxxx-xxxx-xxxx-xxxxx";

  One Recommendation

  Before installing, copy anything important from your boot drive to /mnt/raid or /mnt/backup:

  cp -r ~/code/dotfiles /mnt/backup/
  cp -r ~/.ssh /mnt/backup/

  That way your backups are on drives that won't be touched during installation.