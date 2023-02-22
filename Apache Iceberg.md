[[Data Engineering]]


## Table format
- a way to organize a dataset's files to present them as a single table
![[Pasted image 20230220135510.png]]

#### Apache hive
- **problem**: your data is spead across object storage across 1000's of partitioned parquet files
- **solution**: Apache hive solves this with directories (partitions = subfolders)
	- tables contents are all filies in the table's directories
	- old defacto standard
##### Pros
- works with basically every engine
- more efficient access patterns than full-table scans for every query
- file-format agnosting
- **Atomic transactions**
	- atomically update a whole partitions
- single, centralized metadata to anwer "what data is in this table" for the whole ecosytem

##### Cons
- smaller updates are very inefficient
- no way to change data in multiple partitions safely
- multiple jobs modfifing the same dataset is not so safe
- all the directory listings needed for large tables take a long time
- users have to know the physical layout of the table (partitions)
- hive table statistics are often stale

## ICEBERG (NEW PARADIGM)
![[Pasted image 20230221162034.png]]
- table format specification (standard)
- A set of APIs and librarios for interaction with that specification

it is **NOT** a storage engine, nor an execution engine/service

### Benefits
- efficiently make small updates
	- make changes at the file level (not the file level)
- snapshot isolation for transactions
	- reads and writes don't interfere with each other and all writes are atomic
	- concurrent writes
- faster planning and execution
	- list of files defined on the write side
	- read can then read those to optimize their queries
	- column stats in manifest files used to narrow down files
- Reliable metrics for Cost-Based Optimizers (vs hive)
	- done on write instead of infrequent expensive read job
- Abstract the physical, expose a logical view
	- hidden partitioning - simply query the table :)
	- compaction
	- table schemas & partitions can evolve over time
		- expiriment with table layout
	- all engines see changes immediately

# Iceberg Data Lakehouse
#### Status Quo
![[Pasted image 20230221164750.png]]
- data warehouse is expensive (snowflake, etc)
- proprietary components cause vendor lock-in, tool lock-out
- data drift from marts/cubes/extracts

### Data Lakehouse architecture
![[Pasted image 20230221165206.png]]
- inexpensive
-  enables data warehouse performance & features on the data lake
- open formats let you use the tools of today
![[Pasted image 20230221170903.png]]

# Iceberg architecture
![[Pasted image 20230221184503.png]]
3 layers of metadata:
1. **metadata files** define the table
2. **manifest lists** define a snapshot of the table, with a list of manifests that make up the snapshot and metadata about their data
3. **manifests** are a list of data files along with metadata on those data files for file pruning

### Iceberg Catalog
"phonebook"
- a store that houses the current metadata pointer for iceberg tables
- must support the atomic operations for updating the current metadata pointer (Nessie etc)
	- maps table name to the location of the current generation metadata file


### Metadata file
- stores metadata about a table at a certain point in time