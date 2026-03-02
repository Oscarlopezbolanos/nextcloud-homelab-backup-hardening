# 01 — Incident Summary

**Date:** 2026-02-26  
**Environment:**  
- Ubuntu Server VM (VirtualBox)  
- Nextcloud inside Docker Compose  
- MariaDB database  
- Caddy reverse proxy  
- Host: Windows 10  
- Backup targets: USB HDD (Toshiba Canvio)

## What Happened
The automated 14-day backup script silently stopped producing new backups after January 4, 2026.

No errors were alerted. No logs were visible. The backup automation used conflicting date logic and incorrect paths.

## Impact
Backups had become stale without detection — potential data loss risk if the system failed.

## Root Causes Identified
- Database dump was writing to a non-existent directory (`/root/nextcloud-backups`)
- Script included conflicting internal date logic compared to cron
- Backup artifacts were not being written to the intended offsite location
- Silent failures were not being logged or alerted
