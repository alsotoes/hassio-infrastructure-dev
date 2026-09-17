# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-09-17

### Added
- Initial release of Garage S3 Storage addon for Home Assistant
- Single-node mode with automatic default bucket creation
- Cluster mode support with bootstrap peers
- S3 API (port 3900) compatible with AWS S3 SDKs
- S3 Web UI (port 3902) accessible via Home Assistant ingress
- Admin API (port 3903) for cluster management and metrics
- Auto-generation of secrets (RPC secret, admin token, metrics token) persisted to `/data`
- Configurable storage paths, replication factor, compression
- Support for both LMDB and SQLite database engines
- Health check endpoint monitoring
- Multi-architecture support (aarch64, amd64, armv7, armhf)