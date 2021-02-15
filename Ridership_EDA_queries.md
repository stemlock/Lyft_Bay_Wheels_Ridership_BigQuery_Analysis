# Lyft Bay Wheels Data Query Project

#### Problem Statement

- As a data scientist at Lyft Bay Wheels (https://www.lyft.com/bikes/bay-wheels), (formerly known as Ford GoBike, the
  company running Bay Area Bikeshare), the goal is to try to increase ridership by offering deals through the mobile app. 
  
- What deals should be offered? Currently, the company has several options which can change over time. Frequent offers include: 
  * Single Ride 
  * Monthly Membership
  * Annual Membership
  * Bike Share for All
  * Access Pass
  * Corporate Membership
  * etc.

- Throughout this project, queries will be made that help lead to the following questions being answered: 

  * What are the 5 most popular trips that could be classified as "commuter trips"? 
  
  * What are the recommendations for offers based on the findings?

---

## Dataset

For this project, the data from the Google BigQuery public dataset `san_francisco` is used.

- The Bay Bike Share has two datasets: a static one and a dynamic one.  The static one covers an historic period of about 3 years.  The dynamic one updates every 10 minutes or so. For this project, the statis dataset was used to ensure that the analysis remains consist against time.

- The static tables used are in the dataset **san_francisco**:

  * bikeshare_stations

  * bikeshare_status

  * bikeshare_trips

- The dynamic tables are found in the dataset **san\_francisco\_bikeshare**


## Parts 1, 2, 3

This project is broken into 3 parts:

- Part 1 & 2: Data cleaning and exploratory data analysis (EDA)
    - In Part 1, EDA queries are made using the Google BigQuery GUI interface in the cloud.
    - In Part 2, EDA queries are made using the Linux command line from a Google Cloud TensorFlow virtual machine instance.
- Part 3: Final analysis and results
    - In Part 3, queries are made from a Jupyter Notebook in the Tensor Flow virtual machine instance. The results are saved into Pandas, and a report is presented enhanced by Pandas output tables and simple data visualizations using Matplotlib.

---

## Part 1 - EDA Queries with BigQuery


### Some initial queries

- What's the size of this dataset? (i.e., how many trips)

```sql 
SELECT COUNT(*) AS num_trips
FROM `bigquery-public-data.san_francisco.bikeshare_trips`;
```

| num_trips |
|--------|
| 983648 |

There are 983,648 trips stored in the dataset. 

- What is the earliest start date and time and latest end date and time for a trip?

```sql 
SELECT MIN(start_date) AS Earliest, MAX(end_date) AS Latest
FROM `bigquery-public-data.san_francisco.bikeshare_trips`;
``` 

| Earliest                | Latest                 |
|-------------------------|------------------------|
| 2013-08-29 09:08:00 UTC | 2016-08-31 23:48:00 UT |

The earliest start date and time for a trip is 2013-08-29, at 09:08:00 PST.
The latest end date and time for a trip is 2016-08-31, at 23:48:00 PST.

- How many bikes are there?

```sql 
SELECT COUNT(DISTINCT(bike_number)) AS num_bikes
FROM `bigquery-public-data.san_francisco.bikeshare_trips`;
```

| num_bikes |
|-----|
| 700 |

There are 700 unique bikes.

### Follow-up questions

- Question 1: What is the total number of bike docks across all stations?
  * Answer: There are 1,344 bike docks.
  * SQL query:
  
```sql 
SELECT SUM(dockcount) AS num_docks
FROM `bigquery-public-data.san_francisco.bikeshare_stations`;
```

| num_docks  |
|------|
| 1344 |

- Question 2: What is the average duration of a bike trip?
  * Answer: The average duration of a bike trip is 1,019 seconds or 0 hours, 16 minutes, and 59 seconds.
  * SQL query:
  
```sql
SELECT 
    CAST(AVG(duration_sec) AS INT64) AS average_seconds, 
    CONCAT((CAST(FLOOR(ROUND(AVG(duration_sec)) / 3600.0) AS INT64)), ':', 
    (CAST(FLOOR(ROUND(AVG(duration_sec)) / 60.0) AS INT64)), ':', 
    (CAST(MOD(MOD(CAST(AVG(duration_sec) AS INT64), 3600),60) AS INT64)))  AS duration_hhmmss,
FROM `bigquery-public-data.san_francisco.bikeshare_trips`;
```

