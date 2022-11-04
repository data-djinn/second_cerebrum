[[spark]] [[Data Engineering]] 

# Aggregation
## groupBy
![[Pasted image 20211122190955.png]]
### returns Grouped data object via methods:
| **method** | description                                                                     |
| ---------- | ------------------------------------------------------------------------------- |
| **avg**    | the mean value for each numeric columns for each group                          |
| **count**  | count the number of rows for each group                                         |
| **max**    | the max value for each numeric columns for each group                           |
| **min**    | the min value for each numeric columns for each group                           |
| **mean**   | average value for each numeric columns for each group                           |
| **pivot**  | pivots a column of the current DataFrame and performs the specified aggregation |
| **agg**    | compute the aggregate by specifying a series of aggregate columns               |
| **sum**    | sum for each numeric columns for each group                                     |

```python
eventCountsDF = df.groupBy("event_name").count()
display(eventCountsDF)

from pyspark.sql.functions import sum

statePurchasesDF = df.groupBy("geo.state").agg(sum("ecommerce.total_item_quantity").alias("total_purchases"))
display(statePurchasesDF)
```

## built-in aggregate functions

| **method**                | description                                                        |
| ------------------------- | ------------------------------------------------------------------ |
| **approx_count_distinct** | returns the approximate number of distinct items in a group        |
| **avg**                   | returns the average of the values in a group                       |
| **collect_list**          | returns a list of objects with duplicates                          |
| **corr**                  | returns the Pearson Correlation Coefficient for 2 columns          |
| **max**                   | compute the max value for each numeric columns for each group      |
| **mean**                  | compute the average value for each numeric columns for each group  |
| **stddev_samp**           | returns the sample standard deviation of the expression in a group |
| **sumDistinct**           | returns the sum of distinct values in the expression               |
| **var_pop**               | returns the population variance of the values in a group           |

```python
from pyspark.sql.functions import avg, approx_count_distinct

stateAggregatesDF = 
df.groupBy("geo.state")
    .agg(
    avg("ecommerce.total_item_quantity").alias("avg_quantity"),
    approx_count_distinct("user_id").alias("distinct_users"))

display(stateAggregatesDF)
```

## Built-in Math functions
| **method** | **description**                                                                     |
| ---------- | ----------------------------------------------------------------------------------- |
| ceil       | computes the ceiling of the given column                                            |
| log        | computes the natural logarithm of the given value                                   |
| round      | returns the value of the column rounded to 0 decimal places with HALF-UP round mode |
| sqrt       | computes the sqrt of the specified *float* value                                    |

# Datetimes
| method         | description                                                                                                                                                                                      |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| date_format    | converts a date/timestamp/string to a value of string in the format specified by the date format given by the second argument                                                                    |
| add_months     | returns the date that is numMonths after startDate                                                                                                                                               |
| dayofweek      | extracts the day of the month as an integer from a given date/timestamp/string                                                                                                                   |
| from_unixtime  | converts the number of seconds from unix epoch (1970-01-01 00:00:00 UTC) to a string representing the timestamp of that moment in the current system time zone in the yyyy-MM-dd HH:mm:ss format |
| minute         | extracts the minutes as an integer from a given date/timestamp/string                                                                                                                            |
| unix_timestamp | converts time string with given pattern to Unix timestamp (in seconds)                                                                                                                           |


# Complex types
### String Functions
- **translate**: translate any character in the src by a character in replaceString
- **regexp_replace**: replace a specific group matched by a Java regex, from the specified string column
- **ltrim**: extract a specific group matched by a Java regex, from the specified string column
- **lower**: converts a string column to lowercase
- **split**: splits str around matches of the given pattern

## Collection functions
- **array_contains**: returns null if the array is null, true if the array contains value, and false otherwise
- **explode**: creates a new row for each element in the given array or map column
- **slice**: returns an array containing all the elements in x from index start (or starting from the end if start is negative) with the specified length
- [ ] **element_at**
- [ ] **collect_set**
### 1. Extract item details from purchases
- Explode **`items`** field in **`df`**
- Select **`email`** and **`item.item_name`** fields
- Split words in **`item_name`** into an array and alias with "details"
```python
from pyspark.sql.functions import *

detailsDF = (df.withColumn("items", explode("items"))
  .select("email", "items.item_name")
  .withColumn("details", split(col("item_name"), " "))             
)
display(detailsDF)
```

### 2. Extract size and quality options from mattress purchases
- Filter **`detailsDF`** for records where **`details`** contains "Mattress"
- Add **`size`** column from extracting element at position 2
- Add **`quality`** column from extracting element at position 1
```python
mattressDF = (detailsDF.filter(array_contains(col("details"), "Mattress"))
  .withColumn("size", element_at(col("details"), 2))
  .withColumn("quality", element_at(col("details"), 1))
)           
display(mattressDF)
```

### 3. Extract size and quality options from pillow purchases
- Filter **`detailsDF`** for records where **`details`** contains "Pillow"
- Add **`size`** column from extracting element at position 1
- Add **`quality`** column from extracting element at position 2

Note the positions of **`size`** and **`quality`** are switched for mattresses and pillows.
```python
pillowDF = (detailsDF.filter(array_contains(col("details"), "Pillow"))
  .withColumn("size", element_at(col("details"), 1))
  .withColumn("quality", element_at(col("details"), 2))
)           
display(pillowDF)
```

### 4. Combine data for mattress and pillows
- Perform a union on **`mattressDF`** and **`pillowDF`** by column names
- Drop **`details`** column
```python
unionDF = (mattressDF.unionByName(pillowDF)
  .drop("details"))
display(unionDF)
```
### 5. List all size and quality options bought by each user
- Group rows in **`unionDF`** by **`email`**
  - Collect set of all items in **`size`** for each user with alias "size options"
  - Collect set of all items in **`quality`** for each user with alias "quality options"
```python
optionsDF = (unionDF.groupBy("email")
  .agg(collect_set("size").alias("size options"),
       collect_set("quality").alias("quality options"))
)
```

# Additional Functions
#### non-aggregate:
- **col**: returns a column based on the given column name
- **lit**: creates a column of literal value
- **isnull**: returns true if the column is null
- **rand**: generate a random column with independent and identically distributed samples uniformly distributed in [0.0, 1.0]
#### NA functions
- **drop**: returns a new DataFrame omitting rows with any, all, or a specified number of null values, considering an optional subset of columns
- **fill**: replace null values with the specified value for an optional subset of columns
- **replace**: returns a new DataFrame replacing a value with another value, considering an optional subset of columns

```python
cartsDF = (eventsDF.withColumn("items", explode("items"))
           .select("user_id", "items.item_id")
           .groupBy("user_id")
           .agg(collect_set("item_id").alias("cart"))
)
display(cartsDF)
```