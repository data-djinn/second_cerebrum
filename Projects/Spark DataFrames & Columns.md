[[spark]] [[Data Engineering]]
### Column:
==a logical construction that will be computed on a per-record basis, based on the data in a dataframe, using an expression==

```python
from pyspark.sql.functions import col

col("device")
eventsDF.device
eventsDF["device"]

------------------------
Out[8]: Column<'device'>

# use column objects to form expressions
col("ecommerce.purchase_revenue_in_usd") + col("ecommerce.total_item_quantity")
col("event_timestamp").desc()
(col("ecommerce.purchase_revenue_in_usd") * 100).cast("int")
```

# Subset transformations
## select()
```python
devicesDF = eventsDF.select("user_id", "device")
display(devicesDF)

from pyspark.sql.functions import col

locationsDF = eventsDF.select("user_id", 
  col("geo.city").alias("city"),
  col("geo.state").alias("state"))

display(locationsDF)
```

## selectExpr()
```python
appleDF = eventsDF.selectExpr("user_id", "device in ('macOS', 'iOS') as apple_user")
display(appleDF)
```
![[Pasted image 20211118230914.png]]

## drop()
- Returns a new DataFrame after dropping the given column, specified as a string or column object
- Use strings to specify multiple columns
```python
anonymousDF = eventsDF.drop("user_id", "geo", "device")
display(anonymousDF)

noSalesDF = eventsDF.drop(col("ecommerce"))
display(noSalesDF)
```

## withColumn()
- Returns a new DataFrame by adding a column or replacing the existing column that has the same name.
    - *Can only add or replace one column at a time*
```python
mobileDF = eventsDF.withColumn("mobile", col("device").isin("iOS", "Android"))
display(mobileDF)

purchaseQuantityDF = eventsDF.withColumn("purchase_quantity", col("ecommerce.total_item_quantity").cast("int"))
purchaseQuantityDF.printSchema()

finalDF = (topTrafficDF.withColumn(
  "total_rev",
  ((col("total_rev") * 100).cast("long") / 100)
))

finalDF = (finalDF.withColumn(
  "avg_rev",
  ((col("avg_rev") * 100).cast("long") / 100)
))
```

## withColumnRenamed()
`locationDF = eventsDF.withColumnRenamed("geo", "location")`

# Subset Rows
## filter()
- filter rows using the given SQL expression or column based condition
```python
purchasesDF = eventsDF.filter("ecommerce.total_item_quantity > 0")

revenueDF = eventsDF.filter(col("ecommerce.purchase_revenue_in_usd").isNotNull())

androidDF = eventsDF.filter((col("traffic_source") != "direct") & (col("device") == "Android"))
```

## dropDuplicates()
- returns a new dataframe with duplicate rows removed, optionally considering only a subset of columns
- alias: distinct
```python
eventsDF.distinct()

distinctUsersDF = eventsDF.dropDuplicates(["user_id"])
```

## limit()
- returns a new DataFrame by taking the first n rows
`limitDF = eventsDF.limit(100)`

## sort()
```python
topTrafficDF = (trafficDF.sort(
  trafficDF.total_rev.desc()).limit(3)
)
```

# Chain commands together for efficiency
```python
finalDF = (eventsDF
  .withColumn("revenue", col("ecommerce.purchase_revenue_in_usd"))
  .filter(col("revenue").isNotNull())
  .drop("event_name")
)

chainDF = (df.groupBy("traffic_source").agg(
    sum(col("revenue")).alias("total_rev"),
    avg(col("revenue")).alias("avg_rev"))
  .sort(col("total_rev").desc())
  .limit(3)
  .withColumn("avg_rev", round("avg_rev", 2))
  .withColumn("total_rev", round("total_rev", 2))
)
```