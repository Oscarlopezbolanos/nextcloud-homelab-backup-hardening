
---

# 📁 docs/04_shared_folder_mount_troubleshooting.md

```markdown
# 04 — VirtualBox Shared Folder Troubleshooting

During backup replication, VirtualBox shared folder mounting required verification.

---

## Issue Observed

After VM freeze and hard shutdown:

- Shared folder path inaccessible
- Mount path appeared missing
- rsync errors indicated incorrect destination path

---

## Verification

```bash
mount | grep vbox
Windows_Nextcloud_Backup on /media/sf_Windows_Nextcloud_Backup type vboxsf
Permission Validation
touch test.txt
rm test.txt
Aftetr validation i made a copy to D: drive
