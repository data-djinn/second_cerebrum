[[Python]] [[SQLalchemy Intro]] [[Data Engineering]]
# SQLAlchemy
-   Generate SQL queries by writing Python code
==*Operate across all RDBMS platforms in a consistent manner*==
###   2 main pieces:
- Core (Relational Model focused)
- ORM (User Data Model focused)

- remember to set pyodbc pooling to false for ODBC connections! sqlAlchemy already does this for us

## Connecting to a database
```python
from sqlalchemy import create_engine
engine = create_engine('sqlite:///data.sqlite')
connection = engine.connect()
```
- **Engine**: common interface to the database from SQLalchemy
- Connection string: all the details required to find the database (and logins)
```python
print(engine.table_names())
>>['census', 'state_fact']
```
- **reflection** reads database and builds SQLalchemy table objects
```python
from sqlalchemy import MetaData, Table
metadata = Metadata()
census = Table('census', metadata, autoload=True, autoload_with=engine)
print(repr(census))
```

# Autoloading Tables from a database

SQLAlchemy can be used to automatically load tables from a database using something called reflection. ==Reflection is the process of reading the database and building the metadata based on that information.== It's the opposite of creating a Table by hand and is very useful for working with existing databases.

To perform reflection, you will first need to import and initialize a `MetaData` object. `MetaData` objects contain information about tables stored in a database. During reflection, the `MetaData` object will be populated with information about the reflected table automatically, so we only need to initialize it before reflecting by calling `MetaData()`.

You will also need to import the `Table` object from the SQLAlchemy package. Then, you use this `Table` object to read your table from the engine, autoload the columns, and populate the metadata. This can be done with a single call to `Table()`: using the `Table` object in this manner is a lot like passing arguments to a function. For example, to autoload the columns with the engine, you have to specify the keyword arguments `autoload=True` and `autoload_with=engine` to `Table()`.

Finally, to view information about the object you just created, you will use the `repr()` function. For any Python object, `repr()` returns a text representation of that object. For SQLAlchemy `Table` objects, it will return the information about that table contained in the metadata.


## Viewing Table details
It is important to get an understanding of your database by examining the column names. This can be done by using the `.columns` attribute and accessing the `.keys()` method. For example, `census.columns.keys()` would return a list of column names of the `census` table.

Following this, we can use the metadata container to find out more details about the reflected table such as the columns and their types. For example, information about the table objects are stored in the `metadata.tables` dictionary, so you can get the metadata of your `census` table with `metadata.tables['census']`. This is similar to your use of the `repr()` function on the `census` table from the previous exercise.
```python
from sqlalchemy import create_engine, MetaData, Table

engine = create_engine('sqlite:///census.sqlite')

metadata = MetaData()

# Reflect the census table from the engine: census
census = Table('census', metadata, autoload=True, autoload_with=engine)

# Print the column names
print(census.columns.keys())

# Print full metadata of census
print(repr(metadata.tables['census']))

>> ['state', 'sex', 'age', 'pop2000', 'pop2008'] Table('census', MetaData(bind=None), Column('state', VARCHAR(length=30), table=<census>), Column('sex', VARCHAR(length=1), table=<census>), Column('age', INTEGER(), table=<census>), Column('pop2000', INTEGER(), table=<census>), Column('pop2008', INTEGER(), table=<census>), schema=None)
```

## Basic SQL querying
```python
from sqlalchemy import create_engine
engine = create_engine('sqlite:///census_nyc.sqlite')
connection = engine.connect()
stmt = 'SELECT * FROM people'
result_proxy = connection.execute(stmt)
result = result_proxy.fetchall()
```

## use SQL to build queries
- provides a pythonic way to build SQL statements
- ==Hides differences between backend database types==
### select statement
- requires a list of one or more tables or columns
- using a table will select all the columns in it
```python
# Import select
from sqlalchemy import select

# Reflect census table via engine: census
census = Table('census', metadata, autoload=True, autoload_with=engine)

# Build select statement for census table: stmt
stmt = select([census])

# Print the emitted statement to see the SQL string
   print(stmt)

# Execute the statement on connection and fetch 10 records: result
results = connection.execute(stmt).fetchmany(size=10)

# Execute the statement and print the results
print(results)
```

