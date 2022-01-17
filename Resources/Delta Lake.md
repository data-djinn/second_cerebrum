[[spark]] [[Data Engineering]] [[Cloud]]

![[Pasted image 20211122151206.png]]

##### *Delta lake* is an ==open format storage layer that puts standards in place in an organization's data lake to provide data structure & governance==
- these standards ensure that all the data stored in a data lake is reliable & ready to use for business needs

### Common problems with data lakes:
- poor BI support
- complex to set up
- poor performance
- unreliable data swaps

##### Data lakes are popular choices for data storage due to their inherent flexibility over data warehouses
- besides storing all types of data (structured, unstructured, and semi-structured), they also store data relatively cheaply compared to data warehouses
- crucial to organizations running big data and analytics projects because unstructured data like videos and audio files lend themselves nicely to AI analytics techniques like machine learning
- plus, economic data storage allows organizations to keep data for longer periods of time until they decide what the best use cases for that data are, without incurring hefty costs

##### This flexibility is also one of their biggest shortcomings
- because they store all data types for long periods, data lakes can quickly become data swamps - difficult to navigate & manage
- **delta lake was designed to bring the governance & structure of data warehouses into data lakes to ensure the data in an org's data lake is reliable for use in big data & AI projects**

![[Pasted image 20211122134615.png]]

![[Pasted image 20211122134749.png]]

## Bronze
- raw data ingested from various sources
- often kept for years "as-is"

## Silver
- more refined view of an org's data 
- directly queryable and ready for big data & AI projects
- often joined from various bronze tables to enrich or update records based on recent activity
- clean, normalized
- referred to as your **Single Source of Truth**

## Gold tables
- provide business-level aggregates used for particular use-cases
- usually related to one particular business objective


# Elements of Delta Lake
## Delta Files
- delta lake uses parquet files to store in object storage
    - state-of-the-art file format for keeping tabular data
    - faster and more powerful than traditional methods for storing tabular data because they store data using columns instead of rows
        - enables you to skip over non-relevant columns quickly
        - thus queries take less time to execute
- **Delta files leverage all of the technical capabilities of parquet, but have an additional layer on top**
    - this additional layer:
        - tracks data versioning & metadata
        - stores transaction logs to keep track of changes made to table or object storage directory
        - provides ACID transactions
    
## Delta Tables
consist of:
1. delta files containing the data & kept in object storage
2. delta table registered in a metastore (catalogue that tracks your data's metadata)
3. delta transaction log saved with delta files in object storage

## Delta Optimization Engine
- high-performance query engine that provides an efficient way to process data in data lakes
- delta engine accelerates data lake operations and supports a variety of workloads ranging from large-scale ETL processing to ad-hoc, interactive queries
- many optimizations take place automatically

## Delta Lake Storage Layer
- org keeps all its data in object storage
    - lower cost
    - easily scalable

# Data reliability
## challenges with data lakes:
- **Data can be hard to append**
    - mulitiude of data sources necesitates writing to dozens or hundreds of files before a transaction completes
    - query might capture a partially-written dataset, which can lead to invalid or incorrect result
- **Jobs often fail midway**
    - if a multi-step update fails, it's difficult to recover the correct state of the data
    - often costs countless hours of engineer time recovering data to a valid state
- **Modification of existing data is difficult**
    - data lakes were designed to be written once, read many times
    - regulations like GDPR & CCPA now require orgs to identify & delete a user's data if they request it
- **Real-time data is difficult to leverage**
    - many orgs struggle to implement solutions that allow them to leverage real-time solutions along with historical records
    - keeping multiple systems & pipelines in sync can be expensive, and it's difficult to solve consistency & concurrency problems
- **Keeping historical versions of data is costly**
    - some industries have strict regulations for model & data reproducability that require historical versions of data to be maintained
    - these archives can be expensive & difficult to maintain
- **it is difficult to handle large metadata**
    - for orgs with truly massive data, reading metadata can often create substantial delays in processing & overhead costs
- **Too many files cause problems** 
    - some applications regularly write small files out to data lakes
    - over time, millions of files build up
    - query performance suffers greatly when a small amount of data is spread over too many files
- **It's hard to get great performance**
- **Data quality issues affect analysis results**
    - because data lakes don't have the built-in quality checks of a data warehouse, queries against data lakes need to constantly be assessing data quality and anticipating possible issues


## Delta Lake addresses these pain points by:
#### ACID Transaction
- **ACID Transactions either complete successfully or fail fully**
        - atomicity
        - consistency
        - isolation
        - durability
    - Delta Lake's ACID transactional guarantees eliminate the motivations for having data lake & data warehouse in the same architecture

#### Schema Management
- **delta lake gives you the ability to specify & enforce your data schema**
    - automatically validates that the schema of the data being written is compatible with the schema of the table it is being written into
    - columns present in the table but not in the data are set to null
    - throws an exception if there are extra columns in the data 
        - ensures bad data that could corrupt your system is not written into it
    - enables you to make changes to a table's schema that can be applied automatically

#### Scalable Metadata Handling
- big data is very large in size, and its metadata can be large as well
- with delta lake, metadata is processed with distributed processing - just like regular data

#### Unified Batch & Streaming Data
- delta lake is designed from the ground up to allow a single system to support both batch & stream processing of data
- transactional guarantees of delta Lake mean that each micro-batch transaction creates a new version of a table that is instantly available for insights
- many databricks users use delta lake to transform the update frequency of their dashboards & reports from days to minutes while eliminating the need for multiple systems

#### Data versioning & Time Travel
- transaction logs used to ensure ACID compliance create an auditable history of every version of the table, indicating which files have changed between versions
- this log makes it easy to retain historical versions of the data to fulfill compliance requirements in various industries (e.g. GDPR & CCPA)
- these logs also include metadata like extended statistics about the files in the table & the data in the files
- spark is used to scan transaction logs, making it very performant