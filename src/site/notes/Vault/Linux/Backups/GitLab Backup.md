---
{"dg-publish":true,"permalink":"/vault/linux/backups/git-lab-backup/"}
---

Copy ssh key to the second computer

### Create the Password File

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

## Initialize the Restic Repositories
### Remote repository (elm via SFTP)

First, create the backup directory on elm

```bash
ssh mikaelweiss@elm
mkdir -p /home/mikaelweiss/backups/gitlab-backup
```

Then initialize the repository. Run this on oak.

```bash
restic init -r sftp:mikaelweiss@elm:/home/mikaelweiss/backups/gitlab-backup --password-file /etc/restic/password
```

```
mkdir /backups/gitlab-backup
restic init -r /home/mikaelweiss/backups/gitlab-backup --password-file /etc/restic/password
restic backup -r /home/mikaelweiss/backups/gitlab-backup /var/opt/gitlab/backups /etc/gitlab --password-file /etc/restic/password
```