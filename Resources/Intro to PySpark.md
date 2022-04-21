[[Data Engineering]] [[Spark Reader & Writer]] [[Python]] [[spark]]
# What is Spark?
- ==platform for cluster computing==
- lets you spread data and computation over **clusters**, which are made up of **nodes** (each node is like a separate computer)
  - splitting up your data makes it easier to work with very large datasets, because each node only works with a small amount of data
    - as each node works on its own subset of the data, it carries out a part of the total calculations required
    - both data processing and computation are performed **in parallel** over the nodes in the cluster
##### ask yourself:
- *is my data too big to work with on a single machine?*
- *can my calculations be easily parallelized?*

#### First, connect to a spark cluster
  - typically hosted on a remote machine that's connected to all other nodes
  - one computer (**master**) that manages splitting up the data and the computation
  - the master node is connected to the rest of the computers in the cluster
    - they send their computational results back to the master node
  - more simple to run a cluster locally
  - **create an instance of pyspark's `SparkContext()` class**
    - class constructor takes a few optional arguments that allow you to specify the attributes of the cluster you're connecting to (see [docs)](https://spark.apache.org/docs/latest/) for details on connection parameters)
#### Using DataFrames
- spark will be **slower & more expensive**  for small processing tasks
-  Core data structure is the ==**Resilient Distributed Dataset**== (RDD)
    -  low-level object that splits data across multiple nodes in the cluster
    -  However, RDDs are hard to work with directly, thus spark
-  Spark DF designed to behave like a SQL table
-  Create an instance of the `SparkContext()` class, then create a `SparkSession`
  -  SparkSession is your interface with the SparkContext connection

### Connect & list tables
 ```
 # Import SparkSession from pyspark.sql
from pyspark.sql import SparkSession

# Create my_spark session, has an attribute catalog that lists all the data
my_spark = SparkSession.builder.getOrCreate()

# Print my_spark
print(my_spark)

# Print the tables in the catalog
print(spark.catalog.listTables())
```
### Execute sql against RDD
```
# Don't change this query
query = "FROM flights SELECT * LIMIT 10"

# We already have a SparkContext called 'spark'
flights10 = spark.sql(query)

# Show the results
flights10.show()
```
- returns a Spark DataFrame
### Convert Spark DataFrame -> pandas DataFrame
```
flight_counts = spark.sql(query)

# Convert the results to a pandas DataFrame
pd_counts = flight_counts.toPandas()

# Print the head of pd_counts
print(pd_counts.head())
```

### covert pd DF -> Spark DF
```
pd_temp = pd.DataFrame(np.random.random(10))

# Create spark_temp from pd_temp
spark_temp = spark.createDataFrame(pd_temp)

# Examine the tables in the catalog
print(spark.catalog.listTables())
----------------------------------
[]
---------------------------------
# Add spark_temp to the catalog
spark_temp.createOrReplaceTempView("temp")

# Examine the tables in the catalog again
print(spark.catalog.listTables())
---------------------------------
[Table(name='temp', database=None, description=None, tableType='TEMPORARY', isTemporary=True)]
```
- note that the output of the `createDataFrame()` method is stored locally, not in the `SparkSession` catalog
  - meaning, you can use all Spark DF methods on it, but you can't access the data in other contexts (e.g. sql queries can't reference it)
  - to access the data in other contexts, you have to **save it as a temporary table with `.createTempView('view_name')`** 
    - can also use `.createOrReplaceTempView()`,  just like SQL
![[Pasted image 20220303101503.png]]

### read csv directly into spark DF
`airports = spark.read.csv(file_path, header=True)`

# Data manipulation
## Create columns with: `df.withColumn('new_col_name', col_expression)`
- Spark DataFrame is *immutable*
## Filter columns
- with `df.filter('distance > 10')`
  - spark counterpart of SQL `WHERE` clause
-  or  `df.filter(df.distance > 10)`
  - pass a boolean column
- or pre-defined filter:
```
filterA = df.distance == 10
filterB = df.destination = 'NYC'
filtered_df = df.filter(filterA).filter(filterB)
```
## Select columns with
- `df.select('col1', 'col2')`
- `df.select(df.col1, df.col2)`
note that `.withColumn` returns all columns, and `.select()` returns only the columns you specify
```
# Define avg_speed
avg_speed = (flights.distanc/(flights.air_time/60)).alias("avg_speed")

# Select the correct columns
speed1 = flights.select("origin", "dest", "tailnum", avg_speed)

# Create the same table using a SQL expression
speed2 = flights.selectExpr("origin", "dest", "tailnum", "distance/(air_time/60) as avg_speed")
```

# Grouping & Aggregating
- `min()`
- `max()`
- `count()`
**are all `GroupedData` methods**
created by calling the `df.groupBy()` dataframe method: `df.groupBy().min("cal").show()`
  - this creates a `GroupedData` object (so you can use the `.min()` method)
  - then finds the minimum value in `col`, ond returns it as a DataFrame
```
# Find the shortest flight from PDX in terms of distance
flights.filter(flights.origin == 'PDX').groupBy().min('distance').show()

# Find the longest flight from SEA in terms of air time
flights.filter(flights.origin == 'SEA').groupBy().max('air_time').show()
```
- note how all the values are abbreviations - one or two chars max
- using small codes can help save RAM and compute power - especially because Spark works with **big** data
- PySpark has a whole class devoted to grouped data frames: `pyspark.sql.GroupedData`

## `df.agg()` method
==lets you pass an aggregate column expression that uses any of the aggregate functions from the `pyspark.sql.functions` submodule==
- `pyspark.sql.functions` module contains many useful funcs, such as  `.stddev()` 
```
# Import pyspark.sql.functions as F
import pyspark.sql.functions as F

# Group by month and dest
by_month_dest = flights.groupBy('month','dest')

# Average departure delay by month and destination
by_month_dest.avg('dep_delay').show()

# Standard deviation of departure delay
by_month_dest.agg(F.stddev('dep_delay')).show()
```

# Joins: `left_df.join(right_df, on="col", how='leftouter')`
# ML pipelines: `pyspark.ml`
#### `Transformer` classes
- `df.transform()` method that returns a new DF
    - e.g., create a `Bucketizer` class to create discrete bins from a continuous feature
      - or, a `PCA` class to reduce the dimensionality of your dataset using principle component analysis
#### `Estimator` classes
- implement a `df.fit()` method that returns a model object
    - e.g. a `StringIndexerModel` to include categorical data saved as strings in your models
      - or, a `RandomForestModel` that uses the random forest algorithm for classification or regression

# Data Types
### For ML, Spark  only handles numeric data
**all columns in your df must be either integers or decimals ('doubles')**