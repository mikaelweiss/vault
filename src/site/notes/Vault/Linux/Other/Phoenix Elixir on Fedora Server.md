---
{"dg-publish":true,"permalink":"/vault/linux/other/phoenix-elixir-on-fedora-server/"}
---

sudo dnf install elixir erlang
mix archive.install hex phx_new

## PostgreSQL Setup on Fedora Server 43

Fedora 43 ships with PostgreSQL 18 as the default version.
### 1. Install PostgreSQL

```bash
sudo dnf install postgresql-server postgresql-contrib
```

### 2. Initialize the Database

The database needs to be populated with initial data after installation:

```bash
sudo postgresql-setup --initdb
```

### 3. Start and Enable PostgreSQL

```bash
sudo systemctl enable postgresql
sudo systemctl start postgresql
```

### 4. Verify Installation

```bash
sudo systemctl status postgresql
```

### 5. Create a Database and User

sudo -u postgres psql
psql -c "CREATE USER mywebsite_user WITH PASSWORD 'strong-password' CREATEDB;"