-   ResultProxy: The object returned by the `.execute()` method. It can be used in a variety of ways to get the data returned by the query.
-   ResultSet: The actual data asked for in the query when using a fetch method such as `.fetchall()` on a ResultProxy.
-   This separation between the ResultSet and ResultProxy allows us to fetch as much or as little data as we desire.

Once we have a ResultSet, we can use Python to access all the data within it by column name and by list style indexes. For example, you can get the first row of the results by using `results[0]`. With that first row then assigned to a variable `first_row`, you can get data from the first column by either using `first_row[0]` or by column name such as `first_row['column_name']`

# Filtering & targeting
#### Simple filter
```python
# Create a select query: stmt
stmt = select([census])  

# Add a where clause to filter the results to only those for New York : stmt_filtered
stmt = stmt.where(census.columns.state == 'New York')

 # Execute the query to retrieve all the data returned: results
results = connection.execute(stmt)
  
# Loop over the results and print the age, sex, and pop2000
for result in results:
    print(result.age, result.sex, result.pop2000)
```

#### Use expressions to filter data
```python
# Define a list of states for which we want results
states = ['New York', 'California', 'Texas']

# Create a query for the census table: stmt
stmt = select([census])

# Append a where clause to match all the states in_ the list states
stmt = stmt.where(census.columns.state.in_(states))

for state in connection.execute(stmt):
    print(state.state, state.pop2000)

stmt = stmt.where(
 # The state of California with a non-male sex
 and_(census.columns.state == 'California',
 census.columns.sex != 'M'
 )
)

for result in connection.execute(stmt):
 print(result.age, result.sex)
```

#### Order by clauses
- `order_by(census.columns.state)`
- wrap the column with `desc()` in the `order_by()` clause
##### Order by multiple columns
```python
stmt = select([census.columns.state, census.columns.sex])
stmt = stmt.order_by(census.columns.state, census.columns.sex)
results = connection.execute(stmt)
```
- separate multiple 
- orders by the first column, then orders duplicates by the second column, etc
-  Build a query to select the state column: stmt
```python
# Import desc
from sqlalchemy import desc

# Build a query to select the state column: stmt
stmt = select([census.columns.state])

# Order stmt by state in descending order: rev_stmt
rev_stmt = stmt.order_by(stmt = stmt.order_by(census.columns.state, census.columns.age.desc())

# Execute the query and store the results: rev_results
rev_results = connection.execute(rev_stmt).fetchall()

# Print the first 10 rev_results
print(rev_results[0:10])
```

# SQL functions
`from sqlalchemy import func`
- import as func.sum so it doesn't interfere with python's native functions
- much more efficient than processing in python
```python
from sqlalchemy import func

stmt = select([census.columns.state, func.count(census.columns.age)])

# Group stmt by state
stmt = stmt.group_by(census.columns.state)

# Execute the statement and store all the records: results
results = connection.execute(stmt).fetchall()

# Print the keys/column names of the results returned
print(results[0].keys())

# Build a query to count the distinct states values: stmt
stmt = select([func.count(census.columns.state.distinct())])

# Execute the query and store the scalar result: distinct_state_count
distinct_state_count = connection.execute(stmt).scalar()

# Print the distinct_state_count
print(distinct_state_count)
```

- build expressions & label them for custom calculations
```python
# Import func
from sqlalchemy import func

# Build an expression to calculate the sum of pop2008 labeled as population
pop2008_sum = func.sum(census.columns.pop2008).label('population')

# Build a query to select the state and sum of pop2008: stmt
stmt = select([census.columns.state, pop2008_sum])

# Group stmt by state
stmt = stmt.group_by(census.columns.state)

# Execute the statement and store all the records: results
results = connection.execute(stmt).fetchall()

# Print results
print(results)

# Print the keys/column names of the results returned
print(results[0].keys())
```

# More advanced stuff
## Calculated columns
supported operators: `+` & `-` & `/` & `*`

```python
stmt = select([census.columns.age,
              (census.columns.pop2008 -
              census.columns.pop2000).label('pop_change')
      ])
stmt = stmt.group_by(census.columns.age)
stmt = stmt.order_by(desc('pop_change')]
stmt = stmt.limit(5)
results = connection.execute(stmt).fetchall()
print(results)
```


## Case statements
- used to treat data differently based on a condition
- accepts a list of conditions to mach and a celumn to return if the condition matches
- the list of conditions ends with an else clause to determine what to do when a record doesn't match any prior conditions
### Case example
```python
from sqlalchemy import case
stmt = select([
                func.sum(
                    case([
                        (census.columns.state == 'New York',
                        census.columns.pop2008)
                    ], else_=0))])
```

