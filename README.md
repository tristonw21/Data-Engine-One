# Data Engine One – Home Lab Core Computer

**Owner/Engineer:** Triston White
**Project Type:** Primary Home Lab Computer
**Status:** Online

**Scope:** Data Engine One is **my central home lab machine**, hosting core services, automation tools, and storage.

---

## 1. Top-Level Vault Layout

```
~/Vault/
├── archive/       # Archived data, old projects, backups
├── gaming/        # Game installs, configs, and related tools
├── media/         # Music, video, and images
├── personal/      # Personal documents, notes, and configs
├── school/        # Educational files, course material
└── system/        # Core lab infrastructure
    ├── configs/
    │   └── datalab/         # Docker Compose configs and service definitions
    ├── postgres_data/       # Live database files (PostgreSQL)
    ├── postgres_data_backup/ # Database backups
    ├── n8n_data/            # Automation workflows and credentials
    └── logs/                # Aggregated container logs (optional)
```

---

## 2. System Directory Details

### 2.1 Configs

* **Path:** `~/Vault/system/configs/datalab/`
* **Purpose:** Contains `docker-compose.yml` and related environment files for orchestrating core services like PostgreSQL and n8n.

### 2.2 Persistent Data

* **PostgreSQL:** `~/Vault/system/postgres_data/`
* **Backups:** `~/Vault/system/postgres_data_backup/`
* **n8n:** `~/Vault/system/n8n_data/`

### 2.3 Logs

* **Path:** `~/Vault/system/logs/`
* **Purpose:** Store container logs for troubleshooting or historical tracking.

---

## 3. Home Lab Services

| Service             | Purpose                                | Data Location           |
| ------------------- | -------------------------------------- | ----------------------- |
| PostgreSQL 16.11    | Structured data storage                | `postgres_data/`        |
| n8n 1.58.2          | Automation workflows & scheduling      | `n8n_data/`             |
| Docker Compose      | Orchestration and container management | `configs/datalab/`      |
| Scripts & Utilities | Backup, restore, maintenance           | `~/scripts/` (optional) |

---

## 4. Recommended Commands

**Start All Services**

```bash
cd ~/Vault/system/configs/datalab
docker compose up -d
```

**Stop All Services**

```bash
docker compose down
```

**Backup Database**

```bash
cp -r ~/Vault/system/postgres_data ~/Vault/system/postgres_data_backup
```

**Restore Database**

```bash
docker compose down
rm -rf ~/Vault/system/postgres_data
cp -r ~/Vault/system/postgres_data_backup ~/Vault/system/postgres_data
docker compose up -d
```

**View Logs**

```bash
docker logs postgres_db
docker logs n8n_automation
```
