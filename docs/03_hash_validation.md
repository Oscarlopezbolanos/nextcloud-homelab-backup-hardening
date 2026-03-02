
---

# 📁 docs/03_hash_validation.md

```markdown
# 03 — Hash Validation

To ensure backup integrity, SHA256 hashes were generated and compared across operating systems.

---

## Ubuntu Hash

```bash
sha256sum nextcloud_full_2026-02-26.tar.gz
D2038DCA67C95BC4E45E6461A71CD120340E9F864297562E0BE00F83596785F8

Get-FileHash nextcloud_full_2026-02-26.tar.gz -Algorithm SHA256
Ubuntu outputs lowercase hex.
Windows PowerShell outputs uppercase hex.

SHA256 is case-insensitive.
Matching values confirm integrity.
