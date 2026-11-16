# Changelog

All notable changes to this deployment definition are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- README with configuration reference, port and volume tables, and project icon.
- Icon assets (light and dark variants) with source and license notes.

## [0.0.1]

### Added

- Initial App-as-Code service definition for Nextcloud 34 with MariaDB and Redis dependencies.
- Web interface on ports 80 and 443 with Traefik routing and automatic DNS.
- Bind-mounted data directory and persistent volumes for the application, database and cache.