### Cast statement
- converts data to another type
- useful for converting:
    - integers to floats for division
    - strings to dates ond times
- accepts a column o expression and the target Type

# Relationships
- allows us to avoid duplicate data
- make it easy to chonge things in one place
- useful to break out information from a table we don't need very often
- some relationships are pre-defined in the DB & don't need to be specified

### Join
- Accepts a table and an optional expression that explains how the two tables are related
- the expression is not needed if the relationship is predefined and available 
```python
stmt = select([func.sum(census.columns.pop2000)])
stmt = stmt.select_from(census.join(state_fact))
stmt = stmt.where(state_fact.columns.circuit_court == '10')
result = connection.execute(stmt).scalar()
```

### Joining tables without predefined relationship
- join accepts a table and an optional expression that explains how the two tables are related
- will only join on data that match between the two columns
- avoid joining on columns of different types
```python
stmt = select([func.sum(census.columns.pop2000)])
stmt = stmt.select_from(
        census.join(state_fact, census.columns.state == state_fact.columns.name))
stmt = stmt.where(
        state_fact.columns.census_division_name == 'East South Central')
result = connection.execute(stmt).scalar()
```

# Handling large ResultSets
- might run out of memory/disk space
#### `fetchmany()` to specify how many rows we want to act upon
#### We can loop over `fetchmany()`
#### it returns an empty list when there are no more records
#### we have to close the ResultProxy afterwards
```python
while more_results:
    partial_results = results_proxy.fetchmany(50)
    if partial_results == []:
        more_results = False
    for row in partial_results:
        state_count[row.state] += 1
results_proxy.close()
```

# Creating & Manipulating Databases
- varies by the database type
- `create_engine()` SQLLite will create a database + file if they do not already exist  like  
```python
from sqlalchemy import (Table, Column, String, Integer, Decimal, Boolean)
employees = Table('emplogees', metadata,
                    Column('id', Integer()),
                    Column('name', String(255)),
                    Column('salary', Decimal()),
                    Column('active', Boolean()))
metatada.create_all(engine)
engine.table_names()
```

## Creating tables
- still uses the Table object like we did for reflection
- Replaces the autoload keyword arguments with Column objects
- Creates the tables in the actual database by using the `create_all()` method on the MetaData instance
- **you need to use other tools to handle database table updates, such as adding or removing columns**
    -  use Alembic or raw SQL

### Table constraints
- `unique` forces all values for the data in a column to be unique
- `nullable` determines if a column can be empty in a roj
- `default` sets a default value if one isn't supplied
- all other boolean conditions imaginable with more advanced constraints
```python
employees = Table('emplayees', metadata,
                    Column('id', Integer()),
                    Column('name', String(255), unique=True, nullable=False),
                    Column('salary', Float(), default=100.00),
                    Column('active', Boolean(), default=True))
                    
employees.constraints # access Table object attribute

# Import Table, Column, String, Integer, Float, Boolean from sqlalchemy
from sqlalchemy import Table, Column, String, Integer, Float, Boolean

# Define a new table with a name, count, amount, and valid column: data
data = Table('data', metadata,
             Column('name', String(255)),
             Column('count', Integer()),
             Column('amount', Float()),
             Column('valid', Boolean())
)

# Use the metadata to create the table
metadata.create_all(engine)

# Print table details
print(repr(data))
```

# Joins
- if two tables already have an established relationship, you can use all the columns & it will be joined automatically
```python
# Build a statement to select the state, sum of 2008 population and census
# division name: stmt
stmt = select([
    census.columns.state,
    func.sum(census.columns.pop2008),
    state_fact.columns.census_division_name
])

# Append select_from to join the census and state_fact tables by the census state and state_fact name columns
stmt_joined = stmt.select_from(
    census.join(state_fact, census.columns.state == state_fact.columns.name)
)

# Append a group by for the state_fact name column
stmt_grouped = stmt_joined.group_by(state_fact.columns.name)

# Execute the statement and get the results: results
results = connection.execute(stmt_grouped).fetchall()

# Loop over the results object and print each record.
for record in results:
    print(record)

```
### Hierarchical (self-referencing) tables
- contain a relationship with themselves
- commonly used for:
    - organizational data
    - geographic data
    - networks
    - graph
