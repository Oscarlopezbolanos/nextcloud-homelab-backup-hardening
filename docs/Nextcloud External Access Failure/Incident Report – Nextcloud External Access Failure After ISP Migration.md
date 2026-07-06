### Incident Report – Nextcloud External Access Failure After ISP Migration

## Summary

## Following a migration from WOW Internet using an Eero gateway to Spectrum Internet using a WiFi 7 router, external access to the self-hosted Nextcloud environment at cloud.lopezbolanos.com became unavailable.

Environment

- Ubuntu Server VM (VirtualBox)
- Docker
- Nextcloud
- MariaDB
- Caddy Reverse Proxy
- Cloudflare DNS
- Spectrum Internet Service

Detection

Users attempting to access cloud.lopezbolanos.com received connection failures.

Testing showed:

- DNS resolution inconsistencies
- VirtualBox network adapter changes
- Missing LAN IP assignments
- External HTTPS connection failures

## Investigation

The following troubleshooting steps were performed:

1. Verified local network connectivity.
2. Verified internet access from the VM.
3. Verified DNS records within Cloudflare.
4. Verified Caddy TLS certificates.
5. Verified Docker container status.
6. Verified UFW firewall rules.
7. Verified port 443 listener.
8. Compared public IP assignments before and after ISP migration.

## Root Cause

The migration from WOW/Eero to Spectrum introduced changes to network topology and adapter assignments.

The Ubuntu VM temporarily lost proper network configuration and Spectrum's router interface did not reliably expose the VM as a selectable device for direct port forwarding.

Cloudflare DNS records also required verification after the ISP transition.

## Remediation

- Reconfigured VirtualBox networking.
- Verified DHCP assignment.
- Confirmed Docker and Caddy functionality.
- Verified TLS certificate validity.
- Updated and validated DNS records.
- Disabled Spectrum Security Shield during testing.
- Restored external accessibility to cloud.lopezbolanos.com.

## Outcome

External HTTPS access was successfully restored.

Nextcloud services, TLS certificates, Docker containers, and Cloudflare DNS are currently operational.

## Lessons Learned

- ISP migrations can alter network adapter behavior unexpectedly.
- Maintain documentation of VirtualBox network configurations.
- Verify DNS records immediately after public IP changes.
- Consider automated dynamic DNS updates.
- Maintain multiple backup access methods for self-hosted services.
