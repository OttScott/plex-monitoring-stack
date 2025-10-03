# Plex Monitoring Stack

A comprehensive Docker Compose setup for monitoring an externally installed Plex media server.

## Services

- **Prometheus**: Metrics collection and storage
- **Grafana**: Visualization and dashboarding
- **Dozzle**: Docker container log viewer
- **Node Exporter**: System metrics exporter
- **DCGM Exporter**: NVIDIA GPU metrics exporter
- **SmartCTL Exporter**: Disk health metrics exporter
- **cAdvisor**: Container metrics exporter
- **Plex Prometheus Exporter**: Plex-specific metrics exporter

## Prerequisites

- Docker and Docker Compose
- **External Plex Media Server installation** (not included in this stack)
- Plex authentication token for the exporter
- Environment variables configured (see Setup section)

## Setup

1. **Ensure Plex is installed externally**: This stack requires a Plex Media Server to be installed separately (either on the same host or accessible on your network). This stack will monitor that external Plex instance.

2. Clone this repository

3. Copy the environment file templates and configure them:

   ```bash
   # Create environment files for each service
   cp plex-prometheus-exporter/.env.example plex-prometheus-exporter/.env
   cp prometheus/.env.example prometheus/.env
   cp grafana/.env.example grafana/.env
   # ... configure other .env files as needed
   ```

4. Configure the Plex Prometheus Exporter to connect to your external Plex server:

   Edit `plex-prometheus-exporter/.env` and set:
   - `PLEX_SERVER`: URL to your Plex server (e.g., `http://host.docker.internal:32400` for local installation, or `http://192.168.1.100:32400` for remote)
   - `PLEX_TOKEN`: Your Plex authentication token ([how to find your token](https://support.plex.tv/articles/204059436-finding-an-authentication-token-x-plex-token/))

5. Set the required environment variables in your shell or create a `.env` file in the root:

   ```bash
   export MEDIA_SERVER_PATH="/path/to/this/directory"
   ```

6. Create the data directories that will be mounted:

   ```bash
   mkdir -p prometheus/data
   mkdir -p grafana/data
   mkdir -p dozzle/data
   ```

7. Configure user permissions for data directories:

   ```bash
   sudo chown -R $(id -u):$(id -g) prometheus/data
   sudo chown -R $(id -u):$(id -g) grafana/data
   ```

8. Start the services:

   ```bash
   docker compose up -d
   ```

## Access Points

- **Plex**: Access via your external Plex installation (typically <http://localhost:32400/web> if running on the same machine)
- **Grafana**: <http://localhost:3000>
- **Prometheus**: <http://localhost:9090>
- **Dozzle**: <http://localhost:8080>

## Configuration Files

- `prometheus/config/prometheus.yml`: Prometheus configuration
- `prometheus/config/rules/`: Prometheus alerting rules
- `grafana/provisioning/`: Grafana datasources and dashboards
- `dozzle/data/users.yaml`: Dozzle user authentication

## Dashboards

This repository includes pre-configured Grafana dashboards for monitoring your Plex monitoring stack. For the most up-to-date Plex monitoring dashboards and additional configuration options, visit the [prometheus-plex-exporter repository](https://github.com/timothystewart6/prometheus-plex-exporter).

## Data Persistence

The following directories contain persistent data and are mounted from the host:

- `prometheus/data/`: Time series data
- `grafana/data/`: Dashboards, users, and settings
- `dozzle/data/`: Log viewer configuration

## Notes

- **Plex is NOT included in this stack** - you must have Plex installed separately
- The Plex Prometheus Exporter connects to your external Plex server to collect metrics
- GPU support is configured for NVIDIA cards (for monitoring GPU usage)
- Prometheus retains data for 30 days
- All services are configured with security best practices (no-new-privileges)
- Services are set to restart unless explicitly stopped
- This stack provides comprehensive monitoring for Plex server performance, resource usage, and streaming analytics

## Further reading

For a walk-through and explanation of the design and dashboards used by this stack, see the accompanying blog post:

- ["Monitor your Plex Server like a Pro"](https://technotim.live/posts/monitor-your-plex-server-like-a-pro/)