| average_seconds | duration_hhmmss |
|-----------------|-----------------|
|            1019 | 0:16:59         |

- Question 3: What are the top 10 hours of the week with the greatest number of trips started during that hour? (i.e., which 10 (hour, day of the week) combinations have the most bike trip starts)?
  * Answer:
  
    | start_dow_str | start_hour | num_trips |
    |---------------|------------|-----------|
    | Tuesday       |          8 |     28138 |
    | Wednesday     |          8 |     27043 |
    | Thursday      |          8 |     26014 |
    | Tuesday       |         17 |     25458 |
    | Monday        |          8 |     25443 |
    | Monday        |         17 |     24706 |
    | Wednesday     |         17 |     24650 |
    | Thursday      |         17 |     23312 |
    | Friday        |          8 |     22361 |
    | Friday        |         17 |     20206 |
    
  * SQL query:
  
```sql
SELECT start_dow_str, start_hour, COUNT(trip_id) AS num_trips
FROM `w205-project-300900.bike_trip_data.trip_dow_hour`
GROUP BY start_dow_str, start_hour
ORDER BY COUNT(trip_id) DESC
LIMIT 10;
```
*See Project_1 Jupyter Notebook Appendix for code to create the trip\_dow\_hour view

- Question 4: What are the top 5 popular station pairs in each region?
  * Answer:

| start_station_name                   | end_station_name                         | region_id | num_trips       | top_station_pairs_rank_by_region |
|--------------------------------------|------------------------------------------|-----------|-----------------|-------------------------------------|
| Harry Bridges Plaza (Ferry Building) | Embarcadero at Sansome                   |         3 |            9150 |                                   1 |
| 2nd at Townsend                      | Harry Bridges Plaza (Ferry Building)     |         3 |            7620 |                                   2 |
| Harry Bridges Plaza (Ferry Building) | 2nd at Townsend                          |         3 |            6888 |                                   3 |
| Embarcadero at Sansome               | Steuart at Market                        |         3 |            6874 |                                   4 |
| Embarcadero at Folsom                | San Francisco Caltrain (Townsend at 4th) |         3 |            6351 |                                   5 |
| University and Emerson               | University and Emerson                   |         5 |            1184 |                                   1 |
| University and Emerson               | California Ave Caltrain Station          |         5 |             258 |                                   2 |
| University and Emerson               | Cowper at University                     |         5 |             205 |                                   3 |
| University and Emerson               | Park at Olive                            |         5 |             123 |                                   4 |
| University and Emerson               | Palo Alto Caltrain Station               |         5 |              88 |                                   5 |
| Paseo de San Antonio                 | San Jose Diridon Caltrain Station        |        12 |            1017 |                                   1 |
| Washington at Kearny                 | Powell Street BART                       |        12 |             571 |                                   2 |
| Washington at Kearny                 | Embarcadero at Sansome                   |        12 |             558 |                                   3 |
| Washington at Kearny                 | Market at Sansome                        |        12 |             361 |                                   4 |
| Washington at Kearny                 | Harry Bridges Plaza (Ferry Building)     |        12 |             341 |                                   5 |

  * SQL query:
  
```sql 
SELECT start_station_name, end_station_name, region_id, num_trips, top_station_pairs_rank_by_region FROM (
    SELECT start_station_name, start_station_id, end_station_name, end_station_id, region_id, COUNT(trip_id) AS num_trips,
    row_number() OVER (PARTITION BY region_id ORDER BY COUNT(trip_id) DESC) AS top_station_pairs_rank_by_region
    FROM `w205-project-300900.bike_trip_data.trips_with_regions`
    GROUP BY start_station_name, start_station_id, end_station_name, end_station_id, region_id
    ORDER BY num_trips DESC)
WHERE top_station_pairs_rank_by_region <= 5
ORDER BY region_id;
```

*See Project_1 Jupyter Notebook Appendix for code to create the trips\_with\_regions view

- Question 5: What are the top 3 most popular regions?
  * Answer: 

