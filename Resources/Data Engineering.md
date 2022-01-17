# Introduction to Data Engineering
Data Engineer makes data analysts & scientists lives easier

-   Gather data from different sources
-   Optimize data for analyses
-   Remove corrupt data

*develops, constructs, tests, and maintains architectures such as databases and large-scale processing systems*

-   Processing large amounts of data
-   Using clusters of machines

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

### Scheduling
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

## PySpark
```
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