`alias()` gives us a way to view the table via multiple names
    - important to target the right alias with your `group_by()`
    - if you're not using both the alias and the table name for a query, don't create the alias at all
# Insert
- `insert()`
    - takes the table we are loading data into as the argument
    - add all the values we want to insert with the values clause as `column=value` pairs
    - doesn't return any rows, so no need for a fetch method

```python
from sqlalchemy import insert

stmt = insert(employees).values(id=1,name='Jason',
                                salary=1.0, active=True)
result_proxy = conn.execute(stmt)
print(result_proxy.rowcount) # see how many rows were created
```
##### note that single row insert doesn't use quotes on column names
### Inserting multiple rows
- build an insert statement without any values
- build a **list of dictionaries** that represent all the values clauses for the rows you want to import
- pass both the statement and the values list to the execute method on connection
```python
stmt = insert(employees)
values_list = [{'id':2, 'name':'Rebecca',
                'salary': 2.00, 'active': True},
                {'id':3, 'name':'Bob',
                'salary': 0.00, 'active': False}]
result_proxy = connection.execute(stmt, values_list)
print(result_proxy.rowcount)
```

## Load csv into table
One way to do that would be to read a CSV file line by line, create a dictionary from each line, and then use `insert()`

But there is a faster way using pandas. You can read a CSV file into a DataFrame using the `read_csv()` function (this function should be familiar to you, but you can run help(pd.read_csv) in the console to refresh your memory!). Then, you can call the ``.to_sql()`` method on the DataFrame to load it into a SQL table in a database. The columns of the DataFrame should match the columns of the SQL table.

`.to_sql()` has many parameters, but in this exercise we will use the following:

-  `name` is the name of the SQL table (as a string).
-  `con` is the connection to the database that you will use to upload the data.
-  `if_exists` specifies how to behave if the table already exists in the database; possible values are "fail", "replace", and "append".
-  `index` (True or False) specifies whether to write the DataFrame's index as a column.
```python
# import pandas
import pandas as pd

# read census.csv into a DataFrame : census_df
census_df = pd.read_csv("census.csv", header=None)

# rename the columns of the census DataFrame
census_df.columns = ['state', 'sex', 'age', 'pop2000', 'pop2008']

# append the data from census_df to the "census" table via connection
census_df.to_sql(name="census", con=connection, if_exists="append", index=False)
```

# Update
`update`
- similar to the `insert()` statement but includes a `where` clause to determine which record will be updated
- we add all the values we want to update with the `values()` clause as `column=value` pairs
```python
from sqlalchemy import update
stmt = update(employees)
stmt = stmt.where(employees.columns.id == 3)
stmt = stmt.values(active=True)
result_proxy = connection.execute(stmt)
print(result_proxy.rowcount)
```
##### Updates multiple rows depending on your where clause

### Inserting multi

# Correlated updates
```python
new_salary = select([employees.columns.salary])
new_salary = new_salary.order_by(
    desc(employees.columns.salary))
new_salary = new_salary.limit(1)
stmt = update(employees)
stmt = stmt.values(salary=new_salary) # use correlated subquery!
result_proxy = connection.execute(stmt)
```

- commonly used to update records to a maximum value or change a string to match an abbreviation from another table
```python
select_stmt = select([state_fact]).where(state_fact.columns.name == 'New York')
results = connection.execute(select_stmt).fetchall()
print(results)
print(results[0]['fips_state'])

update_stmt = update(state_fact).values(fips_state = 36)
update_stmt = update_stmt.where(state_fact.columns.name == 'New York')
update_results = connection.execute(update_stmt)

# Execute select_stmt again and fetch the new results
new_results = connection.execute(select_stmt).fetchmany()

# Print the new_results
print(new_results)

# Print the FIPS code for the first row of the new_results
print(new_results[0]['fips_state'])

# Build a statement to update the notes to 'The Wild West': stmt
stmt = update(state_fact).values(notes='The Wild West')

# Append a where clause to match the West census region records: stmt_west
stmt_west = stmt.where(state_fact.columns.census_region_name == 'West')

# Execute the statement: results
results = connection.execute(stmt_west)

# Print rowcount
print(results.rowcount)

```
## Correlated updates
```python
# Build a statement to select name from state_fact: fips_stmt
fips_stmt = select([state_fact.columns.name])

# Append a where clause to match the fips_state to flat_census fips_code: fips_stmt
fips_stmt = fips_stmt.where(
    state_fact.columns.fips_state == flat_census.columns.fips_code)

# Build an update statement to set the name to fips_stmt_where: update_stmt
update_stmt = update(flat_census).values(state_name=fips_stmt)

# Execute update_stmt: results
results = connection.execute(update_stmt)

# Print rowcount
print(results.rowcount)
```

