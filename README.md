- Running the postgres and loading the data, i did it by using jupyter notebook.I didn't dockerize it but this folder contains everything that is required in homework 😁.
- I used command line arguments in running the postgresql in-order to be ready for connection, and i used a `pgcli` as a client to manipulate the Postgres database.


# This is SQL queries that i solved with homework questions:
1. How many taxi trips were totally made on September 18th 2019?

   Tip: started and finished on 2019-09-18.

   Remember that lpep_pickup_datetime and lpep_dropoff_datetime columns are in the format timestamp (date and hour+min+sec) and not in date.

   `SELECT COUNT(*) AS total_trips
   FROM green_taxi_data
   WHERE DATE(lpep_pickup_datetime) = '2019-09-18'
     AND DATE(lpep_dropoff_datetime) = '2019-09-18';
     `

2. Largest trip for each day
   Which was the pick up day with the largest trip distance Use the pick up time for your calculations.
   
   WITH DailyMaxDistance AS (
    `SELECT
        DATE(gt.lpep_pickup_datetime) AS pickup_day,
        MAX(gt.trip_distance) AS max_distance
    FROM
        green_taxi_data gt
    WHERE
        DATE(gt.lpep_pickup_datetime) IN ('2019-09-18', '2019-09-16', '2019-09-26', '2019-09-21')
    GROUP BY
        pickup_day
)

   SELECT
    pickup_day,
    max_distance
   FROM
    DailyMaxDistance;
    `

3. Three biggest pick up Boroughs
   Consider lpep_pickup_datetime in '2019-09-18' and ignoring Borough has Unknown

   Which were the 3 pick up Boroughs that had a sum of total_amount superior to 50000?

  `WITH BoroughTotalAmount AS (
    SELECT
        gt.PULocationID,
        z.Borough,
        SUM(gt.total_amount) AS total_amount_sum
    FROM
        green_taxi_data gt
    JOIN
        zones z ON gt.PULocationID = z.LocationID
    WHERE
        DATE(gt.lpep_pickup_datetime) = '2019-09-18'
        AND z.Borough IS NOT NULL
    GROUP BY
        gt.PULocationID, z.Borough
)

    SELECT
       Borough
    FROM
       BoroughTotalAmount
    WHERE
       total_amount_sum > 50000
    ORDER BY
       total_amount_sum DESC
    LIMIT 3;
    `

4. Largest tip
   For the passengers picked up in September 2019 in the zone name Astoria which was the drop off zone that had the largest tip? We want the name of the    zone, not the id.

   Note: it's not a typo, it's tip , not trip

   `WITH AstoriaTrips AS (
       SELECT
        gt.lpep_pickup_datetime,
        gt.lpep_dropoff_datetime,
        gt.tip_amount,
        z_dropoff."Zone" AS dropoff_zone
    FROM
        green_taxi_data gt
    JOIN
        zones z_pickup ON gt."PULocationID" = z_pickup."LocationID"
    JOIN
        zones z_dropoff ON gt."DOLocationID" = z_dropoff."LocationID"
    WHERE
        z_pickup."Zone" = 'Astoria'
        AND DATE(gt.lpep_pickup_datetime) >= '2019-09-01'
        AND DATE(gt.lpep_dropoff_datetime) <= '2019-09-30'
        AND gt.tip_amount IS NOT NULL
)

    SELECT
        dropoff_zone,
        MAX(tip_amount) AS largest_tip
    FROM
        AstoriaTrips
    GROUP BY
        dropoff_zone
    ORDER BY
        largest_tip DESC
    LIMIT 1;
    ` 
