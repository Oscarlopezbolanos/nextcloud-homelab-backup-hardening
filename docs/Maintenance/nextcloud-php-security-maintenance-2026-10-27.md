# Nextcloud Private Cloud — PHP Security Patching and Upgrade

**Maintenance date:** October 27, 2026  
**Environment:** Self-hosted Ubuntu VM, Docker Compose, Nextcloud, Apache, MariaDB, Redis, Caddy  
**Status:** Application upgrade completed; functional tests passed; database schema warning remains for follow-up.

## Purpose

Address critical PHP vulnerabilities **CVE-2026-109618** and **CVE-2026-109619** by upgrading the PHP runtime bundled in the Nextcloud Apache Docker image. Update Nextcloud and validate service availability and file operations.

Official PHP security release: https://php.net/releases/8_5_12.php  
Google Cloud technical advisory: https://cloud.google.com/blog/products/identity-security/responding-to-core-php-vulnerabilities-cve-2026-109618-cve-2026-109619

## Environment and baseline

| Component | Before | After |
|---|---|---|
| Nextcloud | 34.0.2 | 35.0.2 |
| PHP (inside Nextcloud container) | 8.5.9 | 8.5.12 |
| Docker Compose Nextcloud image | `nextcloud:34-apache` | `nextcloud:35-apache` |
| Other services | Caddy 2, Redis 7-alpine, MariaDB 11 | Existing services retained |

The Nextcloud service uses a bind mount for application data (`./data:/var/www/html`), and MariaDB uses a separate bind mount (`./db:/var/lib/mysql`). A Caddy reverse proxy publishes HTTP/HTTPS. Do not commit the `.env` file, database contents, configuration secrets, or private user files.

## Backup and change control

1. Shut down the Ubuntu virtual machine before making backup copies.
2. Made **four copies of the virtual machine**, stored on separate hard drives.
3. Started the VM and inspected running Docker services and their bind mounts.
4. Reviewed the Compose configuration and image versions before making changes.

**Limitation:** VM copies provide recovery options, but an actual restore test was not documented. Backup integrity and restoration time therefore remain unverified.

## Implementation

The following commands illustrate the checks and update workflow. Commands may have been executed in separate steps; they are not a verbatim shell transcript.

```bash
cd ~/nextcloud

docker compose ls
docker compose config --images
docker ps

docker exec nextcloud-nextcloud-1 php -v
docker exec -u www-data nextcloud-nextcloud-1 php occ status

# Review the compose file, then change the Nextcloud service image:
# image: nextcloud:34-apache
# to:
# image: nextcloud:35-apache

# Pull and recreate the updated service:
docker compose pull nextcloud
docker compose up -d nextcloud
```

The image `nextcloud:35-apache` was inspected before deployment; it contained PHP 8.5.12 and Nextcloud 35.0.2. The upgrade logs showed app updates and a successful code integrity check.

## Post-change validation

```bash
docker exec nextcloud-nextcloud-1 php -v
docker exec -u www-data nextcloud-nextcloud-1 php occ status
docker exec -u www-data nextcloud-nextcloud-1 php occ db:schema:check
```

Observed results:

- Nextcloud `35.0.2` installed.
- PHP `8.5.12` present in the running Nextcloud container.
- `maintenance: false` and `needsDbUpgrade: false`.
- Web interface accessible through the configured domain.
- Existing files visible; test upload and download succeeded.

PHP 8.5.12 is the upstream fixed release for CVE-2026-109618 and CVE-2026-109619. This verifies remediation of those two PHP issues in the updated container runtime, not an exhaustive vulnerability assessment of the entire deployment.

## Outstanding issue

A schema consistency check reported:

```text
oc_share_external: column 'id' differs in: autoincrement
```

The server remains operational, but this warning needs investigation. Do not alter the database schema manually without a new backup and an authoritative migration procedure.

Other observed warnings included differences between existing Redis/S3 sample configuration files and image defaults, plus an Apache server-name warning. Review these separately; they did not prevent the observed application tests.

## Lessons learned

- Treat self-hosted infrastructure as a production-like service with a rollback plan.
- Confirm both the image version and the runtime version actually used by the running container.
- Validate application behavior, not only process or container status.
- Record unresolved warnings instead of declaring the environment entirely error-free.
- Maintain a separate, isolated test environment for vulnerability research; do not attempt exploit testing against the production Nextcloud instance.

## Suggested evidence for this repository

Add sanitized screenshots under `screenshots/`:

- `01-before-versions.png`
- `02-compose-image-update.png`
- `03-upgrade-log.png`
- `04-after-versions.png`
- `05-nextcloud-status.png`
- `06-file-upload-download-test.png`
- `07-schema-warning.png`

Remove domain names, personal files, credentials, tokens, internal paths if sensitive, and any private metadata before publishing.

## Follow-up

- Investigate the `oc_share_external` schema warning.
- Review Nextcloud Administration Overview warnings and logs.
- Perform and document a recovery test using a backup copy in an isolated VM.
- Record image digests for reproducibility and future patch tracking.

**Portfolio:** https://github.com/Oscarlopezbolanos
