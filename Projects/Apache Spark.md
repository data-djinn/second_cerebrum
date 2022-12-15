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

# Driver
- machine in which the application runs
- responsible for:
    - maintaining information about Spark application
    - responsible for:
        - analyzing, distributing, and scheduling work across the executors
    - only 1 driver per cluster

# Worker
- worker node fixes the executor process
- fixed number of executors allocated at any point in time

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

# Workspace
- environment for accessing all of your databricks assets
- organizes objects (notebooks, libraries, and experiments) into folders, provides access to data, and provides access to computational resources, such as clusters and jobs

![[Pasted image 20211117194537.png]]


