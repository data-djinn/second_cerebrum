[[Spark Reader & Writer]] [[Data Engineering]]

# Spark SQL
- module used for structured data processing
    - can use SQL queries
    - can interact with Spark SQL using DataFrame API, enabling Python, Scala, Java, and R
   ```
   SELECT id, results
   FROM exams
   WHERE result > 70
   ORDER BY result
   
   ## Same as:
   
   spark.table("exams")
    .select("id","result")
    .where("result > 70")
    .orderBy("result")
   ```
   
   ### Spark SQL executes all queries on the same engine
   
   ![[Pasted image 20211117200724.png]]
   
   ## Spark SQL optimizes queries before execution
   
   ![[Pasted image 20211117200804.png]]
   
   ## Filtering task
   **Analogy** Filtering out brown M&Ms from 100 bags in 60 seconds - get 99 other people to help!
   
  # Spark architecture concepts
  - Executor
          - instructor
- Driver
        - the table where the students are working
- Cluster
        - Collectively, the instructor, students, and their respective resources
- Slots/Threads/Cores
        - students
- Partitions
        - small bags of candies
- Dataset
        - larger bag of candies
- Job
        - the request to eliminate all the brown candies & place the remaining candies on the table
- Tasks
        - specific instructions assigned to a single student to process a specific bag of candies


![[Pasted image 20211117201501.png]]

![[Pasted image 20211117201512.png]]

![[Pasted image 20211117201522.png]]

![[Pasted image 20211117201556.png]]
- partitions are never sub-divided

## ==Task:== a single unit of work against a single partition of data

## ==Job:== Consists of one or more stages

## ==Application:== Consists of zero or more jobs

## ==Stage:== consists of one or more tasks


# Clusters & nodes
- a cluster is a group of nodes
- nodes are the individual machines within a cluster
    - with databricks, the driver (a JVM) and each executor (each a JVM) all run in their own nodes

# Driver
- Runs the spark application
- assigns tasks to slots in an executor
- coordinates the work between tasks
- Receives the results, if any


# Executor
- provides an environment in which tasks can be run
- Leverages the JVM to execute many threads
        - one per node

# Slots/Cores/Threads
- in Databricks, 1-to-1 (terms can be used interchangeably)
        - Slot is the lowest level of parallelization
        - generally interchangeable terms, but "slot" is the most accurate
        - executes a set of transformations against a partition as directed by the driver

## Parallelization
- scale horizontally by adding more executors
- scale vertically by adding cores to each executor

## Partitions
- a ~128MB chunk of the larger dataset
- each task processes 1 and only 1 partition
- the size and record splits are decided by the driver
- the initial size is partially adjustable with various configunation options

## Applications, jobs, stages, and tasks
- hierarchy into which work is subdivided
- one spark action results in one or more jobs
- the number of stages depends on the operations submitted with the application
- tasks are the smallest unit of work

# General Notes
- executors share machine level resources
- tasks share executor (JVM) resources
- rarely are significant performance improvements made by tweaking Spark config


## Stages
- a stage cannot be completed until all tasks are completed
- Spark 3.x can run some stages in parallel (e.g. inputs to a join)
- one long-running task can delay an entire stage from completing
- ==the shuffle between two stages is one of the most expensive operations in apache spark==
        - *Wide* operations require shuffling (more than 1 stage)
        - assist the optimizer by executing multiple wide transformations back-to-back

##### Shuffling is the process of rearranging data within a cluster between stages
- triggered by "wide" operations:
        - **re-partitioning
        - byKey operations
        - Joins (worst is cross-joins)
        - Sorting
        - Distinct
        - GroupBy
- Try to group wide trasnformations together for optimization
        - narrow --> narrow --> wide --> wide --> wide --> narrow

- one of the most significant, yet unavoidable, cause of performance degradaition
- Just because it's slow, doesn't mean it's bad (don't polish a cannon ball)
