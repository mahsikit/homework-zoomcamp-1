# Data Engineering Zoomcamp — Week 1

Week-1 homework submission for the [DataTalks.Club Data Engineering Zoomcamp (2024)](https://github.com/DataTalksClub/data-engineering-zoomcamp). Covers containerised data ingestion into PostgreSQL using Docker and Python.

## What it does

Builds a local data pipeline that:
1. Spins up a PostgreSQL instance and pgAdmin via Docker Compose.
2. Ingests NYC Green Taxi trip data (September 2019) into PostgreSQL using a Python script.
3. Queries the loaded data to answer the homework questions.

## Tech Stack

| Tool | Role |
|---|---|
| Docker + Docker Compose | Container orchestration |
| PostgreSQL | Data storage |
| Python (SQLAlchemy, pandas) | Data ingestion script |
| pgAdmin | Database GUI |

## Files

| File | Description |
|---|---|
| `ingest.py` | CLI ingestion script — accepts connection params as arguments |
| `command to start docker.txt` | Docker commands used to run the environment |

## How to run

```bash
# Start Postgres + pgAdmin
docker-compose up -d   # or see the commands in 'command to start docker.txt'

# Ingest the data
python ingest.py \
  --user postgres \
  --password YOUR_PASSWORD \
  --host localhost \
  --port 5432 \
  --db ny_taxi \
  --table green_taxi \
  --url <NYC_green_taxi_csv_url>
```

## Sample queries

```sql
-- Trips where pickup and drop-off were both on 2019-09-18
SELECT COUNT(*)
FROM green_taxi
WHERE CAST(lpep_pickup_datetime AS date) = '2019-09-18'
  AND CAST(lpep_dropoff_datetime AS date) = '2019-09-18';

-- Drop-off zone with the largest tip from pickups in Astoria
SELECT z."Zone" AS drop_off_zone
FROM green_taxi gt
JOIN zone z ON gt."DOLocationID" = z."LocationID"
WHERE gt."PULocationID" = (SELECT "LocationID" FROM zone WHERE "Zone" = 'Astoria')
  AND EXTRACT(YEAR FROM gt.lpep_pickup_datetime) = 2019
  AND EXTRACT(MONTH FROM gt.lpep_pickup_datetime) = 9
ORDER BY gt.tip_amount DESC
LIMIT 1;
```

## Skills demonstrated

- Docker and Docker Compose setup for a local data stack
- Python data ingestion with SQLAlchemy and pandas
- PostgreSQL querying: date functions, joins, aggregations, subqueries
