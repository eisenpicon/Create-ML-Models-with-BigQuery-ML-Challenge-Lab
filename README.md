# Create-ML-Models-with-BigQuery-ML-Challenge-Lab

## BigQuery  console 

## Task 1: Create a dataset to store your machine learning models

```
bq mk austin
```

## Task 2: Create a forecasting BigQuery machine learning model.

```sql
CREATE OR REPLACE MODEL
austin.austin_1
OPTIONS(input_label_cols=['duration_minutes'], model_type='linear_reg')
AS SELECT
duration_minutes,
location,
start_station_name,
CAST(EXTRACT(dayofweek FROM start_time) AS STRING) as dayofweek,
CAST(EXTRACT(hour FROM start_time) AS STRING) AS hourofday,
FROM
`bigquery-public-data.austin_bikeshare.bikeshare_trips` AS trips
JOIN
`bigquery-public-data.austin_bikeshare.bikeshare_stations` AS stations
ON
trips.start_station_id = stations.station_id
WHERE
EXTRACT(year from start_time) = 2018;
```
## Task 3: Create the second machine learning model.

```sql
CREATE OR REPLACE MODEL
austin.austin_2
OPTIONS(input_label_cols=['duration_minutes'], model_type='linear_reg')
AS SELECT
duration_minutes,
subscriber_type,
start_station_name,
CAST(EXTRACT(hour FROM start_time) AS STRING) AS hourofday
FROM `bigquery-public-data.austin_bikeshare.bikeshare_trips` AS trips
WHERE
EXTRACT(year from start_time) = 2018;
```
## Task 4: Evaluate the two machine learning models.


### Model 1

```sql
SELECT
SQRT(mean_squared_error) as rmse, mean_absolute_error
FROM
ML.EVALUATE(MODEL austin.austin_1) ;
SELECT
SQRT(mean_squared_error) as rmse, mean_absolute_error
FROM
ML.EVALUATE(MODEL austin.austin_2);
SELECT
SQRT(mean_squared_error)AS rmse, mean_absolute_error
FROM
ML.EVALUATE (MODEL austin.austin_1, (
SELECT
duration_minutes, location, start_station_name,
CAST(EXTRACT(dayofweek FROM start_time) AS STRING) as dayofweek,
CAST(EXTRACT(hour FROM start_time) AS STRING) AS hourofday,
FROM
`bigquery-public-data.austin_bikeshare.bikeshare_trips` AS trips
JOIN
`bigquery-public-data.austin_bikeshare.bikeshare_stations` AS stations
ON
trips.start_station_id = stations.station_id
WHERE
EXTRACT(year from start_time) = 2019
));
```
### Model 2

```
SELECTsql
SQRT(mean_squared_error)AS rmse, mean_absolute_error
FROM
ML.EVALUATE(MODEL austin.austin_2, (
SELECT
duration_minutes, subscriber_type, start_station_name,
CAST(EXTRACT(hour FROM start_time) AS STRING) AS hourofday
FROM
`bigquery-public-data.austin_bikeshare.bikeshare_trips` AS trips
WHERE
EXTRACT(year from start_time) = 2019));
```


## Task 5: Use the subscriber type machine learning model to predict average trip durations

```sql
SELECT start_station_name, avg(duration_minutes) as avg_duration, count(*) as total_trips
FROM
`bigquery-public-data.austin_bikeshare.bikeshare_trips`
WHERE
EXTRACT(year FROM start_time) = 2019
GROUP BY start_station_name
ORDER BY total_trips DESC;
```


### Now again in BigQuery Editor write this command :

```sql
SELECT
avg(predicted_duration_minutes),count(duration_minutes)
FROM
Ml.PREDICT(MODEL austin.austin_2, (
SELECT
duration_minutes,
subscriber_type,
start_station_name,
CAST(EXTRACT(hour FROM start_time) AS STRING) AS hourofday
FROM
`bigquery-public-data.austin_bikeshare.bikeshare_trips` AS trips
WHERE
EXTRACT(year from start_time) = 2019
and start_station_name = '21st & Speedway @PCL'
and subscriber_type='Single Trip'));
```


