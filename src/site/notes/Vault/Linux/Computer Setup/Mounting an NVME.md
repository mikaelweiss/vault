---
{"dg-publish":true,"permalink":"/vault/linux/computer-setup/mounting-an-nvme/"}
---

```zsh
# Find
sudo lsblk
# This will remove everything:
sudo nvme format /dev/nvmexnx -s 1 --force
# Create a partition
sudo fdisk /dev/nvme1n1 # Type: n (new), p (primary), enter, enter, enter, w (write)
sudo mkfs.xfs /dev/nvmeXnXpX
sudo mkdir /mnt/nvme
sudo mount /dev/nvmeXnXpX /mnt/nvme
sudo blkid /dev/nvmeXnXpX # Copy the value
sudo nvim /etc/fstab
UUID=your-uuid /mnt/data xfs defaults,noatime 0 0
sudo systemctl daemon-reload
sudo umount /mnt/nvme
```