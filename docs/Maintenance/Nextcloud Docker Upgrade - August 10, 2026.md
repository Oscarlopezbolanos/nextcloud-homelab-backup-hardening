# Nextcloud Docker Upgrade - August 10, 2026

## Overview

This document records the successful upgrade of my production Nextcloud server running in Docker.

The server was running an unsupported Nextcloud 30 release and was upgraded sequentially through each supported major version until reaching Nextcloud 34.

### Upgrade Path

```text
Nextcloud 30.0.17.2
        ↓
Nextcloud 31.0.14
        ↓
Nextcloud 32.0.13
        ↓
Nextcloud 33.0.7
        ↓
Nextcloud 34.0.2

Nextcloud major versions were upgraded one at a time rather than skipping versions.

Environment

The Nextcloud production environment runs on an Ubuntu virtual machine using Docker Compose.

Docker Services
Nextcloud: nextcloud:34-apache
MariaDB:   mariadb:11
Redis:     redis:7-alpine
Caddy:     caddy:2

Docker Compose directory:

/home/hnolbtg/nextcloud

The Nextcloud container uses persistent storage mounted from the host.

Example Docker Compose configuration:

services:

  db:
    image: mariadb:11
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    restart: unless-stopped
    env_file: .env
    environment:
      - MARIADB_AUTO_UPGRADE=1
    volumes:
      - ./db:/var/lib/mysql

  redis:
    image: redis:7-alpine
    restart: unless-stopped

  nextcloud:
    image: nextcloud:34-apache
    restart: unless-stopped
    env_file: .env
    environment:
      - MYSQL_HOST=db
      - REDIS_HOST=redis
      - TZ=${TZ}
    volumes:
      - ./data:/var/www/html
    depends_on:
      - db
      - redis

  caddy:
    image: caddy:2
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - ./caddy-data:/data
      - ./caddy-config:/config
    depends_on:
      - nextcloud
Pre-Upgrade Validation

Before upgrading, the running Docker containers were checked:

cd /home/hnolbtg/nextcloud

sudo docker compose ps

Docker images were checked:

sudo docker compose images

The Nextcloud application status was checked using OCC:

sudo docker compose exec -u www-data nextcloud php occ status

Initial result:

installed: true
version: 30.0.17.2
maintenance: false
needsDbUpgrade: false

The Docker Compose image configuration was checked:

grep -n "image:" docker-compose.yml

Initial images:

mariadb:11
redis:7-alpine
nextcloud:30-apache
caddy:2
Backup / Recovery Protection

Before performing the upgrade, the production VM had multiple recovery mechanisms available:

Full VM backup stored on external flash drive
Second full VM backup stored on a separate flash drive
Virtual machine snapshot

These backups provided the ability to restore the entire Nextcloud environment if the upgrade failed.

Upgrade Procedure
Nextcloud 30 → 31

Edited:

nano docker-compose.yml

Changed:

image: nextcloud:30-apache

to:

image: nextcloud:31-apache

Pulled the new image:

sudo docker compose pull nextcloud

Recreated the Nextcloud container:

sudo docker compose up -d

Checked container status:

sudo docker compose ps

Verified Nextcloud:

sudo docker compose exec -u www-data nextcloud php occ status

Result:

version: 31.0.14.1
maintenance: false
needsDbUpgrade: false
Nextcloud 31 → 32

Edited:

nano docker-compose.yml

Changed:

image: nextcloud:31-apache

to:

image: nextcloud:32-apache

Pulled and recreated the container:

sudo docker compose pull nextcloud

sudo docker compose up -d

Checked status:

sudo docker compose ps

sudo docker compose exec -u www-data nextcloud php occ status

Immediately after container recreation, Nextcloud temporarily reported:

maintenance: true
needsDbUpgrade: true

The OCC upgrade command was checked:

sudo docker compose exec -u www-data nextcloud php occ upgrade

The command returned:

No upgrade required.

The Docker container startup process had already completed the database migration.

The status was checked again:

sudo docker compose exec -u www-data nextcloud php occ status

Final result:

version: 32.0.13.1
maintenance: false
needsDbUpgrade: false
Nextcloud 32 → 33

Changed the Docker image:

image: nextcloud:33-apache

Pulled and recreated the container:

sudo docker compose pull nextcloud

sudo docker compose up -d

Verified the containers:

sudo docker compose ps

Verified Nextcloud:

sudo docker compose exec -u www-data nextcloud php occ status

Result:

version: 33.0.7.1
maintenance: false
needsDbUpgrade: false
Nextcloud 33 → 34

Changed:

image: nextcloud:33-apache

to:

image: nextcloud:34-apache

Pulled the image:

sudo docker compose pull nextcloud

Recreated the Nextcloud container:

sudo docker compose up -d

Checked all containers:

sudo docker compose ps

Verified Nextcloud:

sudo docker compose exec -u www-data nextcloud php occ status

Final result:

installed: true
version: 34.0.2.1
versionstring: 34.0.2
maintenance: false
needsDbUpgrade: false
productname: Nextcloud
extendedSupport: false
Final Environment
Nextcloud 34.0.2
MariaDB 11
Redis 7-alpine
Caddy 2

All containers remained operational during the upgrade process.

The MariaDB, Redis, and Caddy images were not modified during the Nextcloud major-version upgrades.
