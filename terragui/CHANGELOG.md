# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.6] - 2026-09-21

### Fixed
- Set `init: false` to disable Docker's tini init (--init)
- HA supervisor runs containers with tini as PID 1, conflicting with s6-overlay's /init
- s6-overlay's s6-overlay-suexec requires being PID 1
- Same fix as Garage addon (v0.1.6)

## [0.1.5] - 2026-09-21

### Added
- Plain text `password` option in addon config (replaces `password_hash`)
- Auto-generates PBKDF2-SHA256 hash at startup using Python 3 (installed in Dockerfile)
- User-friendly: set password directly in HA config UI

## [0.1.4] - 2026-09-21

### Fixed
- Removed redundant `metrics_enabled` boolean option
- Metrics now controlled solely by `metrics_backend` (disabled|influxdb|prometheus|graphite)
- Updated README and cont-init.d to reflect single-source-of-truth

## [0.1.3] - 2026-09-21

### Fixed
- Metrics backend configuration: explicit "disabled" option, all backend options documented
- HA addon config limitation: all backend options shown at once (no conditional UI)
- README: detailed per-backend configuration tables

## [0.1.2] - 2026-09-21

### Fixed
- Include application source files in build context (app/, templates/, static/, config/, run.py, requirements.txt, pyproject.toml)
- Fix Dockerfile COPY paths for HA build system
- Add proper labels and ARG handling matching etcd pattern

## [0.1.1] - 2026-09-21

### Added
- TerraGUI storage backend configuration (execution history, plan artifacts, run metadata)
- Support for AWS S3 (Garage), GCP Cloud Storage, Azure Blob Storage
- Environment variable configuration: TERRAFORM_GRAPHICAL_BACKEND
- Local filesystem fallback with configurable path

## [0.1.0] - 2026-09-21

### Added
- Initial release of TerraGUI Terraform Manager addon for Home Assistant
- Web UI for managing Terraform workspaces (port 5005)
- Configurable repos_root for Terraform projects
- Support for Terraform version management
- Sentinel policy integration
- Metrics export (InfluxDB, Prometheus, Graphite)
- Portal lock authentication (set via UI)
- Ingress support for Home Assistant dashboard
- Multi-architecture support (aarch64, amd64, armv7, armhf)
- Data persistence via /config and /data maps