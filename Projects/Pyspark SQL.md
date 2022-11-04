[[rspark]] [[Databricks Certified Associate Developer for Apache Spark]] [[Intro to PySpark]]
# PySpark DataFrames
- PySpark SQL is a Spark library for structured data
- provides more information about the structure of data & computation
- PySpark DataFrame is an immutable, distributed collection of data with named columns
- Designed for processing both structured (e.g. relational DB) and semi-structured data (e.g. JSON)
- Dataframe API available in python, Scala, and Java
- supports both SQL queries (`SELECT * FROM table`) or expression methods (`df.select()`

RDDs are created with SparkContext 
DataFrames interact with SparkSession

SparkSession is available in PySpark shell as `spark`

#### two different methods of creating DataFrames:
1. From existing RDDs using `spark.createDataFrame(RDD, schema=some_list)` method (data types will be inferred)
```python
rdd = sc.parallelize(sample_list)

names_df = spark.createDataFrame(rdd, schema=['Name', 'Age'])
```
-   SparkSession (`spark`) is used to create DataFrame, register DataFrames, execute SQL queries

2. From various data sources (CSV, JSON, TXT) using SparkSession's read method
    -   `df_csv = spark.read.csv("people.csv", header=True, inferSchema=True)`
    -   `df_json = spark.read.json("people.json", header=True, inferSchema=True)`
    -   `df_txt = spark.read.txt("people.txt", header=True, inferSchema=True)`
- schema controls the data and helps DataFrames to optimize queries
- provides inormation about column name, type of data in the column, empty values, etc.

# SQL queries in SparkSQL
- DataFrame API provides a programmatic domain-specific language (DSL)
- DataFrame transformations and actions are easier to construct programmatically
- SQL queries can be concise and easier to understand and portable
```python
# Create a temporary table "people"
people_df.createOrReplaceTempView("people")

# Construct a query to select the names of the people from the temporary table "people"
query = '''SELECT name FROM people'''

# Assign the result of Spark's query to people_df_names
people_df_names = spark.sql(query)

# Print the top 10 names of the people
people_df_names.show(10)
```

# Plotting data
- `pyspark_dist_explore` library provides quick insight into DataFrames
- currently three functions available - `hist()`, `distplot()` , `pandas_histogram()`
    - pandas DF are in-memory, single-server based structures 
```python
# Check the column names of names_df
print("The column names of names_df are", names_df.columns)

# Convert to Pandas DataFrame  
df_pandas = names_df.toPandas()

# Create a horizontal bar plot
df_pandas.plot(kind='barh', x='Name', y='Age', colormap='winter_r')
plt.show()
```