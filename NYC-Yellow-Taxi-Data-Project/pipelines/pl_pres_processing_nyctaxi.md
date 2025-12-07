# Pipeline for Presentation Layer

## Overall Pipeline

![!\[alt text\](image.png)](../docs/screenshots/pl_pres_processing_nyctaxi.png)

## Create the dbo.nyctaxi_yellow table

```sql
CREATE TABLE dbo.nyctaxi_yellow
(
	vendor varchar(50),
	tpep_pickup_datetime date,
	tpep_dropoff_datetime date,
	pu_borough varchar(100),
	pu_zone varchar(100),
	do_borough varchar(100),
	do_zone varchar(100),
	payment_method varchar(50),
	passenger_count int,
	trip_distance FLOAT,
	total_amount FLOAT
);
```

## SP Processing Presentation

For the Stored Procedure Activity “SP Process Presentation ”.

Create the Stored Procedure dbo.process_presentation in the Data Warehouse using the code below.

```sql
CREATE PROCEDURE dbo.process_presentation
AS
INSERT INTO dbo.nyctaxi_yellow
    SELECT
    CASE
        WHEN nty.VendorID = 1 THEN 'Creative Mobile Technologies'
        WHEN nty.VendorID = 2 THEN 'Curb Mobility'
        WHEN nty.VendorID = 6 THEN 'Myle Technologies'
        WHEN nty.VendorID = 7 THEN 'Helix'
        else 'Unknown'
    end as vendor,
    format(nty.tpep_pickup_datetime,'yyyy-MM-dd') as tpep_pickup_datetime,
    format(nty.tpep_dropoff_datetime,'yyyy-MM-dd') as tpep_dropoff_datetime,
    lu1.Borough as pu_borough,
    lu1.Zone as pu_zone,
    lu2.Borough as pu_borough,
    lu2.Zone as pu_zone,
    CASE
        WHEN nty.payment_type = 0 THEN 'Flex Fare trip'
        WHEN nty.payment_type = 1 THEN 'Credit card'
        WHEN nty.payment_type = 2 THEN 'Cash'
        WHEN nty.payment_type = 3 THEN 'No charge'
        WHEN nty.payment_type = 4 THEN 'Dispute'
        WHEN nty.payment_type = 5 THEN 'Unknown'
        WHEN nty.payment_type = 6 THEN 'Voided trip'
        else ''
    end as payment_method,
    nty.passenger_count as passenger_count,
    nty.trip_distance as trip_distance,
    nty.total_amount as total_amount
    from stg.nyctaxi_yellow nty
    left join stg.taxi_zone_lookup lu1
    on nty.PULocationID = lu1.LocationID
    left join stg.taxi_zone_lookup lu2
    on nty.DOLocationID = lu2.LocationID;
```

## SP Loading Presentation Metadata

For the Stored Procedure Activity “SP Loading Staging Metadata”.

Create the Stored Procedure metadata.insert_staging_metadata in the Data Warehouse using the code below.

```sql
CREATE PROCEDURE metadata.insert_presentation_metadata
    @pipeline_run_id VARCHAR(255),
    @table_name VARCHAR(255),
    @processed_date DATETIME2
AS
    INSERT INTO metadata.processing_log (pipeline_run_id, table_processed, rows_processed, latest_processed_pickup, processed_datetime)
    SELECT
        @pipeline_run_id AS pipeline_id,
        @table_name AS table_processed,
        COUNT(*) AS rows_processed,
        MAX(tpep_pickup_datetime) AS latest_processed_pickup,
        @processed_date AS processed_datetime
    FROM dbo.nyctaxi_yellow;
```

