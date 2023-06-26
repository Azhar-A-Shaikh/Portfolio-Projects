# Ney York Parking Tickets Dataset :- 2GB File 

### LINK :- https://www.kaggle.com/datasets/new-york-city/nyc-parking-tickets

**About Dataset**

**Context**
The NYC Department of Finance collects data on every parking ticket issued in NYC (~10M per year!). This data is made publicly available to aid in ticket resolution and to guide policymakers.
There are four files, covering Aug 2013-June 2017. The files are roughly organized by fiscal year (July 1 - June 30) with the exception of the initial dataset. 
The initial dataset also lacks 8 columns that are included in the other three datasets (although be warned that these additional data columns are used sparingly). 
See the dataset descriptions for exact details. Columns include information about the vehicle ticketed, the ticket issued, location, and time.

**Acknowledgements**
Data was produced by NYC Department of Finance. FY2018 data is found here with updates every third week of the month.

**Inspiration**
* When are tickets most likely to be issued? Any seasonality?
* Where are tickets most commonly issued?
* What are the most common years and types of cars to be ticketed?

**Context for the columns**

Summons Number: Nu
Plate ID:Plain Text
Registration State: Plain Text
Plate Type: Plain Text
Issue Date: Date & Time
Violation Code: Number
Vehicle Body Type: Plain Text
Vehicle Make: Plain Text
Issuing Agency: Plain Text
Street Code1: Number
Street Code2: Number
Street Code3: Number
Vehicle Expiration Date: Number
Violation Location: Plain Text
Violation Precinct: Number
Issuer Precinct: Number
Issuer Code: Number
Issuer Command: Plain Text
Issuer Squad: Plain Text
Violation Time: Plain Text
Time First Observed: Plain Text
Violation County: Plain Text
Violation In Front Of Or Opposite: Plain Text
House Number: Plain Text
Street Name: Plain Text
Intersecting Street: Plain Text
Date First Observed: Number
Law Section: Number
Sub Division: Plain Text
Violation Legal Code: Plain Text
Days Parking In Effect: Plain Text
From Hours In Effect: Plain Text
To Hours In Effect: Plain Text
Vehicle Color: Plain Text
Unregistered Vehicle?: Plain Text
Vehicle Year: Number
Meter Number: Plain Text
Feet From Curb: Number
Violation Post Code: Plain Text
Violation Description: Plain Text
No Standing or Stopping Violation: Plain Text
Hydrant Violation: Plain Text
Double Parking Violation: Plain Text


## Creating External Hive table 

```
CREATE EXTERNAL TABLE IF NOT EXISTS newyork_parking (
  summons_number INT,
  plate_id STRING,
  registration_state STRING,
  plate_type STRING,
  issue_date DATE,
  violation_code INT,
  summ_veh_body STRING,
  summ_veh_make STRING,
  issuing_agency STRING,
  street_code1 INT,
  street_code2 INT,
  street_code3 INT,
  vehicle_expiration_date INT,
  violation_location INT,
  violation_precinct INT,
  issuer_precinct INT,
  issuer_code INT,
  issuer_command STRING,
  issuer_squad STRING,
  violation_time STRING,
  time_first_observed INT,
  violation_county STRING,
  front_of_opposite STRING,
  house_number STRING,
  street_name STRING,
  intersecting_street STRING,
  date_first_observed INT,
  law_section INT,
  sub_division STRING,
  violation_legal_code STRING,
  days_in_effect STRING,
  from_hours_in_effect STRING,
  to_hours_in_effect STRING,
  vehicle_color STRING,
  unregistered_vehicle BOOLEAN,
  vehicle_year INT,
  meter_number STRING,
  feet_from_corb INT,
  violation_post_code STRING,
  violation_description STRING,
  no_standing_or_stopping_violation STRING,
  hydrant_violation STRING,
  double_parking_violation STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
location 'data/all_data/parking_violations_issued_fiscal_year_2017.csv';
```
# Quries. 

#### 1) Find the total number of tickets for the year.
```
SELECT COUNT(*) as total_tickets
FROM newyork_parking
WHERE YEAR(issue_date) = 2017;
```

#### 2) Find out how many unique states the cars which got parking tickets came from.

```
SELECT COUNT(DISTINCT registration_state) as unique_states
FROM newyork_parking;
```

#### 3) Some parking tickets don’t have addresses on them, which is cause for concern. Find out how many such tickets there are(i.e. tickets where either "Street Code 1" or "Street Code 2" or "Street Code 3" is empty )

```
SELECT COUNT(*) as missing_address_tickets
FROM newyork_parking
WHERE street_code1 = '' OR street_code2 = '' OR street_code3 = '';
```

#### 4) How often does each violation code occur? (frequency of violation codes - find the top 5)

```
SELECT violation_code, COUNT(*) AS frequency
FROM newyork_parking
GROUP BY violation_code
ORDER BY frequency DESC
LIMIT 5;
```

#### 5) How often does each vehicle body type get a parking ticket? How about the vehicle make? (find the top 5 for both)

**Query The Body type of the Vehincle**
```
SELECT summ_veh_body, COUNT(*) AS frequency
FROM newyork_parking
GROUP BY summ_veh_body
ORDER BY frequency DESC
LIMIT 5;
```

**Query the Brand of Vehincles**

```
SELECT summ_veh_make, COUNT(*) AS frequency
FROM newyork_parking
GROUP BY summ_veh_make
ORDER BY frequency DESC
LIMIT 5;
```

**Query the body type and Brand of vehicles**

