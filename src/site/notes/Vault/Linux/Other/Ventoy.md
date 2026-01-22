---
{"dg-publish":true,"permalink":"/vault/linux/other/ventoy/"}
---

```zsh
wget https://github.com/ventoy/Ventoy/releases/download/v1.1.10/ventoy-1.1.10-linux.tar.gz
sha256sum ventoy-1.1.10-linux.tar.gz
tar -xzf ventoy-1.1.10-linux.tar.gz
cd ventoy-1.1.10
lsblk
sudo ./Ventoy2Disk.sh -i /dev/sdX
```