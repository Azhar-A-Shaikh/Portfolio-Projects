# Hive Project Description

## Overview
This project involves working with Hive to analyze and query data stored in HDFS. Various queries were performed on the dataset, and techniques like partitioning and bucketing were applied to enhance data organization and query performance.

## Dataset
The project utilizes a dataset stored in HDFS. The dataset contains [brief description of the dataset, e.g., customer information, sales transactions, etc.]. The data is stored in various files in the HDFS file system.

## Data Loading
The dataset was loaded into Hive tables using the following steps:

- Create an external table in Hive to define the schema and structure of the data.
- Load the data from HDFS into the Hive table using the `LOAD DATA` statement.
- Verify the data load by executing sample queries to retrieve and examine the loaded data.

## Data Querying
Several queries were executed on the dataset to extract meaningful insights and perform data analysis. Some examples of the queries performed include:

- [Provide examples of queries executed, such as aggregations, filtering, joins, etc.]
- How often does each violation code occur? (frequency of violation codes - find the top 5)
  SELECT violation_code, COUNT(*) AS frequency
  FROM newyork_parking
  GROUP BY violation_code
  ORDER BY frequency DESC
  LIMIT 5;
  
- How often does each vehicle body type get a parking ticket? How about the vehicle make? (find the top 5 for both)
  SELECT summ_veh_body, COUNT(*) AS frequency
  FROM newyork_parking
  GROUP BY summ_veh_body
  ORDER BY frequency DESC
  LIMIT 5;
  
- Divide 24 hours into 6 equal discrete bins of time. The intervals you choose are at your discretion. For each of these groups, find the 3 most commonly occurring violations.
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

## Data Organization Techniques
To optimize query performance and improve data organization, partitioning and bucketing techniques were employed:

1. **Partitioning:** The dataset was partitioned based on [partitioning key/column], dividing the data into separate partitions for faster data retrieval.
2. **Bucketing:** The dataset was bucketed based on [bucketing key/column], distributing the data into buckets to improve query performance.

## Results and Findings
The analysis of the dataset and the execution of various queries yielded the following key findings:

- [Describe the key insights, trends, or patterns discovered during the data analysis process.]

## Conclusion
In conclusion, this Hive project involved loading data from HDFS into Hive tables, performing various queries, and applying partitioning and bucketing techniques to improve data organization and query performance. The analysis of the dataset provided valuable insights and supported data-driven decision-making.

For more detailed information, please refer to the corresponding scripts, HIVEQL -( HQL ) file) in the project folders.
