# Monitoring DAG (Airflow + Prometheus + Grafana)

This folder runs Airflow with StatsD metrics, Prometheus scraping, and Grafana dashboards.

## Services and Ports

- Airflow Web UI: `http://localhost:8082`
- Prometheus: `http://localhost:9092`
- StatsD Exporter metrics: `http://localhost:9102/metrics`
- Grafana: `http://localhost:3000`

## What is configured

- Airflow emits metrics to StatsD exporter (`statsd-exporter:9125`)
- StatsD exporter exposes Prometheus metrics on `:9102/metrics`
- Prometheus scrapes `statsd-exporter:9102`
- Grafana can query Prometheus for dashboards

## Prerequisites

- Docker and Docker Compose installed
- Ports `8082`, `9092`, `9102`, `3000`, and `3306` available

## 1. Start the stack

From this folder (`Monitoring_Dag`):

```bash
docker compose up -d mysql airflow-init airflow-scheduler airflow-webserver statsd-exporter prometheus grafana
```

Check status:

```bash
docker compose ps
```

Expected:
- `mysql` is `healthy`
- `airflow-webserver` is `healthy`
- `airflow-scheduler` is `up`
- `statsd-exporter`, `prometheus`, and `grafana` are `up`

## 2. Log in to Airflow

Open:

- `http://localhost:8082`

Default admin user created by `airflow-init`:

- Username: `admin`
- Password: `airflow`

## 3. Verify Prometheus target is UP

Open:

- `http://localhost:9092/targets`

Expected target:

- `http://statsd-exporter:9102/metrics` with state `UP`

## 4. Verify StatsD metrics flow

Check exporter metrics:

```bash
curl -s http://localhost:9102/metrics | grep statsd_exporter_samples_total
```

When scheduler is emitting metrics, this counter should be greater than `0`.

Check heartbeat metric:

```bash
curl -s http://localhost:9102/metrics | grep af_agg_scheduler_heartbeat
```

Note: the metric is mapped as `af_agg_scheduler_heartbeat` in this repo.

## 5. Grafana login

Open:

- `http://localhost:3000`

Default credentials:

- Username: `admin`
- Password: `grafana`

Add Prometheus data source URL:

- `http://prometheus:9090` (inside docker network)

## Useful commands

View logs:

```bash
docker compose logs --tail=200 airflow-scheduler
docker compose logs --tail=200 airflow-webserver
docker compose logs --tail=200 mysql
docker compose logs --tail=200 prometheus
docker compose logs --tail=200 statsd-exporter
```

Restart specific services:

```bash
docker compose restart airflow-scheduler airflow-webserver prometheus
```

Stop stack:

```bash
docker compose down
```

Stop and delete volumes (fresh reset):

```bash
docker compose down -v
```

## Troubleshooting

- Prometheus target `DOWN` with `host.docker.internal` error:
  - Use service DNS in `configs/prometheus.yaml`:
  - `targets: ["statsd-exporter:9102"]`

- MySQL marked `unhealthy` but DB is running:
  - Use `mysqladmin ping` healthcheck (already set in `docker-compose.yaml`)

- Airflow not opening on `8081`:
  - This stack uses `8082` for Airflow webserver to avoid conflicts

- `af_agg_scheduler_heartbeat` missing:
  - Ensure `airflow-scheduler` is actually running
  - Confirm `statsd_exporter_samples_total` is increasing
  - Check scheduler logs for startup/runtime errors

## Files to know

- `docker-compose.yaml`
- `configs/airflow.cfg`
- `configs/statsd.yaml`
- `configs/prometheus.yaml`
- `.env`
