[[Data Engineering]] [[SQL Server]] [[DBA]]
## What is database design?
- determine how data is logically stored
  - how will it be read & updated?
- uses DB models: high-level specifications for DB structure
  - relational, NoSQL, object-oriented (document), network/graph model
- Uses **schemas**: blueprint of the database
  - defines tables, fields, relationships, indexes, and views
  - when inserting data in relational databases, schemas must be respected

## how should we organize & manage our data?
- schemas: *how should my data be logically organized?*
- Normalization: *should my data have minimal dependency & redundancy?*
- Views: *what joins will be done most often?*
- Access control: *should all users of the data have the same level of access?*
- DBMS: *how do I choose between SQL & noSQL options?*
**all depend on intended use of the data**

# Data Modeling
### Conceptual data model
==Describe entities, relationships, attributes (in the abstract) - gather business requirements==
**Tools:**
- data structure diagrams, e.g. entity-relational diagrams & UML diagrams
### Logical data model
==defines tables, columns, relationships (in SQL) - relational model==
**Tools:**
- database models & schemas, e.g. relational model & star/galaxy schema
- Other tools exist, e.g. dbt, git,TopCoat,
### Physical data model
==describes physical storage==
**Tools:**
- partitions, CPUs, indexes, backup systems, and tablespaces
![[Pasted image 20220128095935.png]]

# OLTP vs. OLAP:
## Online Transaction Processing
- find the price of a book
- update the latest customer transaction
- keep track of employee hours
## Online Analytical Processing
- calculate books with best profit margin
- find most lucrative customers
- decide the salesperson of the month

|           | OLTP                                  | OLAP                                         |
| --------- | ------------------------------------- | -------------------------------------------- |
| *Purpose* | support daily transactions            | report & analyze data                        |
| *Design*  | application-oriented                  | subject-oriented                             |
| *Data*    | up-to-date, operational               | consolidated, historical                     |
| *Size*    | snapshot, gigabytes                   | archive, terabytes                           |
| *Queries* | simple transactions, frequent updates | complex, aggregate queries & limited updates |
| *Users*   | thousands                             | hundreds                                     |

![[Pasted image 20220128094251.png]]

# Data Structure & Storage
## Structured data
- follows a schema
- defined data types & relationships
- easier to analyze
- not as scalable - harder to maintain
## Unstructured Data
- schemaless
- makes up most data in the world
## Semi-structured data
- does not follow larger schema
- self-describing structure (e.g. JSON)

#### Traditional DBs (OLTP)
- real-time relational data
#### Data Warehouse (OLAP)
- Redshift, Snowflake, Synapse, BigQuery
- archived structured data
- optimized for read-only analytics
- organized for reading/aggregating data
- contains data from multiple sources
- Massively parallel processing (MPP)
- Typically uses a denormalized schema & dimensional modeling
- **Data mart**
  - subset of data warehouse
  - dedicated to a specific topic
#### Data Lake
- all kinds of data structures = flexibility & scalability
- Azure BLOB, AWS S3
- Store all kinds of data *at a lower cost*
  - raw, operational, IOT, device logs, real-time, relational, non-relational
- retains all data & can take up to *petabytes*
- schema-on-read as opposed to schema-on-write
- need a data catalog, else it will become a "data swamp"
- run big data analytics using services like apache spark & hadoop
  - popular for deep learning & data discovery, other activities that require a LOT of data

![[Pasted image 20220128095057.png]]

# Dimensional Modeling
- adapting relational model for Data Warehouse design
  - optimized for OLAP queries: aggregate data, not updating (OLTP)
  - Built using star schema
  - easy to interpret and extend schema
## Fact tables
  - decided by business use-case
  - holds records of a metric
  - changes regularly
  - connects to dimensions via foreign keys
## Dimension tables
- holds description of attributes
- does not change as often
![[Pasted image 20220128101601.png]]

```
-- DIMENSION TABLE EXAMPLE
CREATE TABLE week(
  week_id INTEGER PRIMARY KEY
  ,week INTEGER NOT NULL
  ,month VARCHAR(160) NOT NULL
  ,Year INTEGER NOT NULL
);
```

#### Star schema: one dimension
#### Snowflake Schema: more than one dimension