# Delete data from a table
- done with the `delete` () statement
- `delete()` takes the table as an argument
- add `where()` clause to choose which rows to delete
- hard to undo - be careful!
```python
from sqlalchemy import delete
stmt = select([func.count(extra_employees.columns.id)])
conn.execute(stmt).scalar # return count
--------
3
--------
delete_stmt = delete(extra_employees)
result_proxy = connection.execute(delete_stmt)
result_proxy.rowcount # make sure to check against expected count
--------
3
```

### Delete specific rows
- build a `where()` clause that will select all the records you want to delete
```python
# Build a statement to count records using the sex column for Men ('M') age 36: count_stmt
count_stmt = select([func.count(census.columns.sex)]).where(
    and_(census.columns.sex == 'M',
         census.columns.age == 36)
)

# Execute the select statement and use the scalar() fetch method to save the record count
to_delete = connection.execute(count_stmt).scalar()

# Build a statement to delete records from the census table: delete_stmt
delete_stmt = delete(census)

# Append a where clause to target Men ('M') age 36: delete_stmt
delete_stmt = delete_stmt.where(
    and_(census.columns.sex == 'M',
         census.columns.age == 36)
)

# Execute the statement: results
results = connection.execute(delete_stmt)

# Print affected rowcount and to_delete record count, make sure they match
print(results.rowcount, to_delete)
----------
51 51
```
#### drop a table completely with `drop()`
- accepts engine as argument so it knows where to remove the table from
- won't remove it from metadata until the python process is restarted
```python
extra_employees.drop(engine)
print(extra_employees.exists(engine))
-------
False
```
- `drop_all()` method on MetaData
    - verify it worked with `eng.table_names()`

```python
values_list = []

# Iterate over the rows
for row in csv_reader:
    # Create a dictionary with the values
    data = {'state': row[0], 'sex': row[1], 'age': row[2], 'pop2000': row[3],
            'pop2008': row[4]}
    # Append the dictionary to the values list
    values_list.append(data)

# Import insert
from sqlalchemy import insert

# Build insert statement: stmt
stmt = insert(census)

# Use values_list to insert data: results
results = engine.execute(stmt, values_list)

# Print rowcount
print(results.rowcount)

# Import select and func
from sqlalchemy import select, func

# Select sex and average age weighted by 2000 population
stmt = select([(func.sum(census.columns.pop2000 * census.columns.age) 
  					/ func.sum(census.columns.pop2000)).label('average_age'),
               census.columns.sex
			  ])

# Group by sex
stmt = stmt.group_by(census.columns.sex)

# Execute the query and fetch all the results
results = connection.execute(stmt).fetchall()

# Print the sex and average age column for each result
for row in results:
    print(row.sex, row.average_age)

# import case, cast and Float from sqlalchemy
from sqlalchemy import case, cast, Float

# Build a query to calculate the percentage of women in 2000: stmt
stmt = select([census.columns.state,
    (func.sum(
        case([
            (census.columns.sex == 'F', census.columns.pop2000)
        ], else_=0)) /
     cast(func.sum(census.columns.pop2000), Float) * 100).label('percent_female')
])

# Group By state
stmt = stmt.group_by(census.columns.state)

# Execute the query and store the results: results
results = connection.execute(stmt).fetchall()

# Print the percentage
for result in results:
    print(result.state, result.percent_female)

# Build query to return state name and population difference from 2008 to 2000
stmt = select([census.columns.state,
     (census.columns.pop2008-census.columns.pop2000).label('pop_change')
])

# Group by State
stmt = stmt.group_by(census.columns.state)

# Order by Population Change
stmt = stmt.order_by(desc('pop_change'))

# Limit to top 10
stmt = stmt.limit(10)

# Use connection to execute the statement and fetch all results
results = connection.execute(stmt).fetchall()

# Print the state and population change for each record
for result in results:
    print('{}:{}'.format(result.state, result.pop_change))
```