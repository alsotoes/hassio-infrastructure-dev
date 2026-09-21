# TerraGUI Terraform Manager

![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]
![Supports armv7 Architecture][armv7-shield]
![Supports armhf Architecture][armhf-shield]

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[armv7-shield]: https://img.shields.io/badge/armv7-yes-green.svg
[armhf-shield]: https://img.shields.io/badge/armhf-yes-green.svg

A local, open-source Terraform Cloud-like UI for managing Terraform workspaces on your own machines. Built with Flask and SocketIO.

## Features

- Visual dashboard for Terraform workspaces
- Plan/Apply/Destroy execution via web UI
- Workspace management with file browser
- Variable management
- Sentinel policy integration
- Multi-cloud support (AWS, GCP, Azure)
- Terraform version management

## Configuration

### Required

| Option | Description | Default |
|--------|-------------|---------|
| `repos_root` | Path to directory containing Terraform projects | `/config/terraform` |

### Optional

| Option | Description | Default |
|--------|-------------|---------|
| `site_name` | Display name in browser tab | `Terraform Graphical Manager` |
| `repo_url` | GitHub URL for "View on GitHub" link | `https://github.com/eandresr/terraform-graphical-manager` |
| `theme` | UI theme | `terraform-cloud` |
| `max_concurrent` | Max parallel Terraform operations | `3` |
| `terraform_versions_folder` | Path to versioned Terraform binaries | `` (system only) |
| `terraform_default_version` | Default Terraform version | `system` |
| `password_hash` | PBKDF2 hash for portal lock (set via UI) | `` (no auth) |

### Sentinel Policies

| Option | Description | Default |
|--------|-------------|---------|
| `sentinel_cli_path` | Path to Sentinel CLI binary | `` (PATH) |
| `sentinel_global_policies` | Path to global policy sets | `` (disabled) |
| `sentinel_enforce_on_plan` | Run Sentinel after every plan | `false` |
| `sentinel_enforce_on_apply` | Block apply on Sentinel failures | `false` |

### Metrics

| Option | Description | Default |
|--------|-------------|---------|
| `metrics_enabled` | Enable metrics export | `false` |
| `metrics_backend` | Backend: influxdb, prometheus, graphite | `` |
| `metrics_prefix` | Metric name prefix | `tgm` |

See configuration UI for InfluxDB/Prometheus/Graphite specific options.

## Usage

### Adding Terraform Workspaces

1. Add your Terraform projects to `/config/terraform` (via Samba, SSH, or File Editor)
2. Each subdirectory with `.tf` files becomes a workspace
3. Restart the addon to pick up new workspaces

Example structure:
```
/config/terraform/
├── production/
│   ├── main.tf
│   ├── variables.tf
│   └── terraform.tfvars
├── staging/
│   ├── main.tf
│   └── variables.tf
└── development/
    └── main.tf
```

### Accessing the UI

- **Ingress (recommended)**: Click "Open Web UI" in the addon panel
- **Direct**: `http://homeassistant.local:5005/`
- **Port**: 5005

### Authentication

By default, no authentication is required. To enable:
1. Open the web UI
2. Go to Settings → Portal Lock
3. Set a password (stored as PBKDF2 hash in config)

### Terraform Versions

The addon includes the system Terraform. To use specific versions:
1. Create `/data/terraform-versions/1.7.5/terraform` (binary)
2. Set `terraform_versions_folder: /data/terraform-versions`
3. Workspaces can pin versions in their `.terraform-version` file

## Data Persistence

| Path | Purpose |
|------|---------|
| `/config/terraform` | Your Terraform projects (read/write) |
| `/data` | Terraform state, plans, Sentinel policies, metrics config |

Both paths survive addon updates and restarts.

## S3/Remote State

TerraGUI works with any Terraform backend (S3, GCS, Azure, Consul, etc.). Configure your backend in your Terraform files as usual.

## Troubleshooting

### Workspace not showing
- Ensure directory contains at least one `.tf` file
- Check `/config/terraform` permissions
- Restart addon

### Plan/Apply fails
- Check addon logs for Terraform output
- Ensure required providers are available
- Verify Terraform version compatibility

### Port already in use
- Default port is 5005
- Check for conflicts with other addons

## Support

- **Upstream**: https://github.com/eandresr/terraform-graphical-manager
- **Issues**: https://github.com/alsotoes/hassio-infrastructure-dev/issues