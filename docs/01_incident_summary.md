# 01 — Incident Summary

**Date:** 2026-02-26  
**Environment:**  
- Ubuntu Server VM (VirtualBox)
- Nextcloud deployed via Docker Compose
- MariaDB database container
- Caddy reverse proxy
- Host OS: Windows
- Backup Targets: VirtualBox shared folder (4TB SSD), external USB HDD (Toshiba Canvio)

---

## Overview

An incident was discovered where automated Nextcloud backups had silently stopped functioning. The issue was identified during routine verification and infrastructure hardening review.

No immediate data loss occurred, but the absence of recent backups represented a critical operational risk.

---

## Symptoms Observed

- No recent backup artifacts present.
- Cron job had executed without visible errors.
- Database dump path referenced a non-existent directory.
- Shared folder mounts required revalidation after VM restart.
- VM experienced freeze during backup copy operation.

---

## Risk Assessment

If the system had failed during this period:

- User files (~111GB) could have been unrecoverable.
- Database metadata (user accounts, file mappings, shares) would have been lost.
- Service restoration would have required full rebuild.

Severity: **High (Data Integrity Risk)**  
Impact: **Potential Total Data Loss**

---

## Immediate Response Actions

1. Manually created emergency backup:
   - Database dump (.sql)
   - Data archive (.tar.gz)
   - Full environment archive (.tar.gz)
2. Verified backup integrity using SHA256 hashing.
3. Copied backup to secondary storage (4TB SSD).
4. Validated cross-platform integrity (Ubuntu & Windows hash comparison).
5. Began documentation and hardening process.

---

## Incident Status

✔ Backup artifacts successfully created  
✔ Integrity validated  
✔ Offsite copy completed  
✔ Documentation formalized  
✔ Hardening plan initiated
