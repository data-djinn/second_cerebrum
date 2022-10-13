- **Serverless ETL Service
	- no server/cluster provisioning (fully managed by AWS)
	-  categorize, clean, enrich, and move data between various data stores
##### Use cases
- query data in S3
	- data lake in s3 with customer feedback data
	- use AWS Glue to crawl your S3 data lake & prepare tables that you can then query using Athena
- joining data for data waresouse
	- clickstream data in RDS & customer data in S3
	- use Glue to join & enrich data, then load the results into Redshift
- create a centralized data catalog
	- manage metadata between different types of data stored in different place - creating a central repository with Glue

## Components
##### Data sources
- any data source that can be accessed via JDBC can be accessed by glue
##### Crawler
- Glue sets up a crawler that scans & finds important information (metadata) in connected data sources
- crawlers spin up their own servers, execute provided code, and spin them down (serverless)
- output can be stored in any other data store, or crawled againg & queried with athena
##### Data Catalog
- this metadata is stored in the **Data Catalog** - databases made up of tables that you can **query or run ETL jobs**
- **persistent metadata store**
	- store, annotate, and share metadata between AWS services (similar to Apache Hive metastore)
- **centralized repository**
	- only one data catalog per AWS region, providing a uniform repository so different systems can store and find metadata to query & transform that data
- **provided comprehensive audit**
	- track schema changes and data access control
	- ensure that data is not inappropriately modified or inadvertently shared
##### Jobs
- ETL jobs can be Scala or Python
	- write yourself or have Glue write it for you


![[Pasted image 20221010093038.png]]