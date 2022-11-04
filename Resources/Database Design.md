[[Data Engineering]] [[SQL Server]] [[DBA]] [[postgres]]
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

```sql
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

# Normalization
- divides tables into smaller tables & connects them via relationships
- **Goal**: reduce redundancy & increase data integrity
##### Identif repeating groups of data and create new tables for them

# Database Roles
- Manage database access permissions
- A DB Role is an entity that contains information that:
    - Define the role's privileges
    - Interact with the client authentication system
- Roles can be assigned to one or more users
- Roles are global across a database cluster installation
- Create a role
```sql
○ CREATE ROLE data_analyst WITH PASSWORD 'password' VALID UNTIL '2020-01-01'
○ CREATE ROLE admin CREATEDB;
○ GRANT UPDATE ON ratings TO data_analyst;
○ REVOKE UPDATE ON ratings FROM data_analyst;
```
- Available priveleges in PostgreSQL are:
```sql
SELECT, INSERT, UPDATE, DELETE, TRUNCATE, REFERENCES, TRIGGER, CREATE, CONNECT, TEMPORARY, EXECUTE, and USAGE
```
- Users and groups are both roles

### Benefits of roles:
- Roles live on after users are deleted
- Roles can be created before user accounts
- Save DBAs time
### Pitfalls
- Sometimes a role gives a specific user too much access
  - Need to pay attention
```sql
-- Add Marta to the data scientist group
GRANT data_scientist TO marta;
-- Celebrate! You hired data scientists.
-- Remove Marta from the data scientist group
REVOKE data_scientist FROM marta;
```

# Table partitioning

Why partition?
	- Tables grow (100s Gb/Tb)
	- Problem: queries/updates become slower
	- Because: e.g., indices don't fit memory
	- Solution: split table into smaller parts = partitioning
		○ Physical data model

## Vertical partitioning
![[Pasted image 20220320155725.png]]

## Horizontal Partitioning
![[Pasted image 20220320155735.png]]
![[Pasted image 20220320155739.png]]
Can partition by  LIST (columns) instead of range

Pros:
	- Indices of heavily-used partitions fit in memory
	- Move to specific medium: slower vs. faster
	- Used for both OLAP and OLTP

Cons:
	- Partitioning existing table can be a hassle
	- Some constraints can not be set

Related to **sharding**: partitioning over several machines

```sql
-- Create a new table called film_descriptions
CREATE TABLE film_descriptions (
    film_id INT,
    long_description TEXT
);
-- Copy the descriptions from the film table
INSERT INTO film_descriptions
SELECT film_id, long_description FROM film;
    
-- Drop the descriptions from the original table
ALTER TABLE film
DROP COLUMN long_description;
-- Join to view the original table
SELECT * FROM film_descriptions
JOIN film ON film_descriptions.film_id = film.film_id;

-- Create a new table called film_partitioned
CREATE TABLE film_partitioned (
  film_id INT,
  title TEXT NOT NULL,
  release_year TEXT
)
PARTITION BY LIST (release_year);
-- Create the partitions for 2019, 2018, and 2017
CREATE TABLE film_2019
  PARTITION OF film_partitioned FOR VALUES IN ('2019');
CREATE TABLE film_2018
  PARTITION OF film_partitioned FOR VALUES IN ('2018');
CREATE TABLE film_2017
  PARTITION OF film_partitioned FOR VALUES IN ('2017');
-- Insert the data into film_partitioned
INSERT INTO film_partitioned
SELECT film_id, title, release_year FROM film;
-- View film_partitioned
SELECT * FROM film_partitioned;
```