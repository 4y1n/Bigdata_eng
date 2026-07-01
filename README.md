# Big Data Engineering Project

This repository contains the notebooks and data files for the Vienna City Marathon big data engineering project. The instructions below explain how to run the notebooks on a new computer without including any private credentials.

## Project Structure

- `CSV/`: input and generated CSV files used by the notebooks
- `weatherapi.ipynb`: fetches and visualizes marathon-day weather data
- `visualizations_for_presentation.ipynb`: presentation-ready plots and summary tables
- `spark.ipynb`: Spark notebook
- `kafka_and_spark_streaming.ipynb`: Kafka and Spark streaming notebook
- `kafka_and_spark_streaming.local.example.json`: template for local/private streaming configuration
- `docker-compose.yml`: Docker setup for Spark/Jupyter and optional Kafka

## Recommended Setup: Docker

Docker is the easiest way to run the Spark-related notebooks because it provides Jupyter Lab and PySpark without requiring a local Spark installation.

### 1. Prerequisites

Install:

- Docker Desktop or Docker Engine
- Docker Compose
- Git, if the project is cloned from a repository

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

If Jupyter prints a token URL in the terminal, use that URL to open the notebook environment.

The full project folder is mounted inside the container at:

```text
/home/jovyan/work
```

CSV files are available inside Docker at:

```text
/home/jovyan/work/CSV
```

### 3. Run the Spark Notebook

In Jupyter Lab, open:

```text
spark.ipynb
```

Run the notebook cells from top to bottom.

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

### 1. Create a Virtual Environment

From the project root folder:

```bash
python -m venv .venv
source .venv/bin/activate
```

On Windows PowerShell:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

### 2. Install Python Packages

Install the packages used by the notebooks:

```bash
python -m pip install --upgrade pip
python -m pip install pandas numpy matplotlib seaborn requests python-dotenv ipykernel jupyter
```

Register the environment as a Jupyter kernel:

```bash
python -m ipykernel install --user --name bigdata-eng --display-name "Big Data Engineering"
```

### 3. Start Jupyter

```bash
jupyter lab
```

Open the project folder and select the `Big Data Engineering` kernel for the notebooks.

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

### Weather API Key

`weatherapi.ipynb` reads the weather API key from an environment variable loaded via `python-dotenv`:

```python
weather_key = os.getenv("weather_key")
```

To fetch new weather data, create a local `.env` file in the project root:

```text
weather_key=YOUR_OWN_API_KEY_HERE
```

Do not share or commit the `.env` file.

If `CSV/weather.csv` is already present, the visualization parts of the notebook can be run without fetching new data.

### Kafka and Streaming Configuration

For private Kafka or cluster settings, copy the template:

```bash
cp kafka_and_spark_streaming.local.example.json kafka_and_spark_streaming.local.json
```

Then fill in the local file on your own machine. Keep secrets out of Git.

For the provided Docker setup, the streaming notebook defaults to local mode and can run without a private config file.

## Data Files

The notebooks expect the CSV files to be available in the `CSV/` folder. Important files include:

- `CSV/vienna_city_marathon_all_years_participants.csv`
- `CSV/weather.csv`
- `CSV/vie_weather.csv`
- `CSV/vienna_marathon_component_trends.csv`

If a notebook cannot find a CSV file, check that it is being run from the project root or through the Docker container at `/home/jovyan/work`.

## Troubleshooting

### Jupyter Cannot Find Files

Make sure the current working directory is the project root. In a notebook cell, check:

```python
from pathlib import Path
Path.cwd()
```

The folder shown should contain the `CSV/` directory.

### Port Already in Use

If `localhost:8888`, `localhost:4040`, or `localhost:9092` is already in use, stop the process using that port or change the port mapping in `docker-compose.yml`.

### Missing Python Package

Install the missing package in the active environment, for example:

```bash
python -m pip install package-name
```

Inside a notebook, use:

```python
%pip install package-name
```

### Docker Containers Do Not Start Cleanly

Stop all project containers and start again:

```bash
docker compose down
docker compose up spark
```

For Kafka state issues, remove the Kafka volume:

```bash
docker compose down -v
```
