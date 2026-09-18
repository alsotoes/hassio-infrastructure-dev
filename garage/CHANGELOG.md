# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.6] - 2026-09-18

### Fixed
- Set `init: false` in config.yaml to disable Docker's tini init (--init)
- HA supervisor was running container with tini as PID 1, conflicting with s6-overlay's /init
- s6-overlay's s6-overlay-suexec requires being PID 1

## [0.1.5] - 2026-09-18

### Fixed
- Use HA base image (aarch64-base:3.18) with s6-overlay 3.1.6.2 like etcd addon
- Install s6-overlay 3.1.2.1 on top (matching etcd pattern exactly)
- Resolves "s6-overlay-suexec: fatal: can only run as pid 1" by matching working etcd pattern

## [0.1.4] - 2026-09-18

### Fixed
- Use base image's pre-installed s6-overlay (3.2.3.0) instead of installing custom version
- Resolves "s6-overlay-suexec: fatal: can only run as pid 1" when HA supervisor runs container with Docker's init (tini)
- Removed custom s6-overlay installation that conflicted with HA's container init system

## [0.1.3] - 2026-09-18

### Fixed
- Remove SHELL directive from Dockerfile for OCI compatibility
- Match etcd Dockerfile pattern exactly for s6-overlay extraction

## [0.1.2] - 2026-09-18

### Fixed
- Disable AppArmor (apparmor: false) to resolve "exec /init failed: Permission denied" on arm64
- s6-overlay compatibility issues with custom/default AppArmor profiles

## [0.1.1] - 2026-09-18

### Fixed
- Remove restrictive custom AppArmor profile that blocked s6-overlay execution
- Use HA default AppArmor profile (apparmor: true) for s6-overlay compatibility

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