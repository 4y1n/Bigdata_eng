# Bigdata Engineering Project

## Docker usage

This project uses one shared Docker Compose file for both notebook modes.

### 1. Spark notebooks only

Use this for `spark.ipynb` and the other notebooks that do not need Kafka.

```bash
docker compose up spark
```

Open Jupyter Lab at `http://localhost:8888`.

### 2. Kafka + Spark streaming notebook

Use this for `kafka_and_spark_streaming.ipynb`.

```bash
docker compose up spark
```

In a second terminal, start Kafka:

```bash
docker compose --profile streaming up -d kafka
```

The streaming notebook is configured for local mode with:

- Spark running inside the `spark` container
- Kafka reachable as `kafka:9092` from inside Docker
- Kafka reachable as `localhost:9092` from the host

### Local config for credentials

Sensitive cluster and Kafka settings for `kafka_and_spark_streaming.ipynb` can be stored in `kafka_and_spark_streaming.local.json`.

- `kafka_and_spark_streaming.local.json` is ignored by Git.
- `kafka_and_spark_streaming.local.example.json` is the tracked template.
- For local Docker usage, the notebook still defaults to local mode even without the local config file.
- For cluster usage, fill the template locally and set the required credentials there or via environment variables.

### Notes

- The full project folder is mounted into the container at `/home/jovyan/work`.
- CSV inputs are available under `/home/jovyan/work/CSV`.
- Spark UI is exposed on `http://localhost:4040` while a Spark job is running.
- Kafka data is persisted in the `kafka_data` Docker volume.
