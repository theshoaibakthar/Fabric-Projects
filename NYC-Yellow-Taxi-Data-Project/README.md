# NYC Yellow Taxi – End-to-End Fabric Project

This project demonstrates a complete end-to-end data engineering and analytics solution implemented using Microsoft Fabric for NYC Yellow Taxi trip data (January– October 2025).
The solution includes Lakehouse ingestion, Warehouse staging and presentation modeling, metadata-driven pipelines, SQL-based transformations, orchestration automation, and Power BI reporting.

## Project Overview

This implementation reflects production-style data engineering practices. The objectives were to:

* Ingest monthly Parquet files into a Fabric Lakehouse
* Load raw data into a Warehouse staging layer
* Cleanse and validate incoming records
* Enrich trip data with taxi zone lookup data
* Transform and model data in a presentation layer
* Maintain full historical data (append model)
* Log per-run metadata for automation and auditability
* Automate the month-over-month processing workflow
* Expose data via a Fabric semantic model for Power BI reporting

The project was completed using Microsoft Fabric’s Data Factory, Lakehouse, Warehouse, Dataflows Gen2, SQL stored procedures, and Power BI.

## Architecture Overview

```
NYC TLC Source Data (Parquet, CSV)
        |
        v
Microsoft Fabric Lakehouse (Raw Storage)
        |
        v
Staging Pipeline (Dynamic Month Ingestion)
    - File ingestion
    - Stored procedure–based data cleansing
    - Metadata logging
        |
        v
Presentation Processing
    Option 1: Dataflow Gen2 (initial implementation)
    Option 2: SQL Stored Procedure (final optimized solution)
        |
        v
Presentation Table (Warehouse)
        |
        v
Fabric Semantic Model
        |
        v
Power BI Report
```

## Repository Structure

```
NYC-Yellow-Taxi-Data-Project/
│
├── README.md                          
├── docs/
│
├── pipelines/
│   ├── pl_stg_processing_nyctaxi.md
│   ├── pl_pres_processing_nyctaxi.md
```

## Landing Layer

### Data Sources

#### 1. NYC TLC Yellow Taxi Trip Data (Parquet)

Ten monthly Parquet files (January–October 2025), each containing 3–4 million rows.

#### 2. Taxi Zone Lookup (CSV)

Maps `LocationID` to borough, zone, and service zone.

### Data Warehouse Setup

Schemas used:

* `stg` — staging layer
* `dbo` — presentation layer
* `metadata` — pipeline metadata

Core tables:

* `stg.nyctaxi_yellow`
* `stg.taxi_zone_lookup`
* `dbo.nyctaxi_yellow`
* `metadata.processing_log`

## Staging Layer

### Pipeline 1: (`pl_stg_lookup`)

One-time pipeline to load taxi zone lookup data from Lakehouse into `stg.taxi_zone_lookup`.

### Pipeline 2: (`pl_stg_processing_nyctaxi`)

A dynamic pipeline that processes one month per run. 

Key components include:

### Dynamic File Selection

A variable (`v_date`) determines which Parquet file to load.
A script queries `processing_log` to compute the next month automatically.

### Data Cleaning Stored Procedure

Removes out-of-range pickup timestamps and ensures staging contains only data for the month being processed.

### Metadata Logging

Each run inserts a log entry with:

* pipeline run ID
* rows processed
* latest pickup datetime
* timestamp
* table processed


For more details on this pipeline, go to [pl_stg_processing_nyctaxi](pipelines/pl_stg_processing_nyctaxi.md)

## Presentation Layer

Two implementations were created.

### Option 1 — Dataflow Gen2

Used initially to handle transformations and joins visually.

### Option 2 — Stored Procedure (Final Production Approach)

A SQL-based implementation replaced the Dataflow for efficiency.

Benefits:

* Significantly reduced runtime
* Easier to maintain and audit
* Better suited for large data volumes

### Pipeline 3: (`pl_pres_processing_nyctaxi`)

Combine the data from staging tables to create cleaned and transformed data for presentation layer.

For more details on this pipeline, go to [pl_pres_processing_nyctaxi](pipelines/pl_pres_processing_nyctaxi.md)

---

## Pipeline 4: (`pl_orchestrate_nyctaxi`)

Executes:

1. Staging Pipeline
2. Presentation Pipeline

Ensures the end-to-end workflow runs in sequence and can be scheduled autonomously.

## Semantic Model and Power BI

The presentation table (`dbo.nyc_taxi_yellow`) was added to the Fabric semantic model.

A Power BI report was created to analyze:

* total revenue
* trip volume
* passenger counts
* payment method trends
* pickup → drop-off patterns
* daily revenue trends

## Results

After processing January–October 2025:

* Total rows in presentation table: ~16.7 million
* Stored procedure reduced presentation processing time significantly
* Pipelines ran incrementally and repeatably without re-processing prior months

## Skills Demonstrated

### Microsoft Fabric

* Lakehouse storage
* Warehouse modeling
* Data Factory pipelines
* Dataflows Gen2
* SQL stored procedures
* Metadata-driven processing
* Pipeline orchestration

### Data Engineering

* Incremental data ingestion
* Data cleansing and validation
* Solution Architecture (landing → staging → presentation → reporting)
* Automation and performance tuning

### Business Intelligence

* Semantic model development
* Power BI dashboard creation
