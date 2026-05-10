# CyberLab Monitoring Demo

This project is a local monitoring demo for the CyberLab monitoring task.

It uses Docker Compose to run three services:

- Grafana
- Prometheus
- Blackbox Exporter

The dashboard shows whether the configured Bank and Hospital targets are `UP` or `DOWN`.

## What This Demo Does

The monitoring flow is:

```text
Bank/Hospital targets
-> Blackbox Exporter checks whether each target is reachable
-> Prometheus stores the check result
-> Grafana displays the result in a dashboard
```

The current targets are demo placeholders. They are useful for checking that the local monitoring stack works. To monitor the real CyberLab environment, replace the placeholder targets in `targets.yml` with real CyberLab IP addresses, domain names, or service URLs.

## Requirements For Windows

Install the following software before running the project:

1. Git

Download:

```text
https://git-scm.com/download/win
```

2. Docker Desktop for Windows

Download:

```text
https://www.docker.com/products/docker-desktop/
```

Docker Desktop may ask you to enable or install WSL 2. Follow the Docker Desktop instructions if prompted.

3. PowerShell or Windows Terminal

PowerShell is already included with Windows.

## Check The Installation

Open PowerShell and run:

```powershell
git --version
docker --version
docker compose version
```

Each command should print a version number.

If Docker commands fail, open Docker Desktop and wait until it says Docker is running.

## Download Or Open The Project

If the project is provided as a zip file:

1. Extract the zip file.
2. Open PowerShell.
3. Go into the extracted project folder.

Example:

```powershell
cd Desktop
cd monitoring-lab-main
```

If the project is downloaded from GitHub:

```powershell
git clone <repository-url>
cd <repository-folder>
```

If the repository contains a `monitoring-lab-main` folder, enter it:

```powershell
cd monitoring-lab-main
```

The folder you run commands from should contain:

```text
docker-compose.yml
prometheus.yml
blackbox.yml
targets.yml
```

## Start The Monitoring Stack

Run:

```powershell
docker compose up -d
```

The first run may take a few minutes because Docker needs to download the container images.

## Check The Services

Run:

```powershell
docker compose ps
```

You should see these services running:

```text
prometheus
grafana
blackbox
```

## Open Grafana

Open this address in a browser:

```text
http://localhost:3300
```

Login:

```text
Username: admin
Password: admin
```

If Grafana asks you to change the password, you can either change it or skip the step.

## Open The CyberLab Dashboard

Open:

```text
http://localhost:3300/d/cyberlab-blackbox/cyberlab
```

The dashboard contains:

- Service Status
- Uptime History
- Response Time

The `Service Status` panel shows whether the configured Bank and Hospital targets are `UP` or `DOWN`.

## Open Prometheus

Open:

```text
http://localhost:9090
```

To check Bank and Hospital target status, run this query:

```promql
probe_success{job="blackbox_http", type=~"bank|hospital"}
```

Result meaning:

```text
1 = UP
0 = DOWN
```

## Open Blackbox Exporter

Open:

```text
http://localhost:9115
```

This page confirms that Blackbox Exporter is running.

## Stop The Monitoring Stack

Run:

```powershell
docker compose down
```

## Important Files

### `targets.yml`

Defines the targets to monitor.

Current demo targets include:

- Bank Master
- Bank Worker 1
- Bank Worker 2
- Hospital Master
- Hospital Worker 1
- Hospital Worker 2

To monitor real CyberLab systems, replace the placeholder URLs in this file.

Example:

```yaml
- targets:
    - http://<real-bank-master-ip-or-domain>:<port>
  labels:
    name: Bank Master
    type: bank
    role: Master
```

After editing `targets.yml`, restart Prometheus:

```powershell
docker compose restart prometheus
```

### `prometheus.yml`

Configures Prometheus to use Blackbox Exporter and read targets from `targets.yml`.

### `blackbox.yml`

Configures Blackbox Exporter probe modules, including HTTP and TCP checks.

### `grafana/provisioning/datasources/prometheus.yml`

Automatically configures Grafana to connect to Prometheus.

### `grafana/provisioning/dashboards/dashboards.yml`

Automatically configures Grafana to load dashboard JSON files.

### `grafana/dashboards/cyberlab-blackbox.json`

The Grafana dashboard used by this demo.

## Troubleshooting

If Grafana does not open, check whether containers are running:

```powershell
docker compose ps
```

If a service is not running, check its logs:

```powershell
docker compose logs grafana
docker compose logs prometheus
docker compose logs blackbox
```

If `docker compose up -d` fails, make sure Docker Desktop is open and running.

If the dashboard shows `DOWN`, check that the target URL in `targets.yml` is reachable from inside the Docker network.

## Current Project Status

The local demo is configured to show Bank and Hospital nodes in Grafana.

The current targets are placeholders for local testing. The next step for real CyberLab monitoring is to replace those placeholders with real CyberLab target addresses.