| region_name   | region_id | num_trips       |
|---------------|-----------|-----------------|
| San Francisco |         3 |          748309 |
| Oakland       |        12 |           11674 |
| San Jose      |         5 |            2002 |

  * SQL query:

```sql
SELECT region_name, region_id, COUNT(trip_id) AS num_trips
FROM `w205-project-300900.bike_trip_data.trips_with_regions`
GROUP BY region_name, region_id
ORDER BY num_trips DESC;
```

- Question 6: What is the total number of trips for each short station name in each region?
  * Answer:

| station_short_name | region_name   | num_trips       |
|--------------------|---------------|-----------------|
| OK-L5              | Oakland       |            3066 |
| OK-L12             | Oakland       |            8608 |
| SF-G29-2           | San Francisco |             463 |
| SF-F28-2           | San Francisco |             241 |
| SF-F30-1           | San Francisco |             373 |
| SF-F30-2           | San Francisco |             272 |
| SF-F29             | San Francisco |            2249 |
| SF-C28-2           | San Francisco |            2855 |
| SF-D28             | San Francisco |            2664 |
| SF-L19             | San Francisco |            2626 |
| SF-E27             | San Francisco |            2374 |
| SF-H26             | San Francisco |            2046 |
| SF-A27             | San Francisco |            4132 |
| SF-D27             | San Francisco |            2911 |
| SF-C28-1           | San Francisco |            2232 |
| SF-G27             | San Francisco |            2137 |
| SF-D29             | San Francisco |            1533 |
| SF-G26             | San Francisco |            5308 |
| SF-E28             | San Francisco |            2043 |
| SF-E29-2           | San Francisco |            1954 |
| SF-K28             | San Francisco |            1283 |
| SF-L25             | San Francisco |              20 |
| SF-G30-2           | San Francisco |            9059 |
| SF-K21             | San Francisco |           23082 |
| SF-G28-1           | San Francisco |             931 |
| SF-L26             | San Francisco |              84 |
| SF-J26             | San Francisco |           23404 |
| SF-J27             | San Francisco |           20746 |
| SF-K22-2           | San Francisco |           27502 |
| SF-I28             | San Francisco |           13811 |
| SF-J25             | San Francisco |           39936 |
| SF-I23-1           | San Francisco |           14351 |
| SF-I29-2           | San Francisco |           17062 |
| SF-I23-2           | San Francisco |           15197 |
| SF-J28             | San Francisco |           26218 |
| SF-K24             | San Francisco |           35142 |
| SF-J21             | San Francisco |           38531 |
| SF-I19             | San Francisco |           25204 |
| SF-L27             | San Francisco |             173 |
| SF-J23-2           | San Francisco |           10651 |
| SF-J19             | San Francisco |           22525 |
| SF-J15             | San Francisco |           21874 |
| SF-J24             | San Francisco |           41137 |
| SF-J23-1           | San Francisco |            6730 |
| SF-J29-1           | San Francisco |           18278 |
| SF-K18             | San Francisco |           16984 |
| SF-K20             | San Francisco |           23041 |
| SF-K19             | San Francisco |           19145 |
| SF-K22-1           | San Francisco |           17837 |
| SF-I30             | San Francisco |           49062 |
| SF-K17             | San Francisco |           72683 |
| SF-J20             | San Francisco |           39200 |
| SF-G30-1           | San Francisco |            4275 |
| SF-L28             | San Francisco |              69 |
| SF-H18             | San Francisco |            2601 |
| SF-H30             | San Francisco |            1026 |
| SF-H29             | San Francisco |            2162 |
| SF-H25             | San Francisco |            3281 |
| SF-G19             | San Francisco |            2377 |
| SF-J29             | San Francisco |            1804 |
| SF-H28             | San Francisco |            1418 |
| SJ-M6              | San Jose      |            2002 |

  * SQL query:

```sql
SELECT station_short_name, region_name, COUNT(trip_id) AS num_trips
FROM `w205-project-300900.bike_trip_data.trips_with_regions`
GROUP BY station_short_name, region_name
ORDER BY region_name;
```

- Question 7: What are the top 10 used bikes in each of the top 3 region (these bikes could be in need of more frequent maintenance...)?
  * Answer:


