.[[Python]] [[Data Engineering]] [[Data Pipelines with Kedro]][[Unit Testing in Python]]
- democratizing data increases insights
### operational data is stored in the ==landing tables== ("bronze")
  - always there
  - unaltered data as it was first recieved
### data is ==ingested== into the data lake
- data is cleaned to prevent duplicate data processes
- services are built on top of these data lakes

### per use case, the clean data is further acted on & stored in the business layer
![[Pasted image 20220223182942.png]]

# Singer
- open source library that can connect many different datasources
  - "the open source standard for writing scripts that move data"
- a specification that describes how data extraction scripts and data loading scripts should communicate using a standard JSON-based data format over stdout (standardized "location" to which programs write their output
- extract data with *taps* scripts
- load data to *targets* scripts
- can be written in any programming language
  - language-independent
- communicate over *streams*:
  - schema (metadata)
  - state (process metadata)
  - record (data)
- use streams to partition data based on the topic
  - e.g. error messages --> error stream

1. describe the data by specifying its schema in json

```
# Complete the JSON schema
schema = {'properties': {
    'brand': {'type': 'string'},
    'model': {'type': 'string'},
    'price': {'type': 'number'},
    'currency': {'type': 'string'},
    'quantity': {'type': 'number', 'minimum': 1},  
    'date': {'type': 'string', 'format': 'date'},
    'countrycode': {'type': 'string', 'pattern': "^[A-Z]{2}$"}, 
    'store_name': {'type': 'string'}}}

# Write the schema
singer.write_schema(stream_name='products', schema=schema, key_properties=[])
```

## Serializing  JSON
`json.dumps(json_schema['properties']['age'])` - writes the object to a string
`with open("foo.json", mode="w") as fh:`
  `json.dump(obj=json_schema, fp=fh)` -writes the same string to a file

## Running an ingestion pipeline with singer
### Streaming record messages 
- use `write_record()` to convert a single tuple into **singer RECORD message**
  - stream name has to match the stream you specified earlier in a schema message, otherwise the records are ignored
  - *almost* equivalent to nesting the actual record dictionary in another dictionary that has 2 more keys, being the "type" and the "stream"
    - can be done with an unpacking operator **

### chaining taps & targets
```
import singer

singer.write_schema(stream_name="foo", schema=...)
singer.write_records(stream_name="foo", record=...)
```
- ingestion pipeline : **pipe** `|` the taps' output into a singer target
##### Provides modularity
- each tap/target is designed to do **one thing well**
  - easily configured with config files
  - by working with a standardized intermediate format, you could easily swap out the new targets  
`tap-custom-google-sheet-scraper | target-postgresql --config headlines.json`

## Singer STATE messages
- used to keep track of state
- e.g. need to extract only new records from your db daily at noon
  - keep track of the highest "last_updated_on"  value & emit that as a state message at the end of a successful run of your tap
  - you can then reuse the same message to extract only those records that were updated after this old state
`singer.write_state(value={"max-last-updated-on": some_variable})`

## communicate with an API
```
endpoint = "http://localhost:5000"

# Fill in the correct API key
api_key = "scientist007"

# Create the web API’s URL
authenticated_endpoint = "{}/{}".format(endpoint, api_key)

# Get the web API’s reply to the endpoint
api_response = requests.get(authenticated_endpoint).json()
pprint.pprint(api_response)

# Create the API’s endpoint for the shops
shops_endpoint = "{}/{}/{}/{}".format(endpoint, api_key, "diaper/api/v1.0", "shops")
shops = requests.get(shops_endpoint).json()
print(shops)

# Create the API’s endpoint for items of the shop starting with a "D"
items_of_specific_shop_URL = "{}/{}/{}/{}/{}".format(endpoint, api_key, "diaper/api/v1.0", "items", "DM")
products_of_shop = requests.get(items_of_specific_shop_URL).json()
pprint.pprint(products_of_shop)
--------------------------------------
   {'apis': [{'description': 'list the shops available',
               'url': '<api_key>/diaper/api/v1.0/shops'},
              {'description': 'list the items available in shop',
               'url': '<api_key>/diaper/api/v1.0/items/<shop_name>'}]}
    {'shops': ['Aldi', 'Kruidvat', 'Carrefour', 'Tesco', 'DM']}
    {'items': [{'brand': 'Huggies',
                'countrycode': 'DE',
                'currency': 'EUR',
                'date': '2019-02-01',
                'model': 'newborn',
                'price': 6.8,
                'quantity': 40},
               {'brand': 'Huggies',
                'countrycode': 'AT',
                'currency': 'EUR',
                'date': '2019-02-01',
                'model': 'newborn',
                'price': 7.2,
                'quantity': 40}]}
```
```
# Use the convenience function to query the API
tesco_items = retrieve_products("Tesco")

singer.write_schema(stream_name="products", schema=schema,
                    key_properties=[])

# Write a single record to the stream, that adheres to the schema
singer.write_record(stream_name="products", 
                    record={**tesco_items[0], "store_name": "Tesco"})

for shop in requests.get(SHOPS_URL).json()["shops"]:
    # Write all of the records that you retrieve from the API
    singer.write_records(
      stream_name="products", # Use the same stream name that you used in the schema
      records=({**item, "store_name": shop}
               for item in retrieve_products(shop))
    )    
-----------------------------------------------------
{"type": "SCHEMA", "stream": "products", "schema": {"properties": {"brand": {"type": "string"}, "model": {"type": "string"}, "price": {"type": "number"}, "currency": {"type": "string"}, "quantity": {"type": "integer", "minimum": 1}, "date": {"type": "string", "format": "date"}, "countrycode": {"type": "string", "pattern": "^[A-Z]{2}$"}, "store_name": {"type": "string"}}}, "key_properties": []}
    {"type": "RECORD", "stream": "products", "record": {"countrycode": "IE", "brand": "Pampers", "model": "3months", "price": 6.3, "currency": "EUR", "quantity": 35, "date": "2019-02-07", "store_name": "Tesco"}}
    {"type": "RECORD", "stream": "products", "record": {"countrycode": "BE", "brand": "Diapers-R-Us", "model": "6months", "price": 6.8, "currency": "EUR", "quantity": 40, "date": "2019-02-03", "store_name": "Aldi"}}
    {"type": "RECORD", "stream": "products", "record": {"countrycode": "BE", "brand": "Nappy-k", "model": "2months", "price": 4.8, "currency": "EUR", "quantity": 30, "date": "2019-01-28", "store_name": "Kruidvat"}}
    {"type": "RECORD", "stream": "products", "record": {"countrycode": "NL", "brand": "Nappy-k", "model": "2months", "price": 5.6, "currency": "EUR", "quantity": 40, "date": "2019-02-15", "store_name": "Kruidvat"}}
    {"type": "RECORD", "stream": "products", "record": {"store": "Carrefour", "countrycode": "FR", "brand": "Nappy-k", "model": "2months", "price": 5.7, "currency": "EUR", "quantity": 30, "date": "2019-02-06", "store_name": "Carrefour"}}
    
```
# PySpark
### describe schema whenever possible
```
df = (spark.read
      .options(header=True)
      .csv("/home/repl/workspace/mnt/data_lake/landing/ratings.csv"))

df.show()
```
```
# Define the schema
schema = StructType([
  StructField("brand", StringType(), nullable=False),
  StructField("model", StringType(), nullable=False),
  StructField("absorption_rate", ByteType(), nullable=True),
  StructField("comfort", ByteType(), nullable=True)
])

better_df = (spark
             .read
             .options(header="true")
             # Pass the predefined schema to the Reader
             .schema(schema)
             .csv("/home/repl/workspace/mnt/data_lake/landing/ratings.csv"))
pprint(better_df.dtypes)
```

### Clean data
- incorrect data types
- invalid rows (manual entry)
- incomplete rows
- badly chosen placeholders (like "N/A")
- ==data cleaning depends on the context==
  - can our system cope with data that is 95% clean and 95% complete?
  - what are the implicit standards in the company?
    - regional datetimes vs. UTC
    - column naming conventions
  - what are the low-level details of the systems?
    - representation of unknown / incomplete data
    - ranges for numerical values
    - meaning of fields (e.g. NULLs)

### Transform data
common transformations
- filtering rows & ordering results
`prices_in_belgium = prices.filter(col('country_code') == 'BE').orderBy(col('date'))`
- selecting & renaming columns
`prices.select(col('store'), col('brand').alias('brand_name')).distinct()`
- grouping & aggregation
`(prices.groupBy(col('brand')).mean('price')).show()`
- joining multiple datasets
`ratings_with_prices = ratings.join(prices, ["brand", "model"])`

### packaging your application
#### run your pipeline locally
- running PySpark program locally is no different from regular python
`python my_pyspark_data_pipeline.py # script must start at least a SparkSession`
**conditions**
- local installation of Spark
- access to referenced resources
- classpath is properly configured (similar role to `PYTHONPATH` env var)
#### use `spark-submit` helper program
- comes with all spark installations
1. sets up launch env for use with the *cluster manager* & the selected *deploy mode*
==cluster manager are programs that make cluster resources, like RAM & CPUs of different nodes, available to other programs==
- Yarn is a popular example
- each has different options
==deploy mode tells Spark where to run the driver of the Spark application: either dedicated master node, or one of the cluster worker nodes==
2. invoke main class/module/app/function
- URL specific to the cluster manager, or a local string
- tells spark where it can get resources from, which is either a real cluster or simply your local machine
- if you rely on more than just one python module, you need to copy this module to all nodes, so all nodes know what functions/actions to perform
  - typically provided via zip files or py files
  - can take a comma-separated list of files that will be added on each worker's PYTHONPATH
    - lists the places where the python interpreter will look for modules
  - zip files are a common way to package & distribute your code
    - navigate to the root dir, and invoke the `zip` utility with:
 `zip --recurse-paths dependencies.zip root_dir`
     - resulting zip file can be passed as-is to `spark-submit`'s `--py-files` argument
- pass the main file, which contains code to trigger the creation of the SparkSession & run a job  - 
##### `spark-submit --master "local[*]" --py-files PY_FILES MAIN_PYTHON_FILE python_app_arg`

# Tests
software tends to change:
- new functionality desired
- bugs need to get squashed
- performance needs to be improved

core functionality rarely changes - how to ensure stability in light of changes? **tests**
- tests are written & executed to assert our expectations are matched
- by committing tests to our code base, we have a written copy of our expectations as they were at some point in the past
- improve chance of code being correct in the future 
  - prevent introducing breaking changes
- raises confidence (not a guarantee) that code is correct *now*
  - assert actuals match expectations
- most up-to-date documentation
  - form of documentation that is always in sync with what's running
  - well-written tests clarify what a piece of code does, even if it treats that piece like a black box

##### the test pyramid:
testing takes time
- thinking about what to test
- writing tests
- running tests
***but*** **testing has a high ROI**
- when targeted at the correct layer
- when testing the non-trivial parts, e.g. distance between 2 coordinates ? 

**1. Unit tests:** testing pieces of code that do not rely on integration with external components are unit tests
- run fast
- dev effort is minimal - have many of these!
- e.g. data cleaning functions
**2. Integration/service tests:** interaction with file systems and databases are integration tests, as they integrate different services or components
- run more slowly
- take more effort
**3.  UX tests**
- follow the user experience as closely as possible

### Writing unit tests for Pyspark
#### Separate transform from extract & load
- reading from system file (csv, hdfs) has dowsides:
  - depends on i/o (network access, filesystem permissions, directory location, etc)
  - unclear how big the data is
  - unclear what data goes in
- remove dependencies & focus on transformations by creating a **small**  in-memory dataframe
  - improves readability & understanding, because any dev can look at your code and immediately see the inputs to some function and how they relate to the output
  - additionally, you can illustrate how the func behaves with normal data and with exceptional data (like missing or incorrect fields)
##### Create a custom `pyspark.sql.Row` class & pass it any iterable (e.g. tuple)
```
from pyspark.sql import Row
purchase = Row('price'
                , 'quantity'
                , 'product'
                )
record = purchase(12.99, 1, 'cake')
df = spark.createDataFrame((record,))
```
  - inputs are clear
  - data is close to where it is being used ('code-proximity')

#### Create small, reusable, & well-named function
- combining steps make it hard to test functionality
- write out each step to its own function, and apply them in sequence
- each transformation by itself can now be tested
##### testing a single unit
```
def test_calculated_unit_price_in_euro():
  record = dict(price=10
                ,quantity=5
                ,exchange_rate_to_euro=2.)
  df = spark.createDataFrame([Row(**record)])
  result = calculate_unit_price_in_euro(df)
  
  expected_record = Row(**record, unit_price_in_euro=4.)
  expected = spark.createDataFrame([expected_record])
  
  assertDataFrameEqual(result, expected)
```
- interacting with external data sources is costly
- creating in-memory DataFrames makes testing easier
  - data is in plain sight
  - focus is on just a small number of examples
- create small & well-named functions

## Continuous Testing
##### Python testing modules:
- unittest (standard lib)
- doctest (standard lib)
- pytest
- nose

all these tools look for modules, classes, and functions, that are marked in a special way
- core tasks are assertions
- these tools generate reports to help you hone in on bugs
- spark & other distributed computing frameworks add overhead to these tests (2 seconds is a long time)

### Automating test
- running unit tests manually is tedious & error prone
  - you may forget to run them after a series of changes to your code
- **configure git hooks to run test scripts on every commit**
  - rapid feedback!
- second line of defense: CI/CD pipeline

#### Continuous Integration:
- get code changes integrated with the master branch regularly
  - (provided the code didn't break anything - TESTS!)
  - essentially, run many tests, often, and then commit to main

#### Continuous Delivery
- create "artifacts" (deliverables like documentation, but also programs) that can be deployed into production without breaking things
- all artifacts should always be in deployable state at any time without any problem
##### CircleCi
- runs tests automatically for you
- looks for `~/.circleci/config.yml` in your repo
```
jobs:
  test:
    docker:
      - image: circleci/python:3.6.4
    steps:
      - checkout
      - run: pip install -r requirements.txt
      - run: pytest .
```

- check out application from version control
- install python application's dependencies
- run the test suite of your application
- create artifacts (jar, wheel, documentation...)
- save the artifacts to a location accessible by your company's compute infrastructure

##### Improve PEP8 guide compliance with `flake8`
- static code checker - **does not actually run your code**
- 