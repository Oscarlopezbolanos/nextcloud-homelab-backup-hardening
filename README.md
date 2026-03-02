# Nextcloud Homelab Backup Hardening & Incident Response

## Overview

This project documents the detection, investigation, mitigation, and hardening of a failed automated backup system in a self-hosted Nextcloud environment.

The system was running inside an Ubuntu Server VM (VirtualBox) with Docker Compose, MariaDB, and Caddy. During routine verification, it was discovered that backups had silently stopped updating.

Although no data loss occurred, the situation represented a high-risk failure point.

This repository documents the full incident response and recovery process.

---

## Environment

- Ubuntu Server (VirtualBox VM)
- Docker Compose
- Nextcloud
- MariaDB
- Caddy reverse proxy
- Windows host
- NVMe primary storage
- 4TB SSD local backup target
- External HDD (offsite)

Total protected data: ~111GB

---

## Incident Summary

The 14-day automated backup process stopped producing valid artifacts after January 4, 2026.

Root causes identified:

- Database dump path referenced a non-existent directory
- Conflicting internal scheduling logic vs. cron timing
- No alerting or validation on backup success
- Silent failure behavior

The system appeared healthy, but backups were stale.

---

## Emergency Mitigation

A full emergency backup was executed manually:

- Database dump (.sql)
- Data archive (.tar.gz)
- Full environment archive (.tar.gz)

Total archive size: ~111GB

Backups were:

- Copied to secondary 4TB SSD
- Hash validated (SHA256)
- Cross-platform verified (Ubuntu & Windows)
- Documented for recovery reference

---

## Integrity Verification

SHA256 (verified identical on Linux and Windows):

D2038DCA67C95BC4E45E6461A71CD120340E9F864297562E0BE00F83596785F8

Hash match confirms no corruption during transfer.

---

## Infrastructure Improvements

Post-incident improvements include:

- Separation of production and backup storage
- Shared folder mount validation
- Group permission correction (vboxsf)
- Backup integrity validation workflow
- Structured documentation of recovery process

---

## Lessons Learned

- Backups must be verified, not assumed.
- Silent failures are dangerous.
- Absolute paths should always be used in automation scripts.
- Integrity validation (SHA256) should be standard practice.
- Backup storage should be physically separated from production storage.

---

## Current State

✔ Production on NVMe  
✔ Local backup on dedicated 4TB SSD  
✔ Verified emergency archive  
✔ Incident documented  
✔ Environment hardened  

---

## Future Improvements

- Automated backup verification checks
- Incremental backup strategy (rsync-based)
- Monitoring/alerting for failed jobs
- Restore testing procedure documentation