| bike_use_rank_by_region    | bike_number | region_name   | num_trips       |
|----------------------------|-------------|---------------|-----------------|
|                          1 |         275 | Oakland       |              43 |
|                          2 |         328 | Oakland       |              40 |
|                          3 |         353 | Oakland       |              39 |
|                          4 |         480 | Oakland       |              38 |
|                          5 |         549 | Oakland       |              38 |
|                          6 |          99 | Oakland       |              37 |
|                          7 |         487 | Oakland       |              36 |
|                          8 |         463 | Oakland       |              35 |
|                          9 |         489 | Oakland       |              35 |
|                         10 |         284 | Oakland       |              35 |
|                          1 |         389 | San Francisco |            2212 |
|                          2 |         524 | San Francisco |            2201 |
|                          3 |         631 | San Francisco |            2194 |
|                          4 |         392 | San Francisco |            2172 |
|                          5 |         328 | San Francisco |            2170 |
|                          6 |         558 | San Francisco |            2167 |
|                          7 |         287 | San Francisco |            2157 |
|                          8 |         585 | San Francisco |            2151 |
|                          9 |         503 | San Francisco |            2141 |
|                         10 |         592 | San Francisco |            2141 |
|                          1 |         165 | San Jose      |              29 |
|                          2 |         638 | San Jose      |              27 |
|                          3 |         120 | San Jose      |              26 |
|                          4 |          71 | San Jose      |              25 |
|                          5 |          20 | San Jose      |              24 |
|                          6 |         160 | San Jose      |              22 |
|                          7 |         307 | San Jose      |              22 |
|                          8 |           9 | San Jose      |              22 |
|                          9 |          83 | San Jose      |              22 |
|                         10 |         201 | San Jose      |              22 |

  * SQL query:

```sql 
SELECT bike_use_rank_by_region, bike_number, region_name, num_trips,  FROM (
    SELECT bike_number, region_name, COUNT(trip_id) AS num_trips,
    row_number() OVER (PARTITION BY region_id ORDER BY COUNT(trip_id) DESC) AS bike_use_rank_by_region
    FROM `w205-project-300900.bike_trip_data.trips_with_regions`
    GROUP BY bike_number, region_name, region_id)
WHERE bike_use_rank_by_region <= 10
ORDER BY region_name;
```

---

## Part 2 - EDA Queries from the BigQuery CLI 


### Initial query

- How many trips are in the morning vs in the afternoon?
  
  Here, we assume that trips are classified as morning if they begin before 12PM, and are classified as afternoon if they begin on or after 12PM.

  ```
  bq query --use_legacy_sql=false '
      SELECT   
          CASE WHEN start_hour < 12 THEN "Morning"
          ELSE "Afternoon"
          END AS time_period,
          COUNT(*) AS num_trips         
      FROM `bike_trip_data.trip_dow_hour`
      GROUP BY
          CASE WHEN start_hour < 12 THEN "Morning"
          ELSE "Afternoon"
          END;'
  ```
  
    +-------------+-----------+
    | time_period | num_trips |
    +-------------+-----------+
    | Afternoon   |    571309 |
    | Morning     |    412339 |
    +-------------+-----------+

### Follow-up questions

