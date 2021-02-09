# Project 1: Query Project

- In the Query Project, you will get practice with SQL while learning about
  Google Cloud Platform (GCP) and BiqQuery. You'll answer business-driven
  questions using public datasets housed in GCP. To give you experience with
  different ways to use those datasets, you will use the web UI (BiqQuery) and
  the command-line tools, and work with them in Jupyter Notebooks.

#### Problem Statement

- You're a data scientist at Lyft Bay Wheels (https://www.lyft.com/bikes/bay-wheels), formerly known as Ford GoBike, the
  company running Bay Area Bikeshare. You are trying to increase ridership, and
  you want to offer deals through the mobile app to do so. 
  
- What deals do you offer though? Currently, your company has several options which can change over time.  Please visit the website to see the current offers and other marketing information. Frequent offers include: 
  * Single Ride 
  * Monthly Membership
  * Annual Membership
  * Bike Share for All
  * Access Pass
  * Corporate Membership
  * etc.

- Through this project, you will answer these questions: 

  * What are the 5 most popular trips that you would call "commuter trips"? 
  
  * What are your recommendations for offers (justify based on your findings)?

- Please note that there are no exact answers to the above questions, just like in the proverbial real world.  This is not a simple exercise where each question above will have a simple SQL query. It is an exercise in analytics over inexact and dirty data. 

- You won't find a column in a table labeled "commuter trip".  You will find you need to do quite a bit of data exploration using SQL queries to determine your own definition of a communter trip.  In data exploration process, you will find a lot of dirty data, that you will need to either clean or filter out. You will then write SQL queries to find the communter trips.

- Likewise to make your recommendations, you will need to do data exploration, cleaning or filtering dirty data, etc. to come up with the final queries that will give you the supporting data for your recommendations. You can make any recommendations regarding the offers, including, but not limited to: 
  * market offers differently to generate more revenue 
  * remove offers that are not working 
  * modify exising offers to generate more revenue
  * create new offers for hidden business opportunities you have found
  * etc. 

#### All Work MUST be done in the Google Cloud Platform (GCP) / The Majority of Work MUST be done using BigQuery SQL / Usage of Temporary Tables, Views, Pandas, Data Visualizations

A couple of the goals of w205 are for students to learn how to work in a cloud environment (such as GCP) and how to use SQL against a big data data platform (such as Google BigQuery).  In keeping with these goals, please do all of your work in GCP, and the majority of your analytics work using BigQuery SQL queries.

You can make intermediate temporary tables or views in your own dataset in BigQuery as you like.  Actually, this is a great way to work!  These make data exploration much easier.  It's much easier when you have made temporary tables or views with only clean data, filtered rows, filtered columns, new columns, summary data, etc.  If you use intermediate temporary tables or views, you should include the SQL used to create these, along with a brief note mentioning that you used the temporary table or view.

In the final Jupyter Notebook, the results of your BigQuery SQL will be read into Pandas, where you will use the skills you learned in the Python class to print formatted Pandas tables, simple data visualizations using Seaborn / Matplotlib, etc.  You can use Pandas for simple transformations, but please remember the bulk of work should be done using Google BigQuery SQL.

#### GitHub Procedures

In your Python class you used GitHub, with a single repo for all assignments, where you committed without doing a pull request.  In this class, we will try to mimic the real world more closely, so our procedures will be enhanced. 

Each project, including this one, will have it's own repo.

Important:  In w205, please never merge your assignment branch to the master branch. 

Using the git command line: clone down the repo, leave the master branch untouched, create an assignment branch, and move to that branch:
- Open a linux command line to your virtual machine and be sure you are logged in as jupyter.
- Create a ~/w205 directory if it does not already exist `mkdir ~/w205`
- Change directory into the ~/w205 directory `cd ~/w205`
- Clone down your repo `git clone <https url for your repo>`
- Change directory into the repo `cd <repo name>`
- Create an assignment branch `git branch assignment`
- Checkout the assignment branch `git checkout assignment`

The previous steps only need to be done once.  Once you your clone is on the assignment branch it will remain on that branch unless you checkout another branch.

The project workflow follows this pattern, which may be repeated as many times as needed.  In fact it's best to do this frequently as it saves your work into GitHub in case your virtual machine becomes corrupt:
- Make changes to existing files as needed.
- Add new files as needed
- Stage modified files `git add <filename>`
- Commit staged files `git commit -m "<meaningful comment about your changes>"`
- Push the commit on your assignment branch from your clone to GitHub `git push origin assignment`

Once you are done, go to the GitHub web interface and create a pull request comparing the assignment branch to the master branch.  Add your instructor, and only your instructor, as the reviewer.  The date and time stamp of the pull request is considered the submission time for late penalties. 

