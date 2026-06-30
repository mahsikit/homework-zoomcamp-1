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

## Homework answers

**Q1 — `--rm` flag**

![Q1](https://github.com/mysecret39/homework-zoomcamp-1/assets/88777199/725e8b0e-c327-46ca-a429-5cdd3ba6a35f)

**Q2 — pgAdmin version:** `0.42.0`

**Q3 — Trips with same pickup and drop-off date (2019-09-18):** `15,612`

```sql
SELECT COUNT(*)
FROM green_taxi
WHERE CAST(lpep_pickup_datetime AS date) = '2019-09-18'
  AND CAST(lpep_dropoff_datetime AS date) = '2019-09-18';
```

![Q3](https://github.com/mysecret39/homework-zoomcamp-1/assets/88777199/564e3aec-f953-452a-9d8f-b017fff00b79)

**Q4 — Day with the longest trip distance:** `2019-09-26`

```sql
SELECT MAX(trip_distance), CAST(lpep_pickup_datetime AS DATE)
FROM green_taxi
GROUP BY lpep_pickup_datetime
ORDER BY MAX(trip_distance) DESC
LIMIT 10;
```

![Q4](https://github.com/mysecret39/homework-zoomcamp-1/assets/88777199/128acd63-9607-41d6-b231-06b497ac9150)

**Q5 — Boroughs with total amount > $50,000 on 2019-09-18:** `Brooklyn`, `Manhattan`, `Queens`

![Q5](https://github.com/mysecret39/homework-zoomcamp-1/assets/88777199/f873dee1-cfd2-4f2c-b967-455541e33fa4)

**Q6 — Drop-off zone with largest tip from Astoria pickups:** `JFK Airport`

```sql
SELECT z."Zone" AS drop_off_zone
FROM green_taxi gt
JOIN zone z ON gt."DOLocationID" = z."LocationID"
WHERE gt."PULocationID" = (SELECT "LocationID" FROM zone WHERE "Zone" = 'Astoria')
  AND EXTRACT(YEAR FROM gt.lpep_pickup_datetime) = 2019
  AND EXTRACT(MONTH FROM gt.lpep_pickup_datetime) = 9
ORDER BY gt.tip_amount DESC
LIMIT 1;
```

![Q6](https://github.com/mysecret39/homework-zoomcamp-1/assets/88777199/2220b61a-c210-4a35-9108-0f6e098ca649)

## Skills demonstrated

- Docker and Docker Compose setup for a local data stack
- Python data ingestion with SQLAlchemy and pandas
- PostgreSQL querying: date functions, joins, aggregations, subqueries
