# 📦 Watchtower Docker Compose Stack

This repository contains the Docker Compose configuration for the **Watchtower** service, which monitors and automatically updates running Docker containers with zero downtime. It supports rolling restarts and optional email notifications.

---

## 📁 Directory Structure

```bash
/tank/docker/
├── compose/
│   └── watchtower/
│       ├── docker-compose.yml      # Main Docker Compose config
│       ├── .env                    # Runtime environment variables
│       ├── env.example             # Example .env file for reference
│       ├── env.j2                  # Optional Jinja2 template
│       └── readme.md               # This file
├── data/
│   └── watchtower/                 # Reserved for persistence (optional)
```

---

## 🧰 Prerequisites

* Docker Engine
* Docker Compose V2
* Git
* (Optional) ZFS on Linux for dataset management

> ⚠️ **Note:** These instructions assume your ZFS pool is named `tank`. If your pool has a different name (e.g., `rpool`, `zdata`, etc.), replace `tank` in all paths and commands with your actual pool name.

---

## ⚙️ Setup Instructions

1. **Create the stack directory and clone the repository**

   ### If using ZFS:
   ```bash
   sudo zfs create -p tank/docker/compose/watchtower
   cd /tank/docker/compose/watchtower
   sudo git clone https://github.com/Vantasin/watchtower.git .
   ```

   ### If using standard directories:
   ```bash
   mkdir -p ~/docker/compose/watchtower
   cd ~/docker/compose/watchtower
   git clone https://github.com/Vantasin/watchtower.git .
   ```

2. **Create the runtime data directory** (optional)

   ### If using ZFS:
   ```bash
   sudo zfs create -p tank/docker/data/watchtower
   ```

   ### If using standard directories:
   ```bash
   mkdir -p ~/docker/data/watchtower
   ```

3. **Configure environment variables**

   Copy and modify the `.env` file:

   ```bash
   sudo cp env.example .env
   sudo nano .env
   sudo chmod 600 .env
   ```

   > Alternatively generate the `.env` file using the Jinja2 `env.j2` template and CLI or Ansible's `template` module.

4. **Start Watchtower**

   ```bash
   docker compose up -d
   docker compose ps
   ```

---

## 🛠 Watchtower Configuration

### Environment Variables in `.env`:

```dotenv
# Hostname for the Watchtower container
WATCHTOWER_HOSTNAME=raspberrypi

# Notification method: email | slack | shoutrrr | none
WATCHTOWER_NOTIFICATIONS=email

# Email notification settings
WATCHTOWER_NOTIFICATION_EMAIL_FROM=watchtower@example.com
WATCHTOWER_NOTIFICATION_EMAIL_TO=admin@example.com
WATCHTOWER_NOTIFICATION_EMAIL_SERVER=smtp.example.com
WATCHTOWER_NOTIFICATION_EMAIL_SERVER_PORT=587
WATCHTOWER_NOTIFICATION_EMAIL_SERVER_USER=smtp-user
WATCHTOWER_NOTIFICATION_EMAIL_SERVER_PASSWORD=smtp-password

# Watchtower behavior
WATCHTOWER_CLEANUP=true                    # Remove old images after update
WATCHTOWER_INCLUDE_RESTARTING=true        # Include containers in restarting state
WATCHTOWER_POLL_INTERVAL=86400            # Check for updates every 24 hours (in seconds)
```

These variables enable **automated container updates** with optional **email notifications**. You may remove notification-related lines from the `docker-compose.yml` if you don’t need alerts.

> 🔐 **Security Tip:** This file contains sensitive credentials. After editing, restrict permissions to root-only:
>
> ```bash
> sudo chmod 600 .env
> ```

---

## 🧪 Useful Commands

```bash
# Stop the Watchtower service
docker compose down
```

---

## 🧷 ZFS Snapshot (optional)

```bash
sudo zfs snapshot tank/docker/data/watchtower@$(date +%Y%m%d)
sudo zfs rollback tank/docker/data/watchtower@<snapshot_name>
```