- Question 1: Out of the day-of-week, hour-of-day pairs that have at least 10,000 trips, what are the combinations of days and starting hours?

  * Answer: The pairings are every combination of days from Monday to Friday, and hours from 7:00 to 9:00 and from 16:00-18:00.
  * SQL query:
  
  ```sql
  SELECT start_dow_int, start_dow_str, start_hour, COUNT(trip_id) AS num_trips
  FROM `w205-project-300900.bike_trip_data.trip_dow_hour`
  GROUP BY start_dow_int, start_dow_str, start_hour
  HAVING num_trips > 10000
  ORDER BY start_dow_int, start_hour;
  ```
  
    | start_dow_int   | start_dow_str | start_hour | num_trips |
    |-----------------|-----------|------------|-----------------|
    |               2 | Monday    |          7 |           12992 |
    |               2 | Monday    |          8 |           25443 |
    |               2 | Monday    |          9 |           17541 |
    |               2 | Monday    |         16 |           15569 |
    |               2 | Monday    |         17 |           24706 |
    |               2 | Monday    |         18 |           16016 |
    |               3 | Tuesday   |          7 |           14497 |
    |               3 | Tuesday   |          8 |           28138 |
    |               3 | Tuesday   |          9 |           18873 |
    |               3 | Tuesday   |         16 |           16434 |
    |               3 | Tuesday   |         17 |           25458 |
    |               3 | Tuesday   |         18 |           17464 |
    |               4 | Wednesday |          7 |           13827 |
    |               4 | Wednesday |          8 |           27043 |
    |               4 | Wednesday |          9 |           18925 |
    |               4 | Wednesday |         16 |           15942 |
    |               4 | Wednesday |         17 |           24650 |
    |               4 | Wednesday |         18 |           16728 |
    |               5 | Thursday  |          7 |           13360 |
    |               5 | Thursday  |          8 |           26014 |
    |               5 | Thursday  |          9 |           18247 |
    |               5 | Thursday  |         16 |           15481 |
    |               5 | Thursday  |         17 |           23312 |
    |               5 | Thursday  |         18 |           15740 |
    |               6 | Friday    |          7 |           11224 |
    |               6 | Friday    |          8 |           22361 |
    |               6 | Friday    |          9 |           16678 |
    |               6 | Friday    |         16 |           15574 |
    |               6 | Friday    |         17 |           20206 |
    |               6 | Friday    |         18 |           12240 |

- Question 2: Given the answer to Question 1 above, what is the proportion of these trips in comparison to the total number of trips?
  * Answer: 0.57
  * SQL query: 
  
  ```sql
  WITH partial AS (
        SELECT COUNT(*) AS num_trips
        FROM `w205-project-300900.bike_trip_data.trip_dow_hour` 
        WHERE start_dow_str IN ('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday') 
        AND start_hour IN (7, 8, 9, 16, 17, 18))
  SELECT num_trips / (SELECT COUNT(*) FROM `bigquery-public-data.san_francisco.bikeshare_trips`) AS Proportion FROM partial;
  ```

    | Proportion         |
    |--------------------|
    | 0.5700037005107518 |

- Question 3: Given the answer to Question 1 above, if we classify "commuter trips" as trips starting between 7:00-10:00 or 16:00-19:00 during the week, what are the most frequent type of customers who take these "commuter trips"?
  * Answer: There are 529,871 "Subscribers" (annual or 30-day member) and 30,812 "Customers" (24-hour or 3-day member).
  * SQL query:
  
  ```sql
  SELECT subscriber_type, COUNT(*) AS num_trips
  FROM `w205-project-300900.bike_trip_data.commuter_trips` 
  GROUP BY subscriber_type;
  ```
  
    | subscriber_type | num_trips       |
    |-----------------|-----------------|
    | Customer        |           30812 |
    | Subscriber      |          529871 |
    
*See Project_1 Jupyter Notebook Appendix for code to create the commuter\_trips view

- Question 4: What are the top 10 most popular station pairs for these "commuter trips"?
  * Answer:
  
    | start_station_name                            | end_station_name                         | num_trips       |
    |-----------------------------------------------|------------------------------------------|-----------------|
    | 2nd at Townsend                               | Harry Bridges Plaza (Ferry Building)     |            5165 |
    | Harry Bridges Plaza (Ferry Building)          | 2nd at Townsend                          |            5127 |
    | San Francisco Caltrain 2 (330 Townsend)       | Townsend at 7th                          |            5040 |
    | Embarcadero at Sansome                        | Steuart at Market                        |            4904 |
    | Embarcadero at Folsom                         | San Francisco Caltrain (Townsend at 4th) |            4756 |
    | San Francisco Caltrain (Townsend at 4th)      | Harry Bridges Plaza (Ferry Building)     |            4689 |
    | Steuart at Market                             | 2nd at Townsend                          |            4632 |
    | Temporary Transbay Terminal (Howard at Beale) | San Francisco Caltrain (Townsend at 4th) |            4584 |
    | Steuart at Market                             | San Francisco Caltrain (Townsend at 4th) |            4509 |
    | Townsend at 7th                               | San Francisco Caltrain 2 (330 Townsend)  |            4258 |
  
  * SQL query:
  
  ```sql
  SELECT start_station_name, end_station_name, COUNT(trip_id) AS num_trips,
  FROM `w205-project-300900.bike_trip_data.commuter_trips`
  GROUP BY start_station_name, end_station_name
  ORDER BY num_trips DESC
  LIMIT 10;
  ```
  
