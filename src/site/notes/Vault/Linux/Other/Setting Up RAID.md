---
{"dg-publish":true,"permalink":"/vault/linux/other/setting-up-raid/"}
---

```bash
# Clear the partitions on the existing drives
sudo dnf install wipe
sudo wipefs -a /dev/sda
sudo wipefs -a /dev/sdb
# Check the health of the drive
sudo smartctl -a /dev/sda | grep "PASSED"
sudo smartctl -a /dev/sdb | grep "PASSED"
# Open fdisk
sudo fdisk /dev/sda
1. Press `n` (new partition)
2. Press Enter (primary - default)
3. Press Enter (partition 1 - default)
4. Press Enter (default first sector)
5. Press Enter (default last sector - uses whole disk)
6. **When asked "Do you want to remove the signature?" - type `Y`** ← This is important!
7. Press `t` (change partition type)
8. Type `fd` (Linux raid autodetect)
9. Press `w` (write changes and exit)
# Repeat for disk 2
sudo fdisk /dev/sdb
# Check that it all worked
lsblk
# Install mdadm for RAID
sudo dnf install mdadm
# Create a RAID between the two
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sda1 /dev/sdb1
# Check the details of the raid (can take hours)
sudo mdadm --detail /dev/md0
cat /proc/mdstat
# Make the file system
sudo mkfs.exfat /dev/md0
# Create the mount directory & mount it
sudo mkdir /mnt/raid
sudo mount /dev/md0 /mnt/raid
# Make it perminant
sudo blkid /dev/md0
sudo nvim /etc/fstab
UUID=xxxx-xxxx /mnt/raid exfat defaults,uid=1000,gid=1000,umask=0022,nofail 0 0
sudo systemctl daemon-reload
sudo umount /mnt/raid
sudo mount -fav
```