[[Data Engineering]] [[postgres]] 
# How should we organize & manage our data?
-   Schemas: How should my data be logically organized?
-   Normalization: should my data have minimal dependency and redundancy?
-   Views: What joins will be done most often?
-   Access control: Should all users of the data have the same level of access?
-   DBMS: how do I pick between all the sql and noSQL options?
#### It all depends on the intended use of the data

# OLTP VS. OLAP
### OLTP - online transaction processing
-   Find the price of a book
-   Update the latest customer transaction
-   Keep track of employee hours

### OLAP - online analytical processing
-   Calculate books with best profit margin
-   Find most loyal customers
-   Decide on engineer of the month

## OLAP vs. OLTP
|           | **OLTP**                               | **OLAP**                                     |
| --------- | -------------------------------------- | -------------------------------------------- |
| *Purpose* | support daily transactions             | report & analyse data                        |
| *Design*  | application-oriented                   | subject-oriented                             |
| *Data*    | up-to-date, operational                | consolidated, historical                     |
| *Size*    | snapshot, gigabytes                    | archive, terabytes                           |
| *Queries* | simple transactions & frequent updates | complex, aggregate queries & limited updates |
| *Users*   | thousands                              | ==hundreds== (fewer)                         |


![[Pasted image 20211028200821.png]]

   

# Structuring and storing data
1.  Structured data
    1.  Follows a schema
    2.  Defined data types & relationships
    3.  Easier to analyze
    4.  Not as scalable, harder to maintain

2.  Unstructured data
1.  Schemaless
2.  Makes up most data in the world

3.  Semi-structured data
    1.  Does not follow larger schema
    2.  Self-describing structure (e.g. JSON)

-   Traditional DBs (OLTP)
    -   Real-time relational data

###  Data Warehouses (OLAP)
-   GOOGLE Big Query
-   Azure Synapse
-   Redshift
-   Archived structured data
-   Optimized for read-only analytics
-   Organized for reading/aggregating data
-   Contains data from multiple sources
-   Massively parallel processing (MPP)
-   Typically uses a denormalized schema and dimensional modeling

### Data Mart

-   Subset of data warehouses
-   Dedicated to a specific topic

-   Data Lake (big data)

-   All kinds of structures = flexibility and scalability
-   Azure BLOB storage
-   Store all types of data at a lower cost

-   Raw, operational, IOT, device logs, real-time, relational & non-relational

-   Retains all data and can take up to petabytes
-   Schema-on-read as opposed to schema-on-write
-   Need to catalog data, otherwise becomes a data swamp
-   Run big data analytics using services like Apache Spark and Hadoop
    -   Popular for deep learning and data discovery because activities require so much data
 ![[Pasted image 20211031203424.png]]
 
 # Database Design
 - determines how data is logically stored
     - How will it be read & updated?
 - uses database models: