# Apache Airflow Practice Repository

This repository contains multiple small Airflow projects for learning orchestration patterns:

- `DAG_ingestion_pipeline`: basic ingestion flow
- `slack-alert-dag`: failure alerting to Slack
- `Monitoring_Dag`: Airflow metrics with StatsD, Prometheus, and Grafana

## Ideas To Implement Next

1. Add retries and exponential backoff in all DAGs to make failures more realistic.
2. Replace hardcoded secrets (for example Slack webhook URLs) with Airflow Connections and environment variables.
3. Create a shared utilities module for reusable tasks (logging, notifications, data validation).
4. Add data quality checks after ingestion (row count checks, schema validation, null checks).
5. Add branching logic using `BranchPythonOperator` for success/failure or conditional execution paths.
6. Add SLA and execution timeout settings, then alert on SLA misses.
7. Build a dashboard in Grafana for DAG success rate, task duration, and scheduler heartbeat trends.
8. Add integration tests for DAG parsing and task dependency validation in CI.
9. Add a simple backfill strategy and documentation for historical reprocessing.
10. Add one end-to-end DAG combining ingestion + validation + Slack notifications.

## Suggested Order

Start with ideas `2`, `4`, and `8` first. They provide the biggest improvement in reliability and maintainability with low implementation risk.
