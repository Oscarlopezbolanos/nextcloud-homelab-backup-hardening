# nextcloud-homelab-backup-hardening
Self host Cloud on Ubuntu, NextCloud
# Nextcloud Backup Hardening & Incident Response

## Overview

This project documents the investigation and remediation of a failed automated backup system in a self-hosted Nextcloud Docker environment running on Ubuntu (VirtualBox VM).

The issue was discovered during a routine verification of offsite backups.

---

## Environment

- Ubuntu Server (VirtualBox)
- Docker Compose
- Nextcloud (Apache)
- MariaDB
- Redis
- Caddy reverse proxy
- 2TB virtual disk

---

## Incident Summary

Automated 14-day cron backups stopped functioning after January 4, 2026.

The system appeared healthy, but backup artifacts were not updating.

Root cause included:
- Incorrect database dump path (`/root/nextcloud-backups`)
- Conflicting 14-day scheduling logic inside script
- Lack of validation/error alerting
- Silent failure of automation

---

## Investigation Steps

1. Verified cron schedule
2. Inspected backup logs
3. Identified missing directory for DB dump
4. Confirmed Docker mount structure
5. Verified actual Nextcloud data directory
6. Executed manual database dump
7. Created full system tar archive (111GB)

---

## Emergency Mitigation

- Created `/home/hnolbtg/EMERGENCY_BACKUP`
- Performed manual MariaDB dump
- Archived full Nextcloud stack
- Verified archive size: **111GB**
- Confirmed data integrity before remediation

---

## Lessons Learned

- Never rely on automation without verification
- Always use absolute paths in scripts
- Avoid duplicate scheduling logic
- Validate backup artifacts periodically
- Document everything during incident response

---

## Next Steps

- Refactor backup script
- Implement logging validation
- Add offsite replication
- Test restore procedure
- Implement monitoring/alerts

## Backup Validation

- Full system archive created: 111GB
- Archive copied to external Toshiba Canvio (E:\offsite_backups)
- Offsite redundancy confirmed
- Manual validation after automation failure

