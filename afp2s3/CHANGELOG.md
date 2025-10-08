# Changelog

## 0.1.14

- Fix AFP fallback logic: remove invalid `afpfs-ng` binary check and prefer `mount_afp` by default.
- Gate `afp_client` fallback behind `ALLOW_AFP_CLIENT_FALLBACK=true` due to strict `/dev/fuse` checks.
- Add best-effort `chgrp fuse /dev/fuse` and clearer fuse permission diagnostics in init script.

## 0.1.13

- Limit supported architectures to `aarch64` only and update build matrix accordingly.

## 0.1.12

- Elevate container privileges to resolve FUSE access issues: set `privileged: true`, keep `/dev/fuse` device, and add `full_access: true`.
- Update README with guidance on disabling Protection mode and FUSE requirements.
- Ensure PATH includes `/usr/local/bin` in init script for reliable AFP tooling discovery.

## 0.1.11

- Improve AFP mounting robustness by trying `mount_afp` → `afpfs-ng` → `afp_client` in order, with fallbacks on failure to avoid hard-failing on strict `/dev/fuse` checks by `afp_client`.
- Keep enhanced `/dev/fuse` diagnostics and permission adjustments from 0.1.10.

## 0.1.10

- Add-on startup hardening for FUSE:
  - Require `/dev/fuse` via `devices` and grant `SYS_ADMIN` in `privileged`.
  - Enable s6 init with `init: true` in `config.yaml`.
  - Init script logs /dev/fuse mode/uid/gid, attempts to set mode to 0666, and warns on failure.
- Documentation: installation note for FUSE device/capability.

## 0.1.9

- Switch to s6 supervision: add `cont-init.d/10-afp-mount` and `services.d/minio/run`.
- Read configuration via bashio; remove legacy `run.sh` entrypoint.
- Remove references to `s3_access_key` and `s3_secret_key` from docs.
- Add `afp_share` optional config; compose final AFP URL from server + share.
- Add `afp_marker` optional config; allow disabling marker check with empty string.
- Warn if `afp_server` already includes a share/path and `afp_share` is also set.
- Update Dockerfile to start with `/init` and ensure scripts are executable.

## 0.1.8

- Using github repo and patch for arm64

## 0.1.7

- Refactor Dockerfile to fix build issues on arm64.
- Use multi-stage build to compile afpfs-ng from source.
- Use alpine-based image for the build stage.
- Remove patch for afpfs-ng, as it is no longer needed.
- Split RUN command in build stage into multiple commands.
- Add DESTDIR to make install command.

## 0.1.6

- Change the builder layer to arm64v8/ubuntu:jammy

## 0.1.5

- Build `afpfs-ng` from source to guarantee AFP mount tools are shipped with the image.
- Patch upstream `afpfs-ng` to fix a compile-time error when closing Desktop database handles.

## 0.1.4

- Validate AFP mounts by requiring the `.com.apple.timemachine.supported` marker file from the remote share.

## 0.1.3

- Fix AFP mounting on systems where the `afp_client` binary is unavailable by adding fallbacks to other AFP utilities.

## 0.1.2

- Disable the AppArmor profile to increase compatibility with environments that lack AppArmor support.

## 0.1.1

- Add AFP mount validation to abort startup if the share cannot be mounted.
- Ensure the image includes `mountpoint` by installing `util-linux`.

## 0.1.0

- Initial release
- This add-on mounts a remote AFP (Apple Filing Protocol) share into the add-on's filesystem and exposes it as an S3-compatible object storage endpoint using Minio.