## cloud shell

## Task 1: Create a dataset to store your machine learning models
```
bq mk austin
```

## Task 2: Create a forecasting BigQuery machine learning model.

```
export PROJECT_ID=DEVSHELL_PROJECT_ID

bq query --use_legacy_sql=false "CREATE OR REPLACE MODEL austin.austin_1 OPTIONS(input_label_cols=['duration_minutes'], model_type='linear_reg') AS SELECT

duration_minutes, location, start_station_name, CAST(EXTRACT(dayofweek FROM start_time) AS STRING) as dayofweek, CAST(EXTRACT(hour FROM start_time) AS STRING) AS hourofday, FROM \`bigquery-public-data.austin_bikeshare.bikeshare_trips\` AS trips JOIN \`bigquery-public-data.austin_bikeshare.bikeshare_stations\` AS stations ON trips.start_station_id = stations.station_id WHERE EXTRACT(year from start_time) = 2018"

```

## Task 3: Create the second machine learning model.

```
export PROJECT_ID=DEVSHELL_PROJECT_ID

bq query --use_legacy_sql=false "CREATE OR REPLACE MODEL austin.austin_2 OPTIONS(input_label_cols=['duration_minutes'], model_type='linear_reg') AS SELECT duration_minutes, subscriber_type, start_station_name, CAST(EXTRACT(hour FROM start_time) AS STRING) AS hourofday FROM \`bigquery-public-data.austin_bikeshare.bikeshare_trips\` AS trips WHERE EXTRACT(year from start_time) = 2018"
```


## Task 4: Evaluate the two machine learning models.

```
bq query --use_legacy_sql=false "SELECT SQRT(mean_squared_error)AS rmse, mean_absolute_error FROM ML.EVALUATE(MODEL austin.austin_1, (SELECT duration_minutes, location, start_station_name, CAST(EXTRACT(dayofweek FROM start_time) AS STRING) as dayofweek, CAST(EXTRACT(hour FROM start_time) AS STRING) AS hourofday, FROM \`bigquery-public-data.austin_bikeshare.bikeshare_trips\` AS trips JOIN \`bigquery-public-data.austin_bikeshare.bikeshare_stations\` AS stations ON trips.start_station_id = stations.station_id WHERE EXTRACT(year from start_time) = 2019))"

bq query --use_legacy_sql=false "SELECT SQRT(mean_squared_error)AS rmse, mean_absolute_error FROM ML.EVALUATE(MODEL austin.austin_2, (SELECT duration_minutes, subscriber_type, start_station_name, CAST(EXTRACT(hour FROM start_time) AS STRING) AS hourofday FROM \`bigquery-public-data.austin_bikeshare.bikeshare_trips\` AS trips WHERE EXTRACT(year from start_time) = 2019))"
```




## Task 5: Use the subscriber type machine learning model to predict average trip durations

```
bq query --use_legacy_sql=false "SELECT start_station_name, avg(duration_minutes) as avg_duration, count(*) as total_trips FROM \`bigquery-public-data.austin_bikeshare.bikeshare_trips\` WHERE EXTRACT(year FROM start_time) = 2019 GROUP BY start_station_name ORDER BY total_trips DESC"
```
```
bq query --use_legacy_sql=false "SELECT avg(predicted_duration_minutes),count(duration_minutes) FROM Ml.PREDICT(MODEL austin.austin_2, (SELECT duration_minutes, subscriber_type, start_station_name, CAST(EXTRACT(hour FROM start_time) AS STRING) AS hourofday FROM \`bigquery-public-data.austin_bikeshare.bikeshare_trips\` AS trips WHERE EXTRACT(year from start_time)=2019 and start_station_name = '21st & Speedway @PCL' and subscriber_type='Single Trip' ))"
```
