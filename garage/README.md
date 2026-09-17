# Garage S3 Storage

![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]
![Supports armv7 Architecture][armv7-shield]
![Supports armhf Architecture][armhf-shield]

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[armv7-shield]: https://img.shields.io/badge/armv7-yes-green.svg
[armhf-shield]: https://img.shields.io/badge/armhf-yes-green.svg

S3-compatible distributed object storage for Home Assistant, powered by [Garage](https://garagehq.deuxfleurs.fr/).

## About

Garage is a lightweight, self-hosted S3-compatible object storage system designed for small to medium deployments. It supports clustering, replication, and multi-node configurations with high availability.

This addon provides:
- **S3 API** (port 3900) - Compatible with AWS S3 SDKs, restic, rclone, etc.
- **S3 Web UI** (port 3902) - Browser-based bucket/object management via Home Assistant ingress
- **Admin API** (port 3903) - Cluster management and metrics (internal only)
- **RPC** (port 3901) - Inter-node communication for clustering

## Installation

1. Add this repository to your Home Assistant add-on store
2. Install the "Garage S3 Storage" addon
3. Configure the options (see below)
4. Start the addon

## Configuration

### Basic Options

| Option | Default | Description |
|--------|---------|-------------|
| `mode` | `single-node` | Deployment mode: `single-node` or `cluster` |
| `s3_region` | `garage` | S3 region name (use `us-east-1` for Synology HyperBackup) |
| `s3_root_domain` | `.s3.garage.local` | S3 API domain suffix |
| `s3_web_root_domain` | `.web.garage.local` | S3 Web UI domain suffix |
| `log_level` | `info` | Log level: `debug`, `info`, `warn`, `error` |

### Storage Options

| Option | Default | Description |
|--------|---------|-------------|
| `metadata_dir` | `/data/meta` | Metadata storage path |
| `data_dir` | `/data/data` | Object data storage path |
| `db_engine` | `lmdb` | Database engine: `lmdb` or `sqlite` |
| `replication_factor` | `1` | Number of replicas (1-3) |
| `compression_level` | `1` | Compression level (0-3) |
| `block_size` | `1M` | Block size for storage |

### Network Options

| Option | Default | Description |
|--------|---------|-------------|
| `rpc_bind_addr` | `[::]:3901` | RPC listen address |
| `rpc_public_addr` | `` | Public RPC address (auto-detected if empty, required for cluster) |
| `s3_api_bind_addr` | `[::]:3900` | S3 API listen address |
| `s3_web_bind_addr` | `[::]:3902` | S3 Web UI listen address |
| `admin_bind_addr` | `[::]:3903` | Admin API listen address |

### Security (Auto-generated on first run)

| Option | Description |
|--------|-------------|
| `rpc_secret` | Cluster RPC secret (32 hex chars) |
| `admin_token` | Admin API token (base64) |
| `metrics_token` | Metrics API token (base64) |

**Secrets are auto-generated on first start and persisted to `/data/garage-secrets.env`.** You can override them by setting the options above.

### Cluster Mode

For multi-node clusters:
1. Set `mode: "cluster"`
2. Set `rpc_public_addr` to this node's public IP:3901
3. Add `bootstrap_peers` with other nodes' public keys and addresses
4. Ensure all nodes share the same `rpc_secret`

Example `bootstrap_peers`:
```yaml
bootstrap_peers:
  - "NODE_PUBLIC_KEY@PUBLIC_IP:3901"
```

## Usage

### S3 Web UI

Access via Home Assistant ingress (sidebar) or directly at `http://your-ha-ip:3902/`.

Login with your S3 credentials (access key / secret key).

### S3 Clients

**Endpoint URL:** `http://your-ha-ip:3900` (or via hostname)

**Region:** `garage` (or your configured `s3_region`)

#### Restic
```bash
restic -r s3:http://your-ha-ip:3900/bucket-name init
```

#### Rclone
```ini
[garage]
type = s3
provider = Other
env_auth = false
access_key_id = YOUR_ACCESS_KEY
secret_access_key = YOUR_SECRET_KEY
endpoint = http://your-ha-ip:3900
region = garage
```

#### AWS CLI
```bash
aws --endpoint-url http://your-ha-ip:3900 s3 ls
```

### Default Bucket (Single-node mode)

In single-node mode, a default bucket and access key are created automatically using environment variables:
- `GARAGE_DEFAULT_ACCESS_KEY`
- `GARAGE_DEFAULT_SECRET_KEY`
- `GARAGE_DEFAULT_BUCKET`

These can be set in the addon configuration if needed.

## Data Persistence

All data is stored in `/data` which maps to Home Assistant's persistent storage. Your buckets and objects survive addon updates and restarts.

## Backup

The addon data directory contains everything needed for backup:
- `/data/meta` - Metadata
- `/data/data` - Object data
- `/data/garage-secrets.env` - Credentials

Use Home Assistant's backup addon or manually copy the `/data` directory.

## Upgrading

1. Stop the addon
2. Update to new version
3. Start the addon

Garage handles schema migrations automatically.

## Troubleshooting

### Check logs
Addon log output shows Garage startup and any errors.

### Health check
The addon includes a health check on the S3 API endpoint (`/health`).

### Common issues

**"Missing /dev/fuse"** - Not required for Garage (unlike MinIO).

**Cluster won't form** - Ensure `rpc_public_addr` is correct and `rpc_secret` matches on all nodes.

**S3 client connection refused** - Check firewall, ensure port 3900 is accessible.

## Links

- [Garage Documentation](https://garagehq.deuxfleurs.fr/)
- [Garage GitHub](https://github.com/deuxfleurs/garage)
- [S3 API Reference](https://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html)

## License

This addon is licensed under the GPL-3.0 License. Garage itself is licensed under the AGPL-3.0 License.