# Big Data Engineering Project

This repository contains the notebooks and data files for the Vienna City Marathon big data engineering project. The notebooks from the homeworks have been inkluded and updated. 

"visualizations_for_presentation" is the only new notebook and contains a lot of graphs and tables created from the collected data, some (but not all) have been used for the presentation.

## Project Structure

- `CSV/`: input and generated CSV files used by the notebooks
- `web_scraping Group5 2017-2026`: webscrapes marathon data from 2017-2026
- `weatherapi.ipynb`: fetches and visualizes marathon-day weather data
- `luftquali.ipynb`: fetches and visualizes marathon-day air quality data
- `visualizations_for_presentation.ipynb`: presentation-ready plots and summary tables
- `spark.ipynb`: Spark notebook
- `kafka_and_spark_streaming.ipynb`: Kafka and Spark streaming notebook
- `kafka_and_spark_streaming.local.example.json`: template for local/private streaming configuration
- `docker-compose.yml`: Docker setup for Spark/Jupyter and optional Kafka

## Data Files

The notebooks expect the CSV files to be available in the `CSV/` folder. Important files include:

- `CSV/vienna_city_marathon_all_years_participants.csv`
- `CSV/weather.csv`
- `CSV/vienna_marathon_component_trends.csv`

## Recommended Setup: Docker

Docker is the easiest way to run the Spark-related notebooks because it provides Jupyter Lab and PySpark without requiring a local Spark installation.

### 1. Prerequisites

Install:

- Docker Desktop or Docker Engine
- Docker Compose

Check that Docker works:

```bash
docker --version
docker compose version
```

### 2. Start Jupyter Lab with Spark

From the project root folder, run:

```bash
docker compose up spark
```

Open Jupyter Lab in the browser at:

```text
http://localhost:8888
```

### 3. Run the Spark Notebook

While Spark jobs are running, the Spark UI is available at:

```text
http://localhost:4040
```

### 4. Run the Kafka and Spark Streaming Notebook

Start Spark/Jupyter first:

```bash
docker compose up spark
```

In a second terminal, start Kafka:

```bash
docker compose --profile streaming up -d kafka
```

Then open and run:

```text
kafka_and_spark_streaming.ipynb
```

The local Docker setup uses:

- Spark inside the `spark` container
- Kafka as `kafka:9092` from inside Docker
- Kafka as `localhost:9092` from the host machine

Kafka data is stored in the Docker volume `kafka_data`.

To stop the containers:

```bash
docker compose down
```

To also remove Kafka's persisted Docker volume:

```bash
docker compose down -v
```

## Running Non-Spark Notebooks Locally

The analysis and visualization notebooks can also be run with a local Python environment.

## Recommended Notebook Order

For reviewing the project results, use this order:

1. `weatherapi.ipynb`
	- Optional if `CSV/weather.csv` already exists.
	- Fetches or loads weather data for marathon days.
	- Requires a local weather API key only when fetching new data.

2. `visualizations_for_presentation.ipynb`
	- Uses the CSV files and creates presentation-ready plots and tables.
	- This is the main notebook for inspecting the final visualizations.

3. `spark.ipynb`
	- Run through Docker/Jupyter if Spark functionality is needed.

4. `kafka_and_spark_streaming.ipynb`
	- Run only after starting the Docker Kafka service.

## Local Credentials and Private Configuration

No access credentials are included in this repository.

If a notebook requires a private API key or private cluster configuration, create a local file on your own machine. Do not commit this file.