If you decide to make more changes after you have created a pull request, you can simply close the pull request (without merge!), make more changes, stage, commit, push, and create a final pull request when you are done.  Note that the last data and time stamp of the last pull request will be considered the submission time for late penalties.

Make sure you receive the emails related to your repository! Your project feedback will be given as comment on the pull request. When you receive the feedback, you can address problems or simply comment that you have read the feedback. 
AFTER receiving and answering the feedback, merge you PR to master. Your project only counts as complete once this is done.

---

## Parts 1, 2, 3

We have broken down this project into 3 parts, about 1 week's work each to help you stay on track.

**You will only turn in the project once at the end of part 3!**

- In Part 1, we will query using the Google BigQuery GUI interface in the cloud.

- In Part 2, we will query using the Linux command line from our virtual machine in the cloud.

- In Part 3, we will query from a Jupyter Notebook in our virtual machine in the cloud, save the results into Pandas, and present a report enhanced by Pandas output tables and simple data visualizations using Seaborn / Matplotlib.

---

## Part 1 - Querying Data with BigQuery

### SQL Tutorial

Please go through this SQL tutorial to help you learn the basics of SQL to help you complete this project.

SQL tutorial: https://www.w3schools.com/sql/default.asp

### Google Cloud Helpful Links

Read: https://cloud.google.com/docs/overview/

BigQuery: https://cloud.google.com/bigquery/

Public Datasets: Bring up your Google BigQuery console, open the menu for the public datasets, and navigate to the the dataset san_francisco.

- The Bay Bike Share has two datasets: a static one and a dynamic one.  The static one covers an historic period of about 3 years.  The dynamic one updates every 10 minutes or so.  THE STATIC ONE IS THE ONE WE WILL USE IN CLASS AND IN THE PROJECT. The reason is that is much easier to learn SQL against a static target instead of a moving target.

- (USE THESE TABLES!) The static tables we will be using in this class are in the dataset **san_francisco** :

  * bikeshare_stations

  * bikeshare_status

  * bikeshare_trips

- The dynamic tables are found in the dataset **san_francisco_bikeshare**

### Some initial queries

Paste your SQL query and answer the question in a sentence.  Be sure you properly format your queries and results using markdown. 

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

### Questions of your own
- Make up 3 questions and answer them using the Bay Area Bike Share Trips Data.  These questions MUST be different than any of the questions and queries you ran above.

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

### Bonus activity queries (optional - not graded - just this section is optional, all other sections are required)

The bike share dynamic dataset offers multiple tables that can be joined to learn more interesting facts about the bike share business across all regions. These advanced queries are designed to challenge you to explore the other tables, using only the available metadata to create views that give you a broader understanding of the overall volumes across the regions(each region has multiple stations)

We can create a temporary table or view against the dynamic dataset to join to our static dataset.

Here is some SQL to pull the region_id and station_id from the dynamic dataset.  You can save the results of this query to a temporary table or view.  You can then join the static tables to this table or view to find the region:
```sql
#standardSQL
select distinct region_id, station_id
from `bigquery-public-data.san_francisco_bikeshare.bikeshare_station_info`
```

- Top 5 popular station pairs in each region

| start_station_name                   | end_station_name                         | region_id | num_trips | regional_top_station_pairs |
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

```sql 
SELECT start_station_name, end_station_name, region_id, num_trips, regional_top_station_pairs FROM (
    SELECT start_station_name, start_station_id, end_station_name, end_station_id, region_id, COUNT(trip_id) AS num_trips,
    row_number() OVER (PARTITION BY region_id ORDER BY COUNT(trip_id) DESC) AS regional_top_station_pairs
    FROM `w205-project-300900.bike_trip_data.trips_with_regions`
    GROUP BY start_station_name, start_station_id, end_station_name, end_station_id, region_id
    ORDER BY num_trips DESC)
WHERE regional_top_station_pairs <= 5
ORDER BY region_id;
```

*See Project_1 Jupyter Notebook Appendix for code to create the trip\_with\_regions view

- Top 3 most popular regions(stations belong within 1 region)

| region_name   | region_id | num_trips       |
|---------------|-----------|-----------------|
| San Francisco |         3 |          748309 |
| Oakland       |        12 |           11674 |
| San Jose      |         5 |            2002 |

```sql
SELECT region_name, region_id, COUNT(trip_id) AS num_trips
FROM `w205-project-300900.bike_trip_data.trips_with_regions`
GROUP BY region_name, region_id
ORDER BY num_trips DESC;
```

- Total trips for each short station name in each region

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

```sql
SELECT station_short_name, region_name, COUNT(trip_id) AS num_trips
FROM `w205-project-300900.bike_trip_data.trips_with_regions`
GROUP BY station_short_name, region_name
ORDER BY region_name;
```

