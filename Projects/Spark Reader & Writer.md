[[spark]] [[Data Engineering]]
# Read & Write From Data sources
-   .csv
## Parquet
-   Columnar storage format that provides compressed, efficient columnar representation
-   Unlike .csv, allows you to load in only the columns you need (since the values for a single record aren't stored together
-   Available to any project in the hadoop ecosystem, regardless of the data processing framework, data model, or language
-   Schema is stored at the bottom of the file (don't need to infer schema)
-   Doesn't waste space storing missing values
-   "Predicate pushdown" pushes filters down to the source
-   "Data skipping" available - stores the min & max of each column so you don't always have to read the entire file
-   Harder to corrupt because they're not as easy to open
-   Inferring a schema affects performance
    -   declare schema up front when able
    -   required for streaming data

## Delta Lake
- technology designed to be used with Apache Spark to build robust data lakes
- runs on top of ex
    - provides:
        -  ACID transactions
        - scalable metadata handling
        - unified streaming & batch processing

#### tab separator, use first line as header, infer schema: (25 seconds!)
```
usersCsvPath = "/mnt/training/ecommerce/users/users-500k.csv"

usersDF = (spark.read
  .option("sep", "\t")
  .option("header", True)
  .option("inferSchema", True)
  .csv(usersCsvPath))

usersDF.printSchema()

--------------------------------------------
root 
|-- user_id: string (nullable = true) 
|-- user_first_touch_timestamp: long (nullable = true) 
|-- email: string (nullable = true)
```

#### Manually define schema by creating a `StructType` with column names & data types: (1 second!)
```
from pyspark.sql.types import LongType, StringType, StructType, StructField

userDefinedSchema = StructType([
  StructField("user_id", StringType(), True),  
  StructField("user_first_touch_timestamp", LongType(), True),
  StructField("email", StringType(), True)
])

usersDF = (spark.read
  .option("sep", "\t")
  .option("header", True)
  .schema(userDefinedSchema)
  .csv(usersCsvPath))
  
# alternate DDL schema:

DDLSchema = "user_id string, user_first_touch_timestamp long, email string"
```

#### Read from JSON with DataFrameReader's `json` method and the infer schema option: (16 seconds)
```
eventsJsonPath = "/mnt/training/ecommerce/events/events-500k.json"

eventsDF = (spark.read
  .option("inferSchema", True)
  .json(eventsJsonPath))

eventsDF.printSchema()

------------------------------------------
root
 |-- device: string (nullable = true)
 |-- ecommerce: struct (nullable = true)
 |    |-- purchase_revenue_in_usd: double (nullable = true)
 |    |-- total_item_quantity: long (nullable = true)
 |    |-- unique_items: long (nullable = true)
 |-- event_name: string (nullable = true)
 |-- event_previous_timestamp: long (nullable = true)
 |-- event_timestamp: long (nullable = true)
 |-- geo: struct (nullable = true)
 |    |-- city: string (nullable = true)
 |    |-- state: string (nullable = true)
 |-- items: array (nullable = true)
 |    |-- element: struct (containsNull = true)
 |    |    |-- coupon: string (nullable = true)
 |    |    |-- item_id: string (nullable = true)
 |    |    |-- item_name: string (nullable = true)
 |    |    |-- item_revenue_in_usd: double (nullable = true)
 |    |    |-- price_in_usd: double (nullable = true)
 |    |    |-- quantity: long (nullable = true)
 |-- traffic_source: string (nullable = true)
```

#### Or declare schema & data types (1 second):
```
from pyspark.sql.types import ArrayType, DoubleType, IntegerType, LongType, StringType, StructType, StructField

userDefinedSchema = StructType([
  StructField("device", StringType(), True),  
  StructField("ecommerce", StructType([
    StructField("purchaseRevenue", DoubleType(), True),
    StructField("total_item_quantity", LongType(), True),
    StructField("unique_items", LongType(), True)
  ]), True),
  StructField("event_name", StringType(), True),
  StructField("event_previous_timestamp", LongType(), True),
  StructField("event_timestamp", LongType(), True),
  StructField("geo", StructType([
    StructField("city", StringType(), True),
    StructField("state", StringType(), True)
  ]), True),
  StructField("items", ArrayType(
    StructType([
      StructField("coupon", StringType(), True),
      StructField("item_id", StringType(), True),
      StructField("item_name", StringType(), True),
      StructField("item_revenue_in_usd", DoubleType(), True),
      StructField("price_in_usd", DoubleType(), True),
      StructField("quantity", LongType(), True)
    ])
  ), True),
  StructField("traffic_source", StringType(), True),
  StructField("user_first_touch_timestamp", LongType(), True),
  StructField("user_id", StringType(), True)
])

eventsDF = (spark.read
  .schema(userDefinedSchema)
  .json(eventsJsonPath))
```


#### Use Scala method to generate DDL schema string for you:
```
%scala
spark.read.parquet("/mnt/training/ecommerce/events/events.parquet").schema.toDDL
```

#### Write DF to files with DataFrameWriter's parquet method
```
usersOutputPath = workingDir + "/users.parquet"

(usersDF.write
  .option("compression", "snappy")
  .mode("overwrite")
  .parquet(usersOutputPath)
)
```

#### Write `eventsDF` to [Delta](https://delta.io/) with DataFrameWriter's `save` method and the following configurations:
```
eventsOutputPath = workingDir + "/delta/events"

(eventsDF.write
  .format("delta")
  .mode("overwrite")
  .save(eventsOutputPath)
)
```

#### Write `eventsDF` to a table using the DataFrameWriter method `saveAsTable`
- This creates a global table, unlike the local view created by the DataFrame method `createOrReplaceTempView` 