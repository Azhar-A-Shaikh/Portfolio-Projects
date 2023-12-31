## Adding agentloginreport.csv and agentperformance.csv into the HDFS file system inside of Docker.
```
docker cp agentloginreport.csv namenode:agentloginreport.csv
```
```
docker cp agentperformance.csv namenode:agentperformance.csv 
```
```
docker exec -it namenode bash 
```
```
hdfs dfs -put agentloginreport.csv /data/all_data/
```

```
hdfs dfs -put agentperformance.csv /data/all_data/
```

## Creating the Hive Internal Tables for agent login report & agent performance file 

```
CREATE TABLE agentloginreport (
  sl_no INT,
  agent CHAR(20),
  date DATE,
  login_time TIMESTAMP,
  logout_time TIMESTAMP,
  duration INTERVAL
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
location '/data/all_data/agentloginreport.csv';
```

```
CREATE TABLE agentperformance (
  sl_no INT,
  date DATE,
  agent_name CHAR(20),
  total_chats INT,
  average_response_time TIMESTAMP,
  average_resolution_time TIMESTAMP,
  average_rating INT,
  total_feedback INT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
location '/data/all_data/agentperformance.csv';
```

### List of all agents' names.
```
SELECT DISTINCT agent FROM agentloginreport ORDER BY agent;
```

### Find out agent average rating.
```
SELECT agent_name, AVG(average_rating) AS avg_rating
FROM agentperformance
GROUP BY agent_name;
```

### Total working days for each agents 
```
SELECT agent, COUNT(DISTINCT report_date) AS total_working_days
FROM agentloginreport
GROUP BY agent;
```

### Total query that each agent have taken
```
SELECT agent, COUNT(*) AS total_queries_taken
FROM agentloginreport
GROUP BY agent;
```

### Total Feedback that each agent have received 
```
SELECT agent_name, SUM(total_feedback) AS total_feedback_received
FROM agentperformance
GROUP BY agent_name;
```

### Agent name who have average rating between 3.5 to 4 
```
SELECT agent_name
FROM agentperformance
WHERE average_rating BETWEEN 3.5 AND 4;
```

### Agent name who have rating less than 3.5 
```
SELECT agent_name
FROM agentperformance
WHERE average_rating < 3.5;
```

### Agent name who have rating more than 4.5 

```
SELECT agent_name FROM agentperformance WHERE average_rating > 4.5;
```

### How many feedback agents have received more than 4.5 average

```
SELECT agent_name, COUNT(*) as feedback_count FROM agentperformance WHERE average_rating > 4.5 AND total_feedback > 0 GROUP BY agent_name;
```

### Average weekly response time for each agent 

```
SELECT agent_name, WEEKOFYEAR(report_date) as week_number, 
AVG(UNIX_TIMESTAMP(average_response_time) - UNIX_TIMESTAMP('00:00:00')) / 3600 as avg_response_hours 
FROM agentperformance 
GROUP BY agent_name, WEEKOFYEAR(report_date);
```

### Average weekly resolution time for each agents 
```
SELECT agent_name, WEEKOFYEAR(report_date) as week_number, 
AVG(UNIX_TIMESTAMP(average_resolution_time) - UNIX_TIMESTAMP('00:00:00')) / 3600 as avg_resolution_hours 
FROM agentperformance 
GROUP BY agent_name, WEEKOFYEAR(report_date);
```

### Find the number of chat on which they have received a feedback 
```
SELECT agent_name, SUM(total_chats) as total_chats, 
SUM(total_feedback) as total_feedback_chats 
FROM agentperformance WHERE total_feedback > 0 
GROUP BY agent_name;
```

### Total contribution hour for each and every agents weekly basis 
```
SELECT agent_name, WEEKOFYEAR(report_date) as week_number, 
SUM(UNIX_TIMESTAMP(duration) - UNIX_TIMESTAMP('00:00:00')) / 3600 as total_contribution_hours 
FROM agentloginreport 
GROUP BY agent_name, WEEKOFYEAR(report_date);
```

### Perform inner join, left join and right join based on the agent column. 

**INNER JOIN**
```
INSERT OVERWRITE LOCAL DIRECTORY '/path/to/output/dir'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
SELECT *
FROM agentperformance
INNER JOIN agentloginreport ON agentperformance.agent_name = agentloginreport.agent;
```

**LEFT JOIN**
```
INSERT OVERWRITE LOCAL DIRECTORY '/path/to/output/dir'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
SELECT *
FROM agentperformance
LEFT JOIN agentloginreport ON agentperformance.agent_name = agentloginreport.agent;
```

**RIGHT JOIN**
```

INSERT OVERWRITE LOCAL DIRECTORY '/path/to/output/dir'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
SELECT *
FROM agentperformance
RIGHT JOIN agentloginreport ON agentperformance.agent_name = agentloginreport.agent;
```

### Q15. Perform partitioning on top of the agent column and then on top of that perform bucketing for each partitioning.

```
CREATE TABLE agent_performance_partitioned (
  sl_no INT,
  report_date DATE,
  agent_name CHAR(20),
  total_chats INT,
  average_response_time TIMESTAMP,
  average_resolution_time TIMESTAMP,
  average_rating INT,
  total_feedback INT
)
PARTITIONED BY (agent_partition INT)
CLUSTERED BY (agent_name) INTO 4 BUCKETS
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;

-- insert data into partitions
INSERT OVERWRITE TABLE agent_performance_partitioned PARTITION (agent_partition=1) SELECT * FROM agentperformance WHERE agent_name LIKE 'A%';
INSERT OVERWRITE TABLE agent_performance_partitioned PARTITION (agent_partition=2) SELECT * FROM agentperformance WHERE agent_name LIKE 'B%';
INSERT OVERWRITE TABLE agent_performance_partitioned PARTITION (agent_partition=3) SELECT * FROM agentperformance WHERE agent_name LIKE 'C%';
INSERT OVERWRITE TABLE agent_performance_partitioned PARTITION (agent_partition=4) SELECT * FROM agentperformance WHERE agent_name LIKE 'D%';
```






