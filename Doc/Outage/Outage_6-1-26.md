## Outage 6/1/24

# Incident Report: Nextcloud Outage Due to VM Corruption Following Power Loss

## Incident Summary

During a neighborhood power outage, my production Nextcloud virtual machine became corrupted and was unable to boot. The outage occurred while VirtualBox was deleting a snapshot. Because the snapshot operation was interrupted unexpectedly, the virtual machine disk became corrupted, resulting in a kernel panic during startup.

Multiple recovery attempts were performed, including restoring previous snapshots. However, the corruption persisted and the original virtual machine could not be recovered.

Because this environment is treated as a production server within my home lab, multiple backup systems were already in place. A backup Nextcloud virtual machine was restored and brought online within approximately 24 hours.

Attempts were made to recover the most recent files directly from the corrupted virtual machine. During the recovery process it was discovered that portions of the Docker-based Nextcloud data were also corrupted. Some recovery attempts caused issues within the backup environment, requiring restoration from an additional snapshot.

Most data was successfully recovered using alternate backups and secondary storage locations. Only a minimal amount of recent data was lost.

---

## Environment

### Host System

* Windows 11
* VirtualBox

### Virtual Machine

* Ubuntu Server 24.04
* Docker-based Nextcloud deployment

### Services

* Nextcloud
* Docker Containers
* SSH

### Storage

* Nextcloud Data Directory
* Windows-based backup repository
* Multiple VM snapshots

---

## Timeline

### Initial Problem

A neighborhood power outage occurred while VirtualBox was deleting a snapshot of the production Nextcloud virtual machine.

Following restoration of power, the Nextcloud server failed to boot and displayed kernel panic errors.

### Investigation

Several recovery attempts were performed using previous snapshots.

Despite rolling back to multiple restore points, the virtual machine continued to experience kernel panic errors and could not successfully start.

The corruption was determined to be severe enough that recovery of the original virtual machine was not practical.

### Recovery Attempts

A backup Nextcloud virtual machine was restored.

An attempt was made to recover the most recent files from the corrupted server and merge them into the backup environment.

During recovery, portions of the Docker Nextcloud data were found to be corrupted, preventing a complete transfer of files.

Additional troubleshooting was performed involving:

* Docker file recovery
* File ownership verification
* Linux permissions troubleshooting
* SSH file transfers
* CIFS/SMB share troubleshooting
* Nextcloud file rescanning using OCC commands

Some recovery attempts negatively affected the backup server, requiring restoration from an additional snapshot.

### Resolution

A clean backup virtual machine was restored.

Recovered files were imported from alternate backup locations and secondary storage devices.

File permissions were corrected and Nextcloud functionality was verified.

The Nextcloud service was successfully restored and returned to operation within approximately 24 hours.

---

## Impact

### Service Impact

* Nextcloud unavailable for approximately 24 hours
* File synchronization interrupted
* Recent file uploads temporarily inaccessible

### Data Impact

* Majority of files successfully recovered
* Minimal data loss due to multiple backup sources

---

## Root Cause

The root cause of the incident was an unexpected power outage during a VirtualBox snapshot deletion operation.

The interruption caused corruption of the virtual machine disk and associated Nextcloud Docker data, resulting in an unrecoverable kernel panic condition.

---

## Lessons Learned

* Backups are critical for production systems, even in a home lab environment.
* Snapshots should not be considered a replacement for full backups.
* Virtual machines should be shut down gracefully whenever possible.
* Power interruptions during disk-intensive operations can cause severe corruption.
* File ownership and Linux permissions must be verified after restoring data.
* Docker application data can become corrupted alongside the host virtual machine.
* Recovery efforts should always begin with a fresh snapshot of the backup system.
* Multiple backup locations significantly reduce the risk of permanent data loss.
* Understanding SSH, SMB/CIFS, and Linux permissions is essential during recovery operations.

---

## Current Status

### Status: Resolved

* Nextcloud server operational
* User access restored
* File synchronization functioning
* Backup procedures reviewed and validated

### Future Improvements

* Deploy UPS battery backup for critical systems
* Increase backup frequency
* Perform regular recovery testing
* Maintain offline copies of critical data
