
---

# 📁 docs/05_lessons_learned.md

```markdown
# 05 — Lessons Learned

This incident highlighted important infrastructure principles.

---

## 1. Backups Must Be Verified

A backup that exists but is not validated is not a backup.

Hash validation should be standard practice.

---

## 2. Silent Failures Are Dangerous

Cron jobs can fail quietly.

Logging and validation checks must be implemented.

---

## 3. Layered Backup Strategy Is Essential

- Database backup
- Data backup
- Full environment backup

Each serves a different recovery scenario.

---

## 4. Cross-Platform Integrity Verification Is Powerful

Validating SHA256 across Linux and Windows ensures:

- No transfer corruption
- No storage corruption
- No filesystem inconsistencies

---

## 5. Separate Production Storage From Backup Storage

Primary VM now resides on NVMe.
Backups are stored on separate physical SSD.
Offsite external drive reserved for future Proxmox deployment.

This reduces single point of failure risk.

---

## Final Outcome

The homelab environment is now:

✔ Hardened  
✔ Verified  
✔ Documented  
✔ Recoverable  
✔ Structured for future automation  

This incident improved operational maturity and infrastructure discipline.
