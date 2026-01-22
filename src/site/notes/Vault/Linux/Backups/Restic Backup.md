---
{"dg-publish":true,"permalink":"/vault/linux/backups/restic-backup/"}
---

TLDR;
```bash
ssh elm
cat .ssh/id_ed25519.pub
ssh oak
sudo nvim .ssh/authorized_keys
# Copy over the key
## Jump to step 4 ##
```


This guide covers setting up automated restic backups on **elm** to back up `/home/mikaelweiss/share` (your SMB share) to two destinations:

1. **Local**: External hard drive at `/mnt/backup`
2. **Remote**: Oak server via Tailscale SSH

---

## Prerequisites

### Install restic on elm and oak

```bash
sudo dnf install restic
```

---

## 1. Set Up SSH Keys for Password-less Access to Oak

<aside>
💡

Once oak moves to another network, use its Tailscale IP or MagicDNS name (e.g., `oak` or [`oak.tailnet-name.ts.net`](http://oak.tailnet-name.ts.net)). Tailscale handles the routing automatically.

</aside>

---

## 2. Create the Password File

Create a secure password file that restic will use to encrypt your backups. Run this on both computers.

```bash
sudo mkdir -p /etc/restic
sudo nvim /etc/restic/password
sudo chown mikaelweiss:mikaelweiss /etc/restic/password
sudo chmod 600 /etc/restic/password
```

<aside>
⚠️

**Important**: Use the **same password** for both repositories if you want consistent encryption. Store this password somewhere safe — losing it means losing access to your backups forever.

</aside>

---

## 3. Initialize the Restic Repositories

### Local repository (external hard drive)

```bash
restic init --repo /mnt/backup/restic --password-file /etc/restic/password
```

### Remote repository (oak via SFTP)

First, create the backup directory on oak:

```bash
ssh mikaelweiss@oak
mkdir -p /home/mikaelweiss/backups/share-backup
mkdir -p /home/mikaelweiss/backups/minecraft-backup
```

Then initialize the repository. Run this on elm.

```bash
restic init --repo sftp:mikaelweiss@oak:/home/mikaelweiss/backups/share-backup --password-file /etc/restic/password
restic init --repo /mnt/backup/share-backup --password-file /etc/restic/password

restic init --repo sftp:mikaelweiss@oak:/home/mikaelweiss/backups/minecraft-backup --password-file /etc/restic/password
restic init --repo /mnt/backup/minecraft-backup --password-file /etc/restic/passsword
```

When you mount the external drive mount with the owner of the current user/group (mikaelweiss):

```jsx
sudo mount -o uid=$(id -u),gid=$(id -g) /mnt/backup
```

Make it permanent by updating `/etc/fstab`

```jsx
UUID=xxxx-xxxx /mnt/backup exfat uid=xxxx,gid=xxxx,nofail 0 0
```

(get the uid and guid by running `id -u` and `id -g` )

---

## 4. Create the systemd Services

Create `/etc/systemd/system/share-backup.service`:

```bash
[Unit]
Description=Restic backup of share folder
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
User=mikaelweiss
Nice=19
IOSchedulingClass=idle
Environment="RESTIC_PASSWORD_FILE=/etc/restic/password"

# Backup to local repo
ExecStart=/usr/bin/restic -r /mnt/backup/share-backup backup /home/mikaelweiss/share
# Backup to remote repo
ExecStart=/usr/bin/restic -r sftp:mikaelweiss@oak:/home/mikaelweiss/backups/share-backup backup /home/mikaelweiss/share

# Cleanup local repo - keep 7 days
ExecStart=/usr/bin/restic -r /mnt/backup/share-backup forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune
# Cleanup remote repo - keep 7 days
ExecStart=/usr/bin/restic -r sftp:mikaelweiss@oak:/home/mikaelweiss/backups/share-backup forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune

[Install]
WantedBy=multi-user.target
```

create `/etc/systemd/system/minecraft-backup.service`

```bash
[Unit]
Description=Restic backup of minecraft server data folder
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
User=mikaelweiss
Nice=19
IOSchedulingClass=idle
Environment="RESTIC_PASSWORD_FILE=/etc/restic/password"

# Backup to local repo
ExecStart=/usr/bin/podman unshare /usr/bin/restic -r /mnt/backup/minecraft-backup backup /home/mikaelweiss/.minecraft-server/data --password-file /etc/restic/password
# Backup to remote repo
ExecStart=/usr/bin/restic -r sftp:mikaelweiss@oak:/home/mikaelweiss/backups/minecraft-backup copy --from-repo /mnt/backup/minecraft-backup --password-file /etc/restic/password --from-password-file /etc/restic/password

# Cleanup local repo - keep 7 days
ExecStart=/usr/bin/restic -r /mnt/backup/minecraft-backup forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune
# Cleanup remote repo - keep 7 days
ExecStart=/usr/bin/restic -r sftp:mikaelweiss@oak:/home/mikaelweiss/backups/minecraft-backup forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune

[Install]
WantedBy=multi-user.target
```

---

## 6. Create the systemd Timer

Create `/etc/systemd/system/share-backup.timer` :

```bash
[Unit]
Description=Run restic share backup daily

[Timer]
OnCalendar=daily
RandomizedDelaySec=15m
Persistent=true

[Install]
WantedBy=timers.target
```

Create `/etc/systemd/system/minecraft-backup.timer` :

```bash
minecraft[Unit]
Description=Run restic  backup daily

[Timer]
OnCalendar=daily
RandomizedDelaySec=15m
Persistent=true

[Install]
WantedBy=timers.target
```

---

## 7. Enable and Start the Timer

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now share-backup.timer
sudo systemctl enable --now minecraft-backup.timer
```

### Verify the timer is active

```bash
sudo systemctl list-timers | grep share-backup
```

### Run a manual backup to test

```bash
sudo systemctl start minecraft-backup.service
sudo systemctl start share-backup.service
```

### Check the logs

```bash
sudo journalctl -u share-backup.service -f
sudo journalctl -u minecraft-backup.service -f
```

---

## Useful Commands

### List snapshots

```bash
# Local
restic snapshots --repo /mnt/backup/share-backup --password-file /etc/restic/password

# Remote
restic snapshots --repo sftp:mikaelweiss@oak:/home/mikaelweiss/backups/share-backup --password-file /etc/restic/password
```

### Restore files

```bash
# Restore entire snapshot to a directory
restic restore latest --repo /mnt/backup/restic --password-file /etc/restic/password --target /tmp/restore

# Restore specific file
restic restore latest --repo /mnt/backup/restic --password-file /etc/restic/password --target /tmp/restore --include "/home/mikaelweiss/share/specific-file.txt"
```

### Check repository integrity

```bash
restic check --repo /mnt/backup/share-backup --password-file /etc/restic/password
```

### Mount a snapshot (browse backups like a filesystem)

```bash
mkdir -p /tmp/restic-mount
restic mount /tmp/restic-mount --repo /mnt/backup/restic --password-file /etc/restic/password
```

---

## Tailscale Considerations

<aside>
🔐

**When oak moves to another network:**

- Tailscale will automatically handle the connection via its mesh VPN
- The SFTP connection string (`sftp:mikaelweiss@oak:...`) will continue to work as long as both elm and oak are on the same Tailnet
- If you use MagicDNS, `oak` resolves automatically. Otherwise, use the Tailscale IP (e.g., `100.x.x.x`)
- No firewall changes needed on either end — Tailscale punches through NAT
</aside>

### Verify Tailscale connectivity

```bash
tailscale status
tailscale ping oak
```

---