```
SELECT 'Vehicle Body Type' AS category, summ_veh_body AS attribute, COUNT(*) AS frequency
FROM newyork_parking
GROUP BY summ_veh_body
ORDER BY frequency DESC
LIMIT 5
UNION ALL
SELECT 'Vehicle Make' AS category, summ_veh_make AS attribute, COUNT(*) AS frequency
FROM newyork_parking
GROUP BY summ_veh_make
ORDER BY frequency DESC
LIMIT 5;
```

#### 6) A precinct is a police station that has a certain zone of the city under its command. Find the (5 highest) frequencies of:

**Query the TOP 5 zones where the violation occurred**

```
SELECT violation_precinct, COUNT(*) AS frequency
FROM newyork_parking
GROUP BY violation_precinct
ORDER BY frequency DESC
LIMIT 5;
```

**Query the TOP 5 precinct that issued the tickets**
```
SELECT issuer_precinct, COUNT(*) AS frequency
FROM newyork_parking
GROUP BY issuer_precinct
ORDER BY frequency DESC
LIMIT 5;
```
   
**Find the violation code frequency across 3 precincts which have issued the most number of tickets &
Do these precinct zones have an exceptionally high frequency of certain violation codes?**

```
SELECT np.issuer_precinct, np.violation_code, COUNT(*) AS frequency
FROM newyork_parking np
WHERE np.issuer_precinct IN (
  SELECT issuer_precinct
  FROM newyork_parking
  GROUP BY issuer_precinct
  ORDER BY COUNT(*) DESC
  LIMIT 3
)
GROUP BY np.issuer_precinct, np.violation_code;
```

**Find out the properties of parking violations across different times of the day: The Violation Time field is specified in a strange format. 
Find a way to make this into a time attribute that you can use to divide into groups.**

```
SELECT SUBSTR(violation_time, 1, 2) AS hour, COUNT(*) AS frequency
FROM newyork_parking
GROUP BY SUBSTR(violation_time, 1, 2);
```


**Divide 24 hours into 6 equal discrete bins of time. The intervals you choose are at your discretion. 
For each of these groups, find the 3 most commonly occurring violations.**

```
SELECT
  CASE
    WHEN SUBSTR(violation_time, 1, 2) BETWEEN '00' AND '03' THEN '00:00-03:59'
    WHEN SUBSTR(violation_time, 1, 2) BETWEEN '04' AND '07' THEN '04:00-07:59'
    WHEN SUBSTR(violation_time, 1, 2) BETWEEN '08' AND '11' THEN '08:00-11:59'
    WHEN SUBSTR(violation_time, 1, 2) BETWEEN '12' AND '15' THEN '12:00-15:59'
    WHEN SUBSTR(violation_time, 1, 2) BETWEEN '16' AND '19' THEN '16:00-19:59'
    ELSE '20:00-23:59'
  END AS hour,
  violation_code
FROM newyork_parking
```

#### 7) Query for the 3 most commonly occurring violation codes, find the most common times of day (in terms of the bins from the previous part) 

**Query the Tickers based on Different Seasons in New york
(Hint: A quick Google search reveals the following seasons in NYC: Spring(March, April, March); Summer(June, July, August); 
Fall(September, October, November); Winter(December, January, February))**

```
SELECT 
CASE 
  WHEN month IN (3, 4, 5) THEN 'Spring'
  WHEN month IN (6, 7, 8) THEN 'Summer'
  WHEN month IN (9, 10, 11) THEN 'Fall'
  ELSE 'Winter'
END AS season, 
COUNT(*) AS frequency
FROM ( SELECT 
  SUBSTR(issue_date, 6, 2) AS month 
FROM newyork_parking) t
GROUP BY season;
```

**Query to  find the 3 most common type of violations for each of these seasons.**

```
SELECT season, violation_code, COUNT(*) AS frequency
FROM (
  SELECT
    CASE
      WHEN month IN (3, 4, 5) THEN 'Spring'
      WHEN month IN (6, 7, 8) THEN 'Summer'
      WHEN month IN (9, 10, 11) THEN 'Fall'
      ELSE 'Winter'
    END AS season,
    violation_code
  FROM newyork_parking
) AS seasons_violations
GROUP BY season, violation_code
ORDER BY season, frequency DESC
LIMIT 3;

```

#### Query to perform Static partitioning based on any Registration State 

ALTER TABLE newyork_parking
ADD PARTITION (registration_state='NY') LOCATION 'data/all_data/registration_state=NY'

#### Query for dynamic partitioning based on Registraction State

CREATE EXTERNAL TABLE IF NOT EXISTS newyork_parking (
  summons_number INT,
  plate_id STRING,
  registration_state STRING,
  plate_type STRING,
  issue_date DATE,
  violation_code INT,
  summ_veh_body STRING,
  summ_veh_make STRING,
  issuing_agency STRING,
  street_code1 INT,
  street_code2 INT,
  street_code3 INT,
  vehicle_expiration_date INT,
  violation_location INT,
  violation_precinct INT,
  issuer_precinct INT,
  issuer_code INT,
  issuer_command STRING,
  issuer_squad STRING,
  violation_time STRING,
  time_first_observed INT,
  violation_county STRING,
  front_of_opposite STRING,
  house_number STRING,
  street_name STRING,
  intersecting_street STRING,
  date_first_observed INT,
  law_section INT,
  sub_division STRING,
  violation_legal_code STRING,
  days_in_effect STRING,
  from_hours_in_effect STRING,
  to_hours_in_effect STRING,
  vehicle_color STRING,
  unregistered_vehicle BOOLEAN,
  vehicle_year INT,
  meter_number STRING,
  feet_from_corb INT,
  violation_post_code STRING,
  violation_description STRING,
  no_standing_or_stopping_violation STRING,
  hydrant_violation STRING,
  double_parking_violation STRING
)
PARTITIONED BY (registration_state STRING)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION 'data/all_data/parking_violations_issued_fiscal_year_2017.csv' ;




























