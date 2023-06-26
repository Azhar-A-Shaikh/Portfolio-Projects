## Overview
This project focuses on building a data pipeline to convert customer spending data from multiple currencies into a unified currency. The data is retrieved from a Forex exchange API and stored in an AWS S3 bucket. Using an EC2 instance with Docker installed, a Python code is executed to pull the records from the API and store them in the S3 bucket. An EMR cluster is then set up to process the customer spending data and perform currency conversion which will later be analyzed. 

## Tech Stack

**The project utilizes the following technologies and services**

* **Docker:** Used to containerize the Hadoop Hive framework, enabling portability and easy deployment.
* **AWS EC2:** An EC2 instance is provisioned to host the Docker container and execute the Python code.
* **AWS S3:** Serves as the storage medium for both the Forex exchange data and customer spending data.
* **AWS Glue:** Provides ETL (Extract, Transform, Load) capabilities to transform and prepare the data for analysis.
* **AWS EMR (Elastic MapReduce):** An EMR cluster is deployed to process the data, perform currency conversion, and generate analysis results.
* **AWS IAM (Identity and Access Management):** Used for access control and setting up permissions for accessing various AWS services.
* **AWS Athena:** Enables interactive querying of the data in S3, allowing for efficient analysis and exploration of the final output result.

## Project Workflow

1. Provision an EC2 instance and install Docker on it.
2. Develop a Python code that retrieves records from the Forex exchange API and stores them in an S3 bucket.
3. Configure the EC2 instance to execute the Python code periodically, ensuring that the API data is regularly updated in the S3 bucket.
4. Set up an EMR cluster with Spark to process the customer transaction data and the API response data stored in separate S3 buckets.
5. Develop a Spark batch code that reads the customer transaction data and the API response data from their respective S3 buckets.
6. Perform currency conversion using the retrieved Forex exchange rates to convert the customer spending into a single currency, such as INR.
7. Store the transformed and unified customer spending data back into an S3 bucket.
8. Utilize Athena to query and analyze the unified customer spending data, taking advantage of its interactive querying capabilities and integration with S3.
9. Extract insights and generate analysis results from the Athena queries, enabling data-driven decision-making and further analysis.

## Conclusion
This project demonstrates a robust data pipeline leveraging AWS services, Docker, Spark, and Athena for efficient currency conversion and analysis of customer spending data. The EC2 instance, equipped with a Python code, seamlessly pulls records from the Forex exchange API and stores them in an S3 bucket, ensuring the availability of up-to-date exchange rates.

By utilizing an EMR cluster with Spark, the project efficiently processes customer transaction data from a separate S3 bucket and incorporates the API response data. The Spark batch code intelligently converts customer spending into a single currency, such as INR, making analysis and comparison simpler and more meaningful. The transformed and unified spending data is then stored back into an S3 bucket for easy accessibility and scalability.

The integration of Athena allows for interactive querying and analysis of the unified customer spending data, empowering data-driven decision-making. Through Athena, insights and analysis results can be extracted, providing valuable information to guide business strategies and optimize financial operations.

This project exemplifies the seamless orchestration of various technologies and services to streamline data workflows, simplify currency conversion, and enable insightful analysis. By leveraging AWS services, Docker, Spark, and Athena, the project offers a scalable, cost-effective, and efficient solution for processing and analyzing customer spending data across different currencies.
