# Data Engine One - Primary Home Lab System Documentation

This document is a detailed reference for my primary core home lab computer, **Data Engine One**. It includes the filesystem, Docker services, volumes, PostgreSQL version history, and operational commands. It’s intended for maintenance, troubleshooting, and automation.

---

## 1. System Overview

* **Host Name:** tristons-server
* **Primary Role:** Core home lab server for projects, automation, and personal data management
* **OS:** Linux (distribution not explicitly provided)
* **Shell:** Zsh
* **Package Management:** apt (assumed for Debian-based system)
* **Filesystem Location:** `~/Vault/`
* **Automation / Service Management:** Docker + Docker Compose
* **Notes:** System used for both personal and school projects, media, and home lab automation

---

## 2. Filesystem Layout

### Root Vault Directory (`~/Vault/`)

```text
~/Vault/
├── archive     # Old projects, backups, logs
├── gaming      # Game installations, configs, saves
├── media       # Movies, music, images
├── personal    # Personal documents, journaling
├── school      # School projects and learning resources
└── system      # Core system configurations, automation, Docker services
```

### System Directory (`~/Vault/system/`)

```text
~/Vault/system/
├── configs/
│   └── datalab/
│       └── docker-compose.yml   # Docker Compose config for lab services
├── postgres_data/               # Active PostgreSQL persistent storage
├── postgres_data_backup/        # Backup of old PostgreSQL v15 data (before upgrade to v16)
└── workflows/                   # n8n automation workflows
```

**Notes:**

* All directories are owned by user `triston` with permissions `drwxrwxr-x`.
* Backup directories are maintained before major version changes to ensure data integrity.
* Docker configurations are version-controlled for reproducibility.

---

## 3. Docker Services

### Containers

| Container        | Image            | Purpose                                      | Ports                            | Status Notes                                                       |
| ---------------- | ---------------- | -------------------------------------------- | -------------------------------- | ------------------------------------------------------------------ |
| `postgres_db`    | postgres:16      | Primary relational database for lab projects | Internal only (no host exposure) | Previously PostgreSQL v15 → upgraded to v16, old data incompatible |
| `n8n_automation` | n8nio/n8n:1.58.2 | Automation workflows for tasks and scripts   | 127.0.0.1:5678->5678/tcp         | Web-based automation and workflow management                       |

### Docker Network

* `datalab_core_net` – internal network connecting PostgreSQL and n8n services

### Notes on Docker Compose

* The Docker Compose file had the deprecated `version` attribute. Functionality unaffected but can be removed.
* Persistent storage paths:

  * PostgreSQL: `~/Vault/system/postgres_data/`
  * n8n workflows: `~/Vault/system/workflows/`
* Restart behavior:

  * PostgreSQL container fails to start if old data is present and incompatible with current image version
  * n8n container pulls and runs latest workflow images

---

## 4. PostgreSQL Details

* **Original Version:** 15
* **Current Version:** 16.11 (Debian 16.11-1.pgdg13+1)
* **Issue:** Data directory from v15 incompatible with v16 → database could not start
* **Solution:** Moved old data to `postgres_data_backup/` and created fresh `postgres_data/` volume
* **Persistent Volume Path:** `~/Vault/system/postgres_data/`
* **Backup Path:** `~/Vault/system/postgres_data_backup/`

---

## 5. Automation (n8n)

* **Version:** 1.58.2
* **Container Name:** n8n_automation
* **Port:** 127.0.0.1:5678 (host mapped)
* **Purpose:** Automates system tasks, notifications, and workflow management
* **Workflows Path:** `~/Vault/system/workflows/`
* **Notes:** Pulls multiple workflow images; fully containerized

---

## 6. Common Commands

```bash
# Docker commands
docker compose up -d         # Start all containers
docker compose down          # Stop and remove all containers
docker logs <container>      # View logs for a container

# PostgreSQL maintenance
sudo rm -rf ~/Vault/system/postgres_data/*                # Clean existing DB (destructive)
mv ~/Vault/system/postgres_data ~/Vault/system/postgres_data_backup  # Backup old data

# System update
sudo apt update && sudo apt upgrade -y
```

---

## 7. Networking

* Internal Docker network: `datalab_core_net`
* PostgreSQL: no host port exposure
* n8n: mapped to 127.0.0.1:5678
* Firewalled and internal-only services for security

---

## 8. Use Cases

* **Core Home Lab:** Central server for personal, school, and project data
* **Automation:** Workflow management via n8n
* **Database Management:** PostgreSQL containerized for project data
* **Development:** Script testing and service experimentation
* **Data Organization:** Structured Vault directory for clean project storage

---

## 9. System Notes

* All services are containerized to ensure isolation and reproducibility
* Filesystem permissions are standardized for user `triston`
* PostgreSQL upgrade path documented and old data preserved
* Docker Compose handles service orchestration, network creation, and volume management
* The lab is a sandboxed environment, safe for testing automation and development workflows

---

## 10. Reference Paths Summary

```text
~/Vault/
  archive/
  gaming/
  media/
  personal/
  school/
  system/
    configs/datalab/docker-compose.yml
    postgres_data/          # Active PostgreSQL storage
    postgres_data_backup/   # Backup PostgreSQL v15 → v16
    workflows/              # n8n automation workflows
```

---

## 11. System Diagram

```text
Data Engine One (Home Lab)
├── Vault/
│   ├── archive/
│   ├── gaming/
│   ├── media/
│   ├── personal/
│   ├── school/
│   └── system/
│       ├── configs/
│       │   └── datalab/
│       │       └── docker-compose.yml
│       ├── postgres_data/      ← PostgreSQL v16 active data
│       ├── postgres_data_backup/ ← PostgreSQL v15 backup
│       └── workflows/          ← n8n workflows
│
└── Docker Setup
    ├── Network: datalab_core_net
    ├── Container: postgres_db (PostgreSQL 16)
    └── Container: n8n_automation (n8n 1.58.2, 127.0.0.1:5678)
```

---

## 12. Docker Compose File (`docker-compose.yml`)

```yaml
services:
  db:
    image: postgres:16
    container_name: postgres_db
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: example
    volumes:
      - ../postgres_data:/var/lib/postgresql/data

  n8n:
    image: n8nio/n8n:1.58.2
    container_name: n8n_automation
    restart: always
    ports:
      - "127.0.0.1:5678:5678"
    volumes:
      - ../workflows:/home/node/.n8n
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=example

networks:
  default:
    name: datalab_core_net
```

---

**End of Data Engine One Home Lab Documentation**
