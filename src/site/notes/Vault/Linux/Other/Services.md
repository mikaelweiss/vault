---
{"dg-publish":true,"permalink":"/vault/linux/other/services/"}
---

These are **systemd unit types** that manage different aspects of your system:

**Services** - Long-running programs (daemons). Example: `nginx.service`, `postgresql.service`. They start, run in background, and can be stopped/restarted.

**Targets** - Grouping/synchronization points, like runlevels. Example: `multi-user.target` (normal operation), `graphical.target` (with GUI). They bundle multiple units together.

**Sockets** - Manage IPC/network sockets for on-demand service activation. Example: `docker.socket` listens and only starts `docker.service` when something connects. Enables lazy-loading.

**Timers** - Scheduled tasks (like cron). Example: `logrotate.timer` triggers `logrotate.service` daily. More flexible than cron with dependency handling.

**Paths** - Monitor filesystem paths and activate services when files/directories change. Example: trigger a service when a file appears in `/var/spool/`.

# Common systemctl Commands for Services

Here are the most frequently used `systemctl` commands:

## Basic Service Control
```bash
sudo systemctl start [service]      # Start a service
sudo systemctl stop [service]       # Stop a service
sudo systemctl restart [service]    # Restart a service
sudo systemctl reload [service]     # Reload config without restart
sudo systemctl status [service]     # Check service status
```

## Enable/Disable Services
```bash
sudo systemctl enable [service]     # Enable service at boot
sudo systemctl disable [service]    # Disable service at boot
sudo systemctl enable --now [service]   # Enable and start immediately
sudo systemctl disable --now [service]  # Disable and stop immediately
```

## Service Information
```bash
systemctl is-active [service]       # Check if running
systemctl is-enabled [service]      # Check if enabled at boot
systemctl is-failed [service]       # Check if service failed
systemctl list-units --type=service # List all services
systemctl list-unit-files --type=service # List service states
```

## Advanced Commands
```bash
sudo systemctl mask [service]       # Prevent service from starting
sudo systemctl unmask [service]     # Remove mask
sudo systemctl daemon-reload        # Reload systemd manager config
systemctl show [service]            # Show all properties
systemctl cat [service]             # Show service unit file
```

## Examples
```bash
sudo systemctl restart nginx
sudo systemctl status apache2
sudo systemctl enable postgresql
```

**Note:** Most commands require `sudo` for system services, except status/information queries.