- Question 5: What is the average avaibility of bikes at these stations during "commuter trip" hours?
  * Answer: 
  
    | station_id | start_station_name                            | trip_type                     | avg_bikes            | 
    |------------|-----------------------------------------------|-------------------------------|----------------------|
    |         50 | Harry Bridges Plaza (Ferry Building)          | Commuter Trip Hours - Morning |                   11 |
    |         50 | Harry Bridges Plaza (Ferry Building)          | Commuter Trip Hours - Night   |                   14 |
    |         51 | Embarcadero at Folsom                         | Commuter Trip Hours - Morning |                    8 |
    |         51 | Embarcadero at Folsom                         | Commuter Trip Hours - Night   |                    7 |
    |         55 | Temporary Transbay Terminal (Howard at Beale) | Commuter Trip Hours - Morning |                    9 |
    |         55 | Temporary Transbay Terminal (Howard at Beale) | Commuter Trip Hours - Night   |                   10 |
    |         60 | Embarcadero at Sansome                        | Commuter Trip Hours - Morning |                    6 |
    |         60 | Embarcadero at Sansome                        | Commuter Trip Hours - Night   |                    6 |
    |         61 | 2nd at Townsend                               | Commuter Trip Hours - Morning |                   12 |
    |         61 | 2nd at Townsend                               | Commuter Trip Hours - Night   |                   12 |
    |         65 | Townsend at 7th                               | Commuter Trip Hours - Morning |                    7 |
    |         65 | Townsend at 7th                               | Commuter Trip Hours - Night   |                    6 |
    |         69 | San Francisco Caltrain 2 (330 Townsend)       | Commuter Trip Hours - Morning |                   12 |
    |         69 | San Francisco Caltrain 2 (330 Townsend)       | Commuter Trip Hours - Night   |                   10 |
    |         70 | San Francisco Caltrain (Townsend at 4th)      | Commuter Trip Hours - Morning |                    7 |
    |         70 | San Francisco Caltrain (Townsend at 4th)      | Commuter Trip Hours - Night   |                   12 |
    |         74 | Steuart at Market                             | Commuter Trip Hours - Morning |                   11 |
    |         74 | Steuart at Market                             | Commuter Trip Hours - Night   |                    9 |
  
  * SQL query:

  ```sql
    SELECT station_id, start_station_name, 
        CASE
            WHEN (hour BETWEEN 7 AND 9) THEN 'Commuter Trip Hours - Morning'
            ELSE 'Commuter Trip Hours - Night'
        END AS trip_type,
        CAST(ROUND(AVG(bikes_available)) AS INT64) avg_bikes, 
    FROM `w205-project-300900.bike_trip_data.station_status` AS stat
    LEFT JOIN (
        WITH top10 AS(
            SELECT start_station_name, start_station_id, end_station_name, end_station_id, COUNT(trip_id) AS num_trips,
            FROM `w205-project-300900.bike_trip_data.commuter_trips`
            GROUP BY start_station_name, start_station_id, end_station_name, end_station_id
            ORDER BY num_trips DESC
            LIMIT 10)
        SELECT top10.start_station_name, top10.start_station_id FROM top10
        UNION DISTINCT
        SELECT top10.end_station_name, top10.end_station_id FROM top10) top10
    ON stat.station_id = top10.start_station_id
    WHERE station_id IN (top10.start_station_id)
        AND dow_str IN ('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday')
        AND hour IN (7,8,9,16,17,18)
    GROUP BY station_id, start_station_name, trip_type
    ORDER BY station_id, trip_type;
  ```

*See Project_1 Jupyter Notebook Appendix for code to create the station\_status view

---

## Part 3 - Synthesizing and visualizing analysis results

For the final synthesis and visualization of the Lyft Bay Wheel data analysis, please refer to the [Lyft Bay Wheels Ridership Analysis notebook](LyftBayWheels_Ridership_Analysis.ipynb)
