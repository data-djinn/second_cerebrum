[[Workflow Scheduling & ETL]] [[Data Engineering]] 

# Extract
### Function to extract table to a pandas DataFrame
```
def extract_table_to_pandas(tablename, db_engine):
 query = "SELECT * FROM {}".format(tablename)
 return pd.read_sql(query, db_engine)
```
### Connect to the database using the connection URI
`connection_uri = "postgresql://repl:password@localhost:5432/pagila"`
`db_engine = sqlalchemy.create_engine(connection_uri)`

### Extract the film table into a pandas DataFrame
`extract_table_to_pandas("film", db_engine)`

### Extract the customer table into a pandas DataFrame
`extract_table_to_pandas("customer", db_engine)`

# Transform
-   Selection of specific attributes
-   Translation of code values
-   Data validation
-   Splitting columns into multiple columns
-   Joining from multiple sources

```
import pyspark.sql

spark = pyspark.sql.SparkSession.builder.getOrCreate()
spark.read.jdbc("jdbc:postgresql://localhost:5432/pagila"),
                "customer",
                properties={"user":"repl","password":"password"})

# Get the rental rate column as a string
rental_rate_str = film_df.rental_rate.astype("str")

# Split up and expand the column
rental_rate_expanded = rental_rate_str.str.split(".", expand=True)

# Assign the columns to film_df
film_df = film_df.assign(
 rental_rate_dollar=rental_rate_expanded[0],
 rental_rate_cents=rental_rate_expanded[1],
)

# Use groupBy and mean to aggregate the column
ratings_per_film_df = rating_df.groupBy('film_id').mean('rating')

# Join the tables using the film_id column
film_df_with_ratings = film_df.join(
 ratings_per_film_df,
 film_df.film_id==ratings_per_film_df.film_id
)

# Show the 5 first results
print(fil_df_with_ratings.show(5))
```

# Load
## column vs row-oriented
| column-oriented                | row-oriented                     |
| ------------------------------ | -------------------------------- |
| queries over subset of columns | stored per record                |
|                                | added per transaction\           |
|                                | e.g. adding new customer is fast |
|                                |                                  |

### e.g. redshift
```
# pandas .to_parquet() method
df.to_parquet("./s3://path/to/bucket/customer.parquet")

COPY customer
FROM 's3://path/to/bucket/customer.parquet'
FORMAT AS parquet
```