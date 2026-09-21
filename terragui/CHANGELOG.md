# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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