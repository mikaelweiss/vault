---
{"dg-publish":true,"permalink":"/vault/linux/other/samba-file-share/"}
---

### Set up on Fedora Server

```bash
sudo dnf install samba -y
sudo systemctl enable smb --now
firewall-cmd --get-active-zones
sudo firewall-cmd --permanent --zone=FedoraWorkstation --add-service=samba
sudo firewall-cmd --reload
```

#### Set password
```bash
sudo smbpasswd -a mikaelweiss
```
#### Set up SELinux context
```bash
mkdir /home/mikaelweiss/share
sudo semanage fcontext --add --type "samba_share_t" "/home/mikaelweiss/share(/.*)?"
sudo restorecon -R ~/share
```

#### Set up ini
sudo nvim /etc/samba/smb.conf
```ini
[global]
   workgroup = SAMBA
   security = user
   passdb backend = tdbsam

   # Security hardening
   server min protocol = SMB3
   smb encrypt = required

   # Host restrictions (adjust subnet to match yours)
   hosts allow = 192.168.0.0/24, 100.64.0.0/10 EXCEPT 192.168.0.103, 100.105.253.35
   hosts deny = ALL

   # Disable printing (remove if you need it)
   load printers = no
   printing = bsd
   printcap name = /dev/null
   disable spoolss = yes

[share]
   comment = My Share
   path = /home/mikaelweiss/share
   writeable = yes
   browseable = yes
   public = no
   valid users = mikaelweiss
   create mask = 0644
   directory mask = 0755
```
`sudo systemctl restart smb`

#### Make sure Tailscale is in the right firewall zone
```bash
sudo firewall-cmd --permanent --zone=trusted --add-interface=tailscale0
sudo firewall-cmd --reload
```
