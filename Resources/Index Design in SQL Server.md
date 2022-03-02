[[SQL Server]] [[Improving SQL Server Query Performance]]

# Index basics
- index is like a book index: helps query engine locate information
  - sorted list of values & their corresponding pointers to the data page # where the values are located
  - index itself is stored on pages, which are also indexed (root page --> leaf pages)
- stored on-disk or in-memory associated with table or view
  - on-disk, keys are stored in tree tructure (B+ tree)
- speeds retrieval of rows from corresponding table/view
- contains keys built from one or more columns in the table/view
- stores data logically organized as a table, & physically stored as row-wise *rowstore* format (or also *columnstore*, potentially)
#### Tradeoff between query speed & update cost
- *narrow* indexes (few columns in index key) require less disk space and maintenance overhead
- *wide* indexes cover more queries
- can be added, modified or dropped at will - experiment to find efficiency!
- create a variety of indexes, and let the query optimizer decide for itself

#### Ideal is a column that has integer data type and are also unique/not null
# use [Filtered Indexes](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/create-filtered-indexes?view=sql-server-ver15) 
    ==use a filter predicate to index a portion of rows in the table==
- for columns that have a well-defined subset of data (e.g. date!)
#### use indexes as partition scheme across multiple filegroups

# Database Considerations
- large number of indexes on a table affect the performance of `INSERT, UPDATE, DELETE, MERGE` statements
  - they require the index to be adjusted to new DB state
  - avoid over-indexing heavily updated tables
  - keep indexes narrow (few columns)
- use many indexes to improve query performance on tables with low update requirements, but large volumes of data
  - large numbers of indexes can help the performance of queries that do not modify data, such as `SELECT`
- Indexing small tables may not be optimal, table scans are faster

#### indexes on views can provide significant performance gains when the view contains aggregations, table joins, or both
- a view does not need to be explicitly referenced in a query for the engine to use it
#### Use database engine tuning advisor to analyze your DB & make index recommendations

# Query Considerations
- write queries that insert or modify as many rows as possible in a single statement, instead of using multiple queries to update the same rows
  - optimizes index exploitation
- evaluate based on query type & how columns are used
  - e.g. a column used in an exact-match query would be a good candidate for an index
### Non-clustered
==contains the index key values and row locators that point to the storage location of the table data==
- create multiple non-clustered indexes on table or indexed view
- create on columns that are frequently used in predicates & join conditions in queries
  - avoid adding unnecessary columns
- Good for databases/tables with low update requirements
- covering indexes can improve query performance because all the data needed to meet the requirements of the query within the index itself
  - all non-SARGable columns are in the leaf pages - *the columns returned by the `SELECT, WHERE, JOIN` are all covered by the index*
    - much less I/O to execute, if the index is narrow compared to the rows & columns in the table itself
  - data rows of the underlying table are not sorted & stored in order of non-clustered index keys
  - leaf level of a non-clustered index is made up of index pages instead of data pages
![[Pasted image 20220202171601.png]]

use for queries that:
- use `join` or `group by`
- do not return large result sets
- contain columns frequently involved in search conditions

### Clustered
==sort and store the data rows in the table based on their key values==
- only one per table, because that's how the data ends up physically sorted
- *every table* should have a clustered index on the column(s) that:
  - can be used for frequently used queries
  - provide high degree of uniqueness
  - *automatically created when you use `PRIMARY KEY` constraint
  - can be used in range queries
- store on a different filegroup that is on a different disk to enable concurrent reads
![[Pasted image 20220202171515.png]]

# Column considerations
- 
- keep the length of the index key short for *clustered indexes
  - additionally, clustered indexes benefit from being created on unique/not null columns
- columns of type **ntext, text, image, varchar(max), nvarchar(max), and varbinary(max)** cannot be specified as index key column
  - (n)varchar, varbinary(max), & xml can participate in a non-clustered index as non-key index columns (see [Index with INCLUDE])
- **Unique index provides additional info for the query optimizer**
- examine data distribution in the column
  - often long-running queries are caused by indexing a column with few unique values, or by performing a join on such a column
  - columns that are used in `WHERE <`. `>`, `=`, or `BETWEEN`, or participate in a `JOIN` should be placed first in the index
    - additional columns should be ordered based on their level of distinctness (most distinct to least distinct)
  - TODO: learn abount indexes on computed columns
   
# Index considerations
- set initial storage characteristics by `SET FILLFACTOR`
- determine the index storage location by using filegroups or partition schemes to optimize performance

## Filegroups/Partition Schemes
- by default, the indexes are stored in the same filegroup as the base table on which the index is created
- nonpartitioned clustered index and the base table always reside in the same filegroup
- **Create nunclustered indexes on a filegroup other than the filegroup of the base table or clustered index**
- **Partition clustered and non-clustered indexes to span multiple filegroups**
- **move a table from one filegroup to another by dropping the clustered index and specifying a new filegroup or partition scheme in `MOVE TO` clause of the `DROP INDEX` statement, or by `CREATE INDEX` w/ `DROP INDEX` clause
- by creating the nonclustered index on a different filegroup, you can achieve performance gains if the filegroups are using different physical drives with their own controllers
### Partitions across multiple Filegroups
- Partitioned indexes are partitioned horizontally, row by row, based on a partition function
  - partition function defines how each row is mapped to a set of partitions based on the values of certain columns, called partitioning columns
  - partition schemes specifies the mapping of the partitions to a set of filegroups
  - **provides scalable systems that make large indexes more manageable** (used in OLTP systems
  - **queries run faster**: individual partitions are processed concurrently, excluding those not needed for query

## Index sort order
- index key column should match those in `ORDER BY` clause expected from the query
  - this removes the need for a SORT operator in the query plan
  - DB engine can move efficiently in either direction, so ASC/DESC doesn't matter as much