- What are the top 10 used bikes in each of the top 3 region. these bikes could be in need of more frequent maintenance.

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

## Part 2 - Querying data from the BigQuery CLI 

- Use BQ from the Linux command line:

  * General query structure

    ```
    bq query --use_legacy_sql=false '
        SELECT count(*)
        FROM
           `bigquery-public-data.san_francisco.bikeshare_trips`'
    ```

### Queries

1. Rerun the first 3 queries from Part 1 using bq command line tool (Paste your bq
   queries and results here, using properly formatted markdown):

  * What's the size of this dataset? (i.e., how many trips)
  
  ```
  bq query --use_legacy_sql=false '
      SELECT COUNT(*) AS num_trips
      FROM `bigquery-public-data.san_francisco.bikeshare_trips`;'
  ```
  
    +-----------+
    | num_trips |
    +-----------+
    |    983648 |
    +-----------+

  * What is the earliest start time and latest end time for a trip?
  
  ```
  bq query --use_legacy_sql=false '
      SELECT MIN(start_date), MAX(end_date)
      FROM `bigquery-public-data.san_francisco.bikeshare_trips`;'
  ```
  
    +---------------------+---------------------+
    |   min_start_time    |    max_end_time     |
    +---------------------+---------------------+
    | 2013-08-29 09:08:00 | 2016-08-31 23:48:00 |
    +---------------------+---------------------+

  * How many bikes are there?
  
  ```
  bq query --use_legacy_sql=false '
      SELECT COUNT(DISTINCT(bike_number))
      FROM `bigquery-public-data.san_francisco.bikeshare_trips`;'
  ```
  
    +-----------+
    | num_bikes |
    +-----------+
    |       700 |
    +-----------+

2. New Query (Run using bq and paste your SQL query and answer the question in a sentence, using properly formatted markdown):

  * How many trips are in the morning vs in the afternoon?
  
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

### Project Questions
Identify the main questions you'll need to answer to make recommendations (list
below, add as many questions as you need).

- Question 1: 

Out of the day-of-week, hour-of-day pairs that have at least 10,000 trips, what are the combinations of days and starting hours?

- Question 2: 

Given the answer to Question 1 above, what is the proportion of these trips in comparison to the total number of trips?

- Question 3: 

Given the answer to Question 1 above, if we classify "commuter trips" as trips starting between 7:00-10:00 or 16:00-19:00 during the week, what are the most frequent type of customers who take these "commuter trips"?

- Question 4: 

What are the top 10 most popular station pairs for these "commuter trips"?

- Question 5: 

What is the average and minimum avaibility of bikes at these stations during "commuter trip" hours?


### Answers

Answer at least 4 of the questions you identified above You can use either
BigQuery or the bq command line tool.  Paste your questions, queries and
answers below.

- Question 1: Out of all the day-of-week, hour-of-day pairs that have at least 10,000 trips, what are the combinations of days and starting hours?

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

## Part 3 - Employ notebooks to synthesize query project results

### Get Going

Create a Jupyter Notebook against a Python 3 kernel named Project_1.ipynb in the assignment branch of your repo.

A link to the Jupyter Notebook containg part 3 of the analysis is included below:

[Project 1 Jupyter Notebook](./Project_1.ipynb)

#### Run queries in the notebook 

At the end of this document is an example Jupyter Notebook you can take a look at and run.  

You can run queries using the "bang" command to shell out, such as this:

```
! bq query --use_legacy_sql=FALSE '<your-query-here>'
```

- NOTE: 
- Queries that return over 16K rows will not run this way, 
- Run groupbys etc in the bq web interface and save that as a table in BQ. 
- Max rows is defaulted to 100, use the command line parameter `--max_rows=1000000` to make it larger
- Query those tables the same way as in `example.ipynb`

Or you can use the magic commands, such as this:

```sql
%%bigquery my_panda_data_frame

select start_station_name, end_station_name
from `bigquery-public-data.san_francisco.bikeshare_trips`
where start_station_name <> end_station_name
limit 10
```

```python
my_panda_data_frame
```

#### Report in the form of the Jupter Notebook named Project_1.ipynb

- Using markdown cells, MUST definitively state and answer the two project questions:

  * What are the 5 most popular trips that you would call "commuter trips"? 
  
  * What are your recommendations for offers (justify based on your findings)?

- For any temporary tables (or views) that you created, include the SQL in markdown cells

- Use code cells for SQL you ran to load into Pandas, either using the !bq or the magic commands

- Use code cells to create Pandas formatted output tables (at least 3) to present or support your findings

- Use code cells to create simple data visualizations using Seaborn / Matplotlib (at least 2) to present or support your findings

### Resource: see example .ipynb file 

[Example Notebook](example.ipynb)

