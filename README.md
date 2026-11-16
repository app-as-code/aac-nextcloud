<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <img src="assets/icon.svg" alt="Nextcloud logo" width="128">
  </picture>
</p>

<h1 align="center">aac-nextcloud</h1>

<p align="center">Self-hosted deployment definition for Nextcloud, a self-hosted file sync, share and collaboration platform.</p>

<p align="center">
  <a href="https://github.com/nextcloud/server"><img alt="Upstream" src="https://img.shields.io/badge/upstream-nextcloud%2Fserver-blue?logo=github"></a>
  <a href="https://hub.docker.com/_/nextcloud"><img alt="Image" src="https://img.shields.io/badge/image-nextcloud%3A34-2496ED?logo=docker&logoColor=white"></a>
  <a href="https://github.com/nextcloud/server/blob/master/COPYING"><img alt="License" src="https://img.shields.io/badge/license-AGPL--3.0-green"></a>
</p>

## About

Nextcloud is a self-hosted platform for storing, syncing and sharing files, with a web
interface, desktop and mobile clients and a WebDAV interface. Its app system adds calendar,
contacts, chat and video calls, document editing and many other features. Data stays on
the server that you control.

Key features (as provided by upstream):

- File storage with sync clients for desktop and mobile
- Sharing of files and folders with users, groups and public links
- Calendar, contacts and tasks through WebDAV-compatible clients
- App ecosystem for collaboration, chat and document editing
- Server-side encryption options and granular access control

## Upstream

- Project: <https://github.com/nextcloud/server>
- Website: <https://nextcloud.com>
- Documentation: <https://docs.nextcloud.com>
- Container image: `nextcloud:34` (official image on Docker Hub)
- Database: MariaDB `12.3.1-ubi10-rc` (deployed as `aac-nextcloud-db`)
- Cache: Redis `8.6.1` (deployed as `aac-nextcloud-redis`)
- License: AGPL-3.0 (upstream project)

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run, the external networks used for routing must exist first:

```bash
docker network create services-secured
docker network create services-exposed

# Provide the values listed under "Configuration" in .env / stack.env
docker compose up -d
```

Secrets are not stored in this repository. Provide them through the deployment pipeline
or create the `.env` and `stack.env` files yourself (see below). Do not commit them.

## Configuration

Environment variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment,
and secret values are marked as such):

| Variable | Default | Description |
|---|---|---|
| `NEXTCLOUD_ADMIN_USER` | `admin` | Initial administrator user name |
| `NEXTCLOUD_ADMIN_PASSWORD` | `CHANGE_ME` (secret) | Initial administrator password |
| `NEXTCLOUD_TRUSTED_DOMAINS` | `CHANGE_ME` | Host names the instance accepts requests for |
| `OVERWRITEHOST` | `CHANGE_ME` | Public host name used to build URLs |
| `OVERWRITEPROTOCOL` | `https` | Protocol used to build URLs |
| `OVERWRITECLIURL` | `CHANGE_ME` | Base URL used by the command-line tool |
| `TRUSTED_PROXIES` | `CHANGE_ME` | Reverse proxy addresses trusted for client IP detection |
| `SERVICE_IMAGE_TAG` | `CHANGE_ME` | Nextcloud image tag passed to the deployment |
| `MYSQL_HOST` | `CHANGE_ME` | Database host (the MariaDB dependency) |
| `MYSQL_DATABASE` | from `MARIADB_DATABASE` | Database name |
| `MYSQL_USER` | from `MARIADB_USER` | Database user |
| `MYSQL_PASSWORD` | `CHANGE_ME` (secret) | Database user password |
| `MARIADB_DATABASE` | `nextcloud` | Database name created in the MariaDB dependency |
| `MARIADB_USER` | `nextcloud` | Database user created in the MariaDB dependency |
| `MARIADB_PASSWORD` | `CHANGE_ME` (secret) | Password of the database user |
| `MARIADB_ROOT_PASSWORD` | `CHANGE_ME` (secret) | Root password of the MariaDB dependency |
| `MARIADB_ALLOW_EMPTY_PASSWORD` | `false` | Allow an empty root password (keep `false`) |
| `MARIADB_RANDOM_ROOT_PASSWORD` | `false` | Generate a random root password |
| `REDIS_HOST` | `CHANGE_ME` | Redis host (the Redis dependency) |
| `REDIS_HOST_PORT` | `6379` | Redis port |
| `NEXTCLOUD_DATA_DIR` | `/mnt/data` | Data directory inside the container |
| `NEXTCLOUD_INIT_HTACCESS` | `false` | Initialize the `.htaccess` file on start |
| `NEXTCLOUD_INIT_LOCK` | `false` | Lock the initialization step |
| `NEXTCLOUD_UPDATE` | `0` | Run the automatic update on container start |
| `NEXTCLOUD_UPLOAD_MAX_FILESIZE` | `100G` | Maximum upload size per file |
| `NEXTCLOUD_MAX_FILE_UPLOADS` | `20000` | Maximum number of files per upload request |
| `PHP_MEMORY_LIMIT` | `1G` | PHP memory limit |
| `PHP_UPLOAD_LIMIT` | `100G` | PHP upload limit |
| `APACHE_DISABLE_REWRITE_IP` | `1` | Disable Apache client IP rewriting |
| `HOMEPAGE_WIDGET_USERNAME` | `admin` | User name for the Homepage dashboard widget |
| `HOMEPAGE_WIDGET_PASSWORD` | `CHANGE_ME` (secret) | Password for the Homepage dashboard widget |
| `TZ` | `Europe/Berlin` | Time zone |

Ports:

| Container port | Published port | Protocol | Purpose |
|---|---|---|---|
| `80` | per deployment (`external_port` in `service.yml`) | TCP | Web interface (`web`) |
| `443` | per deployment (`external_port` in `service.yml`) | TCP | Web interface over TLS (`web`) |

Volumes:

| Mount | Description |
|---|---|
| `html` -> `/var/www/html` | Nextcloud application and web root (owner UID/GID 33) |
| `galaxy_01` -> `/mnt/data` | Bind mount for the Nextcloud data directory |
| `db` -> `/var/lib/mysql` | Persistent storage for the MariaDB dependency |
| `redis-data` -> `/data` | Persistent storage for the Redis dependency |

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-nextcloud>.

## Credits and license

Nextcloud is developed by Nextcloud GmbH and the Nextcloud community and licensed under
AGPL-3.0. This repository only contains the deployment definition for it and is not a fork
of the application. The logo is provided by the Nextcloud project; see
[`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md) for icon sources and licenses.
