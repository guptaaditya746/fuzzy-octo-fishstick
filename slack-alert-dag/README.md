# Slack Alert DAG

This project runs an Airflow DAG that intentionally fails tasks and sends failure alerts to Slack via Incoming Webhook.

## 1. Prerequisites

- Docker and Docker Compose installed
- A Slack workspace where you can create/install apps

## 2. Create Slack Incoming Webhook

1. Open: https://api.slack.com/apps
2. Click `Create New App`.
3. Choose `From scratch`, enter app name, select workspace.
4. In app settings, open `Incoming Webhooks`.
5. Turn `Activate Incoming Webhooks` to `On`.
6. Click `Add New Webhook to Workspace`.
7. Select the target Slack channel and authorize.
8. Copy the generated webhook URL.

## 3. Add Webhook to DAG

Open file:

- `dags/slack-alert-dag.py`

Replace:

```python
SLACK_WEBHOOK_URL = "---PASTE-YOUR-WEBHOOK-HERE---"
```

with your real webhook URL:

```python
SLACK_WEBHOOK_URL = "https://hooks.slack.com/services/XXX/YYY/ZZZ"
```

## 4. Start Airflow Stack

From this folder (`slack-alert-dag`), run:

```bash
docker compose up -d airflow-init airflow-webserver airflow-scheduler
```

Check container status:

```bash
docker compose ps
```

Expected:
- `airflow-webserver` should become `healthy`
- `airflow-scheduler` should be `up`

## 5. Open Airflow UI

Default URL:

- http://localhost:8081

Default admin credentials from `docker-compose.yaml`:

- Username: `admin`
- Password: `airflow`

## 6. Run the DAG

1. In Airflow UI, find DAG: `SlackAlertTestDag`
2. Turn it ON (toggle).
3. Click `Trigger DAG`.
4. Wait for task failures (this DAG is designed to fail some tasks).
5. Check your Slack channel for alert message.

## 7. Useful Commands

View logs:

```bash
docker compose logs --tail=200 airflow-webserver
docker compose logs --tail=200 airflow-scheduler
```

Restart services:

```bash
docker compose restart airflow-webserver airflow-scheduler
```

Stop everything:

```bash
docker compose down
```

Stop and remove local volumes/data:

```bash
docker compose down -v
```

## 8. Troubleshooting

- Port `8081` already in use:
  - Change `docker-compose.yaml` port mapping from `8081:8080` to `8082:8080`, then open `http://localhost:8082`.
- No Slack message:
  - Re-check webhook URL in `dags/slack-alert-dag.py`.
  - Confirm DAG run actually failed.
  - Check scheduler logs for callback errors.
- Webhook pasted but still silent:
  - Make sure Slack app is installed to the same workspace/channel.

## Reference

- Video: https://youtu.be/2Cbz9Z06KJo
