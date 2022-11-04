# Introduction to Data Engineering
==Data engineering: taking any action involving data and turning it into a reliable, repeatable, and maintainable process==
-   Gather data from different sources
-   Optimize data for analyses (prep)
-   Remove corrupt data (prep)
-   explore & visualize (analysis)
-   experimentation & prediction (data science)

*develops, constructs, tests, and maintains architectures such as databases and large-scale processing systems*
#### Data engineer's responsibility:
- data engineers deliver:
  - the correct data
  - in the right form
  - to the right people
  - as efficiently as possible
- ingest data from different sources
- optimize databases for analysis
- remove corrupted data
- develop, construct, test, and maintain data architectures
-  Process large amounts of data using clusters of machines

  #### Big data
  - big data becomes the norm --> data engineers are more and more needed
  - big data:
    - so large you have to think about how to deal with its size
    - traditional methods don't work anymore
![[Pasted image 20220302150836.png]]
**5 Vs**
- volume (how much?)
- variety (what kind?)
- velocity (how frequent?)
- veracity (how accurate?)
- value (how useful?)


| Data Engineer                            | Data Scientist             |
| ---------------------------------------- | -------------------------- |
| Develop Scalable data architecture       | Mining data for patterns   |
| Streamline data acquisition              | statistical modeling       |
| set up processses to bring together data | predictive models using ML |
| clean corrupt data                       | monitor business processes |
| well versed in cloud technology          | clean outliers in data     |

## Tools of the data engineer
###   Databases
-   Holds large amounts of data
-   Support applications
-   Other DBs are used for analyses

###  Processing
-   Clean data
-   Aggregate data
-   Join data

![[Pasted image 20211023100653.png]]

### Scheduling [[Apache Airflow]]
- plan jobs with specific intervals
- resolve dependency requirements of jobs

### What are databases?
- a large collection of data organized especially for rapid search and retrieval
    - holds data
    - organizes data
    - retrieve/search data through DBMS

| Databases                                 | File systems                     |
| ----------------------------------------- | -------------------------------- |
| very organized                            | less organized                   |
| Functionality like search, replicate, etc | simple, less added functionality |

### Parallel computing
- basis of modern data processing tools
    - memory
    - processing power
- split tasks into subtasks
- distribute subtasks over several computers
![[Pasted image 20211023101618.png]]

- Task needs to be large
- need several processing units
![[Pasted image 20211023101717.png]]

## PySpark [[Intro to PySpark]]
```python
# Print the type of athlete_events_spark
print(type(athlete_events_spark))

# Print the schema of athlete_events_spark
print(athlete_events_spark.printSchema())

# Group by the Year, and find the mean Age
print(athlete_events_spark.groupBy('Year').mean('Age'))

# Group by the Year, and find the mean Age
print(athlete_events_spark.groupBy('Year').mean('Age').show())
```

![[Pasted image 20211023101858.png]]
^`show()`

![[Pasted image 20211023101909.png]]

