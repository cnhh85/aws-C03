# Data & Analytics

## Amazon Athena

- **Serverless** query service to analyse data stored in Amazon S3
- Uses standard SQL language to query the files (built on Presto)
- Supports CSV, JSON, ORC, Avro, and Parquet
- Pricing: $5.00 per TB of data scanned
- Commonly used with Amazon Quicksight for reporting/dashboards

  ---

- Use cases: Business intelligence/analytics/reporting, analyse & query VPC Flow Logs, ELB Logs, CloudTrail trails, etc...

> Exam Tip: analyse data in S3 using serverless SQL, use Athena

### Amazon Athena – Performance Improvement

- Use **columnar data** for cost-savings (fewer scans)
  - Apache Parquet or ORC is recommended
  - Huge performance improvement
  - Use Glue to convert your data to Parquet or ORC
- **Compress data** for smaller retrievals (bzip2, gzip, lz4, snappy, zlip, zstd…)
- **Partition** datasets in S3 for easy querying on virtual columns

``` plain text
  - s3://yourBucket/pathToTable
                   /<PARTITION_COLUMN_NAME>=<VALUE>
                    /<PARTITION_COLUMN_NAME>=<VALUE>
                     /<PARTITION_COLUMN_NAME>=<VALUE>
                      /etc…
```

- Example: s3://athena-examples/flight/parquet/year=1991/month=1/day=1/
- **Use larger files** (> 128 MB) to minimise overhead

### Amazon Athena – Federated Query

- Allows you to run SQL queries across data stored in relational, non-relational, object, and custom data sources (AWS or on-premises)
- Uses Data Source Connectors that run on AWS Lambda to run Federated Queries (e.g., CloudWatch Logs, DynamoDB, RDS, …)
- Store the results back in Amazon S3

![[Pasted image 20260131001604.png|500]]

## Redshift Overview

- Redshift is based on PostgreSQL, but it’s not used for OLTP
- **It’s OLAP – online analytical processing (analytics and data warehousing)**
- 10x better performance than other data warehouses, scales to PBs of data
- **Columnar** storage of data (instead of row-based) & parallel query engine
- Two modes: Provisioned cluster or Serverless cluster
- Has a SQL interface for performing the queries
- BI tools such as Amazon Quicksight or Tableau integrate with it
- **vs Athena**: faster queries/joins/aggregations thanks to indexes

### Redshift Cluster

- Leader node: for query planning, results aggregation
- Compute node: for performing the queries, send results to the leader
- Provisioned mode:
  - Choose instance types in advance
  - Can reserve instances for cost savings

![[Pasted image 20260131002033.png|400]]

### Redshift – Snapshots & DR

- **Redshift has “Multi-AZ” mode for some clusters**
- Snapshots are point-in-time backups of a cluster, stored internally in S3
- Snapshots are incremental (only what has changed is saved)
- You can restore a snapshot into a new cluster
- Automated: every 8 hours, every 5 GB, or on a schedule. Set retention between 1 and 35 days
- Manual: snapshot is retained until you delete it

> You can configure Amazon Redshift to automatically copy snapshots (automated or manual) of a cluster to another AWS Region

### Loading data into Redshift: Large inserts are MUCH better

![[Pasted image 20260131002155.png]]

> Enhanced VPC Routing

### Redshift Spectrum

- Query data that is already in S3 without loading it
- **Must have a Redshift cluster available to start the query**
- The query is then submitted to thousands of Redshift Spectrum nodes

![[Pasted image 20260131002303.png|500]]

## Amazon OpenSearch Service

- **Amazon OpenSearch is the successor to Amazon ElasticSearch**
- In DynamoDB, queries only exist by primary key or indexes…
- **With OpenSearch, you can search any field, even partially matches**
- It’s common to use OpenSearch as a complement to another database
- Two modes: managed cluster or serverless cluster
- Does `not` natively support SQL (can be enabled via a plugin)
- Ingestion from Kinesis Data Firehose, AWS IoT, and CloudWatch Logs
- Security through Cognito & IAM, KMS encryption, TLS
- Comes with OpenSearch Dashboards (visualisation)

### OpenSearch patterns // DynamoDB

![[Pasted image 20260131002414.png]]

### OpenSearch patterns // CloudWatch Logs

![[Pasted image 20260131002431.png]]

### OpenSearch patterns // Kinesis Data Streams & Kinesis Data Firehose

![[Pasted image 20260131002448.png]]

## Amazon EMR

- EMR stands for “Elastic MapReduce”
- EMR helps creating **Hadoop** clusters (Big Data) to analyse and process a vast amount of data
- The clusters can be made of hundreds of EC2 instances
- EMR comes bundled with Apache Spark, HBase, Presto, Flink…
- EMR takes care of all the provisioning and configuration
- Auto-scaling is integrated with Spot instances

> Use cases: data processing, machine learning, web indexing, big data…

