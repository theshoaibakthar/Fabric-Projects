# Worldwide Earthquake Events Data Processing Project

This project demonstrates a data processing pipeline for worldwide earthquake events using the Medallion Architecture (Bronze, Silver, Gold layers) in Microsoft Fabric. The pipeline ingests raw earthquake data from the USGS Earthquake API, processes and cleans it, and prepares it for analysis and reporting.

## Project Structure

### Notebooks
1. **01 Worldwide Earthquake Events API - Bronze Layer Processing.ipynb**
   - **Purpose**: Ingests raw earthquake data from the USGS API
   - **Technology**: Python with requests library
   - **Output**: Raw JSON data stored in the Bronze layer

2. **02 Worldwide Earthquake Events API - Silver Layer Processing.ipynb**
   - **Purpose**: Cleans and transforms the raw data
   - **Technology**: PySpark
   - **Output**: Structured, cleaned data in the Silver layer

3. **03 Worldwide Earthquake Events API - Gold Layer Processing.ipynb**
   - **Purpose**: Aggregates and prepares data for analytics
   - **Technology**: PySpark
   - **Output**: Business-ready data in the Gold layer

## Data Source
- **API**: USGS Earthquake Hazards Program API
- **URL**: https://earthquake.usgs.gov/fdsnws/event/1/query
- **Format**: GeoJSON
- **Parameters**: Date range (starttime, endtime)

### Data Attribute Definitions

`id`: A string identifier for each data record.

`latitude`: The latitude of the event, stored as a double.

`longitude`: The longitude of the event, also stored as a double.

`elevation`: The elevation at which the event occurred, expressed in meters, stored as a double.

`title`: A string representing the title or name of the event.

`place_description`: A string describing the location of the event.

`sig`: A bigint (large integer) representing the significance score of the event.

`mag`: A double indicating the magnitude of the earthquake.

`magType`: A string describing the type of magnitude scale used.

`time`: A timestamp marking the exact time of the event.

`updated`: A timestamp indicating the last update time for the event data.

## Prerequisites
- Microsoft Fabric workspace with access to Notebooks and Lakehouse
- Python environment with required libraries:
  - requests
  - json
  - reverse_geocoder
- PySpark runtime in Fabric

## How to Run
1. Ensure you have the necessary parameters (start_date, end_date) set
2. Execute the notebooks in numerical order:
   - Start with Notebook 01 to ingest raw data
   - Run Notebook 02 to process and clean the data
   - Finish with Notebook 03 for final aggregation
3. Monitor the pipeline execution in Fabric

