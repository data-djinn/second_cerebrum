[[Data Engineering]] [[Spark Reader & Writer]] [[Python]] [[spark]]

- will be *slower* for small processing tasks
-  Core data structure is the ==**Resilient Distributed Dataset**== (RDD)
    -  low-level object that splits data across multiple nodes in the cluster
    -  However, RDDs are hard to work with directly, thus spark
-  Spark DF designed to behave like a SQL table
-  Create an instance of the SparkContext class, then create a `SparkSession`
 ```
 # Import SparkSession from pyspark.sql
from pyspark.sql import SparkSession

# Create my_spark (session, has an attribute catalog that lists all the data
my_spark = SparkSession.builder.getOrCreate()

# Print my_spark
print(my_spark)

# Print the tables in the catalog
print(spark.catalog.listTables())
```

```
# Don't change this query
query = "FROM flights SELECT * LIMIT 10"

# We already have a SparkContext called 'spark'
flights10 = spark.sql(query)

# Show the results
flights10.show()
```