### Amazon EMR – Node types & purchasing

- **Master Node**: Manage the cluster, coordinate, and manage health – long-running
- **Core Node**: Run tasks and store data – long running
- **Task Node** (optional)**: Just to run tasks – usually Spot
- **Purchasing options:**
  - On-demand: reliable, predictable, won’t be terminated
  - Reserved (min 1 year): cost savings (EMR will automatically use if available)
  - Spot Instances: cheaper, can be terminated, less reliable
- Can have a long-running cluster, or a transient (temporary) cluster

## Amazon QuickSight

- **Serverless machine learning-powered business intelligence service to create interactive dashboards**
- Fast, automatically scalable, embeddable, with per-session pricing
- Use cases:
  - Business analytics
  - Building visualisations
  - Perform ad-hoc analysis
  - Get business insights using data
- Integrated with RDS, Aurora, Athena, Redshift, S3…
- **In-memory computation using the `SPICE`** engine if data is imported into QuickSight
- Enterprise edition: Possibility to set up **Column-Level security (CLS)**

### QuickSight Integrations

![[Pasted image 20260131003652.png]]

### QuickSight – Dashboard & Analysis

- Define Users (standard versions) and Groups (enterprise version)
  - These users & groups only exist within QuickSight, not IAM !!
- A dashboard…
  - is a read-only snapshot of an analysis that you can share
  - preserves the configuration of the analysis (filtering, parameters, controls, sort)

  ---

- **You can share the analysis or the dashboard with Users or Groups**
- To share a dashboard, you must first publish it
- Users who see the dashboard can also see the underlying data

## AWS Glue

- Managed **extract, transform, and load (ETL)** service
- Useful to prepare and transform data for analytics
- Fully **serverless** service

![[Pasted image 20260131003802.png]]

### AWS Glue – Convert data into Parquet format

![[Pasted image 20260131003816.png]]

### Glue Data Catalogue: catalogue of datasets

![[Pasted image 20260131003833.png]]

### Glue – things to know at a high-level

> Glue Job Bookmarks: prevent re-processing old data
---
> Glue DataBrew: clean and normalise data using pre-built transformation
---
> Glue Studio: new GUI to create, run and monitor ETL jobs in Glue
---
> Glue Streaming ETL (built on Apache Spark Structured Streaming): compatible with Kinesis Data Streaming, Kafka, MSK (managed Kafka)

## AWS Lake Formation

- **Data lake = a central place to have all your data for analytics purposes**
- Fully managed service that makes it easy to set up a **data lake** in days
- Discover, cleanse, transform, and ingest data into your Data Lake
- It automates many complex manual steps (collecting, cleansing, moving, cataloguing data, …) and deduplicates (using ML Transforms)
- Combine structured and unstructured data in the data lake
- **Out-of-the-box source blueprints**: S3, RDS, Relational & NoSQL DB…
- **Fine-grained Access Control for your applications (row and column-level)**
- Built on top of AWS Glue

![[Pasted image 20260131004235.png]]

### AWS Lake Formation //Centralised Permissions Example

![[Pasted image 20260131004252.png]]

## Amazon Managed Service for Apache Flink

- Previously named: Kinesis Data Analytics for Apache Flink
- Flink (Java, Scala or SQL) is a framework for processing data streams

![[Pasted image 20260131004327.png]]

- Run any Apache Flink application on a managed cluster on AWS
  - Provisioned compute resources, parallel computation, and automatic scaling
  - Application backups (implemented as checkpoints and snapshots)
  - Use any Apache Flink programming features to transform data
  - Important: Flink does not read from Amazon Data Firehose

## Amazon Managed Streaming for Apache // Kafka (Amazon MSK)

- Alternative to Amazon Kinesis
- Fully managed Apache Kafka on AWS
  - Allow you to create, update, and delete clusters
  - MSK creates & manages Kafka broker nodes & Zookeeper nodes for you
  - Deploy the MSK cluster in your VPC, multi-AZ (up to 3 for HA)
  - Automatic recovery from common Apache Kafka failures
  - Data is stored on EBS volumes **for as long as you want**
- **MSK Serverless**
  - Run Apache Kafka on MSK without managing the capacity
  - MSK automatically provisions resources and scales compute & storage

### Apache Kafka at a high level

![[Pasted image 20260131004443.png]]

### Kinesis Data Streams vs. Amazon MSK

| Kinesis Data Streams      | Amazon MSK                                   |
| ------------------------- | -------------------------------------------- |
| 1 MB message size limit   | 1MB default, configure for higher (ex: 10MB) |
| Data Streams with Shards  | Kafka Topics with Partitions                 |
| Shard Splitting & Merging | Can only add partitions to a topic           |
| TLS In-flight encryption  | PLAINTEXT or TLS In-flight Encryption        |
| KMS at-rest encryption    | KMS at-rest encryption                       |
