[[Spark Reader & Writer]] [[Data Engineering]]

![[Pasted image 20211117175941.png]]

- spark uses clusters to process big data by breaking a large task into smaller ones and distributing the work among several machines
    - the secret to Spark's performance is parallellism
    - each parallelized action is referred to as a job
    - each job is broken down into stages, which is a set of ordered steps that, together, accomplish a job
    - tasks are created by the driver and assigned a partition of data to process
        - *smallest unit of work*

![[Pasted image 20211117191735.png]]

![[Pasted image 20211117191747.png]]
![[Pasted image 20231001151138.png]]
# Driver
- machine in which the application runs
- responsible for:
	- instantiating a `SparkSession`, through which the executors and cluster manager are accessed 
	- requesting resources (CPU, memory, etc.) from the cluster manager for Spark's executors (JVMs)
		- once resources are allocated
        - analyzing, distributing, and scheduling work across the executors via DAGs
        - maintaining information about Spark application
    - only 1 driver per cluster
### SparkSession
- unified conduit to all Spark operations & data
	- subsumes previous entry points like `SparkContext`, `SQLContext`, `HiveContext`, `SparkConf`, and `StreamingContext` (these are still maintained for backwards compatibility)
- creates JVM runtime parameters, defines DataFrames & Datasets, reads from data sources, accesses catalog metadata, and issues Spark SQL queries

# Cluster Manager
- manages & allocates resources for the cluster of nodes on which your Spark application runs
- 4 types supported:
	- built-in (stand-alone)
	- Hadoop
	- YARN
	- Mesos
	- Kubernetes

# Worker
- worker node fixes the executor process
- fixed number of executors allocated at any point in time
	- typically one executor per worker node

# Executor
- Holds a chunk (partition) of the data to be processed
    - collection of rows that sits on one physical machine in the cluster
- responsible for carrying out the work assigned by the driver
- each executor is responsible for two things:
    - execute code assigned by the driver
    - report the state of the computation back to the driver

# Core
- Spark parallelizes at two levels
    - splitting work among executors
    - splitting work among cores
- each executor has multiple cores
- each core can be assigned a task

# Distributed data & partitions
- physical data is distributed across storage as partitions residing in either HDFS or cloud storage
- spark treats each partition as a high-level logical data abstraction (an in-memory DataFrame)
![[Pasted image 20231001162102.png]]
- partitioning allows for efficient parallelism
- a distributed scheme of breaking up data into chunks or partitions allows Spark executors to process only data that is close to them, *minimizing network bandwidth*
- each executor's core is assigned to it's own partition to work on
![[Pasted image 20231001164509.png]]

# 
# Workspace
- environment for accessing all of your databricks assets
- organizes objects (notebooks, libraries, and experiments) into folders, provides access to data, and provides access to computational resources, such as clusters and jobs

![[Pasted image 20211117194537.png]]


