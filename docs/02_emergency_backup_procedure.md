# 02 — Emergency Backup Procedure

This document outlines the manual emergency backup process used to secure Nextcloud data.

---

## Step 1 — Database Backup

```bash
docker exec -t nextcloud-db-1 mysqldump -u root -p nextcloud > nextcloud_db_2026-02-26.sql
