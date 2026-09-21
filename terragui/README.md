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
| `metrics_backend` | Backend: `disabled`, `influxdb`, `prometheus`, `graphite` | `disabled` |
| `metrics_prefix` | Metric name prefix | `tgm` |

**Note:** Home Assistant's addon configuration UI shows all backend options at once (it doesn't support conditional fields). Only fill in the options for your selected backend.

#### InfluxDB v2 Options (when `metrics_backend: influxdb`)

| Option | Description | Default |
|--------|-------------|---------|
| `metrics_influxdb_url` | InfluxDB URL (e.g., `http://influxdb:8086`) | `` |
| `metrics_influxdb_token` | InfluxDB token | `` |
| `metrics_influxdb_org` | InfluxDB organization | `` |
| `metrics_influxdb_bucket` | InfluxDB bucket name | `tgm` |
| `metrics_influxdb_verify_ssl` | Verify SSL certificates | `true` |

#### Prometheus Pushgateway Options (when `metrics_backend: prometheus`)

| Option | Description | Default |
|--------|-------------|---------|
| `metrics_prometheus_url` | Pushgateway URL (e.g., `http://prometheus:9091`) | `` |
| `metrics_prometheus_job` | Job name | `tgm` |
| `metrics_prometheus_username` | Basic auth username | `` |
| `metrics_prometheus_password` | Basic auth password | `` |
| `metrics_prometheus_verify_ssl` | Verify SSL certificates | `true` |

#### Graphite Options (when `metrics_backend: graphite`)

| Option | Description | Default |
|--------|-------------|---------|
| `metrics_graphite_host` | Graphite host | `` |
| `metrics_graphite_port` | Graphite port | `2003` |
| `metrics_graphite_protocol` | Protocol: `tcp` or `udp` | `tcp` |

### TerraGUI Storage Backend (Execution History & Artifacts)

TerraGUI persists execution history, plan artifacts, and run metadata. By default this uses the local filesystem (`/data/terraform-graphical-manager`). You can configure cloud storage for persistence across container recreations.

| Option | Description | Default |
|--------|-------------|---------|
| `storage_backend` | Backend: local, aws, gcp, azure | `local` |
| `storage_local_path` | Local filesystem path | `/data/terraform-graphical-manager` |

#### AWS S3 (e.g., Garage S3)
| Option | Description | Default |
|--------|-------------|---------|
| `storage_s3_bucket` | S3 bucket name | `` |
| `storage_s3_access_key` | S3 access key | `` |
| `storage_s3_secret_key` | S3 secret key | `` |
| `storage_s3_region` | S3 region | `garage` |
| `storage_s3_endpoint` | S3 endpoint URL (for Garage) | `http://garage:3900` |

#### GCP Cloud Storage
| Option | Description | Default |
|--------|-------------|---------|
| `storage_gcp_bucket` | GCS bucket name | `` |
| `storage_gcp_credentials` | Service account JSON | `` |

#### Azure Blob Storage
| Option | Description | Default |
|--------|-------------|---------|
| `storage_azure_container` | Container name | `` |
| `storage_azure_connection_string` | Connection string | `` |

**Example: Garage S3 for TerraGUI execution history**
```yaml
storage_backend: "aws"
storage_s3_bucket: "tgm-history"
storage_s3_access_key: "YOUR_GARAGE_ACCESS_KEY"
storage_s3_secret_key: "YOUR_GARAGE_SECRET_KEY"
storage_s3_region: "garage"
storage_s3_endpoint: "http://garage:3900"
```

This is separate from Terraform state backend - it stores TerraGUI's own execution logs, plans, and metadata.

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

## Integration with Home Assistant Addons

### Using Garage S3 as Terraform State Backend

If you have the **Garage S3 Storage** addon running in the same HA instance, you can use it as a Terraform S3 backend for state storage.

**Garage S3 Addon Details:**
- S3 API endpoint: `http://homeassistant.local:3900` (or container name `garage` on port 3900)
- Region: `garage` (default)
- Access Key / Secret Key: From Garage addon logs or `/data/garage-secrets.env`

**Terraform Backend Configuration:**
```hcl
terraform {
  backend "s3" {
    bucket         = "terraform-state"
    key            = "production/terraform.tfstate"
    region         = "garage"
    endpoint       = "http://garage:3900"  # Use container name for inter-addon communication
    access_key     = "YOUR_GARAGE_ACCESS_KEY"
    secret_key     = "YOUR_GARAGE_SECRET_KEY"
    skip_credentials_validation = true
    skip_metadata_api_check     = true
    skip_region_validation      = true
    force_path_style            = true
  }
}
```

**Important Notes:**
- Use the **container name** (`garage`) as hostname for inter-addon communication (not `homeassistant.local`)
- Garage runs on port 3900 for S3 API
- Enable `force_path_style = true` for Garage compatibility
- Create the bucket first: `aws --endpoint-url http://garage:3900 --profile garage s3 mb s3://terraform-state`

**Alternative: Using HA Host Network**
If you prefer host networking, use:
```hcl
endpoint = "http://homeassistant.local:3900"
# or your HA IP
endpoint = "http://192.168.x.x:3900"
```

### Using InfluxDB v2 for Metrics Export

If you have **InfluxDB v2** running as an addon (e.g., `influxdb` addon), configure TerraGUI to export metrics:

**InfluxDB Addon Details:**
- Default URL: `http://influxdb:8086` (container name)
- Token: Generated on first run, check addon logs
- Org: Default `homeassistant` or your configured org
- Bucket: Create a bucket named `tgm` or your preferred name

**TerraGUI Addon Configuration:**
```yaml
metrics_enabled: true
metrics_backend: "influxdb"
metrics_prefix: "tgm"
metrics_influxdb_url: "http://influxdb:8086"
metrics_influxdb_token: "YOUR_INFLUXDB_TOKEN"
metrics_influxdb_org: "homeassistant"
metrics_influxdb_bucket: "tgm"
metrics_influxdb_verify_ssl: false
```

**Steps:**
1. Install InfluxDB addon from HA addon store
2. Configure InfluxDB: create org `homeassistant`, bucket `tgm`, generate token
3. Copy token to TerraGUI config `metrics_influxdb_token`
4. Enable metrics in TerraGUI: `metrics_enabled: true`, `metrics_backend: "influxdb"`
5. Restart TerraGUI addon

**Verify:** Check InfluxDB Data Explorer for `tgm_*` measurements.

### Using Both Together

Example Terraform workspace with Garage S3 backend and TerraGUI metrics to InfluxDB:

```hcl
# versions.tf
terraform {
  required_version = ">= 1.5"
  
  backend "s3" {
    bucket         = "terraform-state"
    key            = "${terraform.workspace}/terraform.tfstate"
    region         = "garage"
    endpoint       = "http://garage:3900"
    access_key     = var.garage_access_key
    secret_key     = var.garage_secret_key
    skip_credentials_validation = true
    skip_metadata_api_check     = true
    skip_region_validation      = true
    force_path_style            = true
  }
}

variable "garage_access_key" {
  type      = string
  sensitive = true
}

variable "garage_secret_key" {
  type      = string
  sensitive = true
}
```

Then in TerraGUI UI:
1. Add workspace pointing to your Terraform directory
2. Set variables `garage_access_key` and `garage_secret_key` in workspace variables
3. Run plan/apply - state stored in Garage S3
4. Metrics automatically exported to InfluxDB

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