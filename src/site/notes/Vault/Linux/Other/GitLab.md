---
{"dg-publish":true,"permalink":"/vault/linux/other/git-lab/"}
---

sudo dnf install -y curl policycoreutils openssh-server perl
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
sudo nvim /etc/yum.repos.d/gitlab_gitlab-ce.repo
change fedora/43 to el/8
sudo dnf clean all
sudo dnf makecache
sudo EXTERNAL_URL="http://oak" dnf install -y gitlab-ce
sudo dnf install -y libxcrypt-compat
sudo gitlab-ctl reconfigure

get password from `/etc/gitlab/initial_root_password` and then sign in
username: root
password in `/etc/gitlab/initial_root_password`

Manually confirm email address:

sudo gitlab-rails console
user = User.find_by(email: 'your@email.com')
or: 
user = User.find_by(username: 'root')
user.confirm
exit

## Making it use less RAM

Edit /etc/gitlab/gitlab.rb
```bash
# Puma - web server (biggest impact)
puma['worker_processes'] = 0  # Single mode saves ~400MB+

# Sidekiq - background jobs
sidekiq['concurrency'] = 10  # Down from 20

# PostgreSQL
postgresql['shared_buffers'] = "256MB"  # 1/4 of available RAM

# Disable monitoring (if not needed)
prometheus_monitoring['enable'] = false  # Saves ~200MB

# Disable unused services
gitlab_pages['enable'] = false
registry['enable'] = false  # If not using container registry

# Memory allocator optimization
gitlab_rails['env'] = {
  'MALLOC_CONF' => 'dirty_decay_ms:1000,muzzy_decay_ms:1000'
}

# Gitaly optimization
gitaly['configuration'] = {
  concurrency: [
    { 'rpc' => "/gitaly.SmartHTTPService/PostReceivePack", 'max_per_repo' => 3 },
    { 'rpc' => "/gitaly.SSHService/SSHUploadPack", 'max_per_repo' => 3 }
  ],
  cgroups: {
    repositories: { count: 2 },
    mountpoint: '/sys/fs/cgroup',
    hierarchy_root: 'gitaly',
    memory_bytes: 536870912,
    cpu_shares: 512
  }
}

gitaly['env'] = {
  'MALLOC_CONF' => 'dirty_decay_ms:1000,muzzy_decay_ms:1000',
  'GITALY_COMMAND_SPAWN_MAX_PARALLEL' => '2'
}
```

sudo gitlab-ctl status
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
sudo gitlab-ctl status
