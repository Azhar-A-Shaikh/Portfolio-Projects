# Hive Project Overview

## Tech Stack Used.
* **Hadoop** :- Hadoop is a distributed processing framework that enables users to store and process large datasets across clusters of computers.
* **Hive** :- Hive is a data warehousing tool that enables users to query and analyze large datasets stored in Hadoop Distributed File System (HDFS)
* **Docker** :- Docker is an open-source platform that enables software applications to run consistently across different environments by packaging them into containers.

**By utilizing Docker, I have encapsulated the entire Hadoop Hive framework within a single container. This containerization approach allows for seamless deployment and consistent execution of Hive across various environments, providing the benefits of portability, scalability, and ease of management. With Docker, Hadoop Hive becomes a self-contained unit that can be effortlessly deployed, shared, and operated, simplifying the setup and configuration process while ensuring reliable and efficient execution of Hive workflows.**

### How Hive and Hadoop Work

* Hadoop breaks down large datasets into smaller chunks and distributes them across a cluster of computers. This allows data processing to be done in parallel across multiple nodes, resulting in faster processing times.
* Hive provides a SQL-like interface to query and analyze data stored in Hadoop. It translates queries into MapReduce jobs, which are executed across the Hadoop cluster.
* Hive uses a schema-on-read approach, which means that data is only structured when it is queried. This allows for flexibility in data exploration and analysis, as new data can be added to the Hadoop cluster without the need to restructure the entire dataset.
* Hive provides a wide range of built-in functions and support for custom user-defined functions (UDFs), enabling users to perform complex data transformations and analysis.

#### Apache Hive Architecture

![Apache Hive Architecture](https://cdn.analyticsvidhya.com/wp-content/uploads/2020/10/Screenshot-from-2020-10-25-20-47-32.png)


## Overview
The Projects involves working with Hive to analyze and query data stored in HDFS. Various queries were performed on the dataset, and techniques like partitioning and bucketing were applied to enhance data organization and query performance.

## Dataset
The Projects utilizes a dataset stored in HDFS. The data is stored in various files in the HDFS file system.

## Data Loading
The dataset was loaded into Hive tables using the following steps:

- Create an external table in Hive to define the schema and structure of the data.
- Load the data from HDFS into the Hive table using the `LOAD DATA` statement.
- Verify the data load by executing sample queries to retrieve and examine the loaded data.

## Data Querying
Several queries were executed on the dataset to extract meaningful insights and perform data analysis. Some examples of the queries performed include:

- How often does each violation code occur? (frequency of violation codes - find the top 5)
  ```
  SELECT violation_code, COUNT(*) AS frequency
  FROM newyork_parking
  GROUP BY violation_code
  ORDER BY frequency DESC
  LIMIT 5;
  ```
  
- How often does each vehicle body type get a parking ticket? How about the vehicle make? (find the top 5 for both)
  ```
  SELECT summ_veh_body, COUNT(*) AS frequency
  FROM newyork_parking
  GROUP BY summ_veh_body
  ORDER BY frequency DESC
  LIMIT 5;
  ```
  
- Divide 24 hours into 6 equal discrete bins of time. The intervals you choose are at your discretion. For each of these groups, find the 3 most commonly occurring violations.
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

## Data Organization Techniques
To optimize query performance and improve data organization, partitioning and bucketing techniques were employed:

1. **Partitioning:** The dataset was partitioned based on [partitioning key/column], dividing the data into separate partitions for faster data retrieval.
2. **Bucketing:** The dataset was bucketed based on [bucketing key/column], distributing the data into buckets to improve query performance.

#### How Partitioning & Bucketing Looks
![How Partitioning & Bucketing Looks like](https://cdn.analyticsvidhya.com/wp-content/uploads/2020/11/Screenshot-from-2020-11-11-14-41-05.png)

## Results and Findings
The analysis of the dataset and the execution of various queries yielded the following key findings:

## Conclusion
In conclusion, this Hive projects involved loading data from HDFS into Hive tables, performing various queries, and applying partitioning and bucketing techniques to improve data organization and query performance. The analysis of the dataset provided valuable insights and supported data-driven decision-making.

For more detailed information, please refer to the corresponding scripts, HIVEQL -( HQL ) file) in each project folder.
