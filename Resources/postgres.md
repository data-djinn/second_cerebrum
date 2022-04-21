[[Database Design]]

# Subqueries
- can be in any part of a query
## `WHERE`
- Can return:
    - scalar quantities (e.g. `3.14159`, `-2`, `0.001`)
    - lists (`id = (12, 25, 392, 401, 939)`)
    - tables
- useful for intermediary transformations or filters
- compare groups to summarized values
- reshaping data
- combining data that can't be joined

### `FROM`
- Restructure & transform your data
  - from long to wide before selecting
  - prefiltering data
- calculating aggregates of aggregaes
- multiple subqueries in one `FROM` clause - **join them!**
- join subqueriest to a table in `FROM`

### `SELECT`
- Returns a **single value only**
  - include aggregate values to compare against individual values of main query
- mathematical calculations
  - e.g. deviation from the average
- Properly place filters! (usually the same filters in query & subquery)


## best practices
- Line up SELECT, FROM, WHERE, GROUP BY
- Annotate with `/* comments */` (or `--`)
- Indent your entire subquery
- Subqueries require computing power!
  - *How big is your DB?*
  -  *How big is the table you're querying from?*
- Is the subquery actually necessary? **performance hit**

## Correlated & nested subqueries
- uses values from the outer query to generate a result - **dependent on main query**
- **re-run for every row generated in the final dataset** - slows performance significantly

## When to use what technique
### Join
- Co mbine 2+ tables
  - Simple operations/aggregations
### Correlated Subqueries
- Match subqueries & tables
  -  Avoid limits of joins
    -   E.g. can't join 2 columns to a single column in another 
- Simplify syntax
- **High processing time**

### Multiple/nested subqueries
- Multi-step transformations
  - Improve accuracy and reproducibility

### Common Table Expressions
- Organize subqueries sequentially
- Can reference other CTEs
Large number of disparate information


# Window partitions
- calculate separate values for different categories
- calculated different calcs in the same column
- `AVG(home_goal) OVER(PARTITION BY season)
- partition by one or more columns
- can partition aggregate columns, ranks, etc
## Sliding windows
- perform calcs relative to the current row
- can be used to calculate running totals, sums, averages, etc.
- can be partitioned by one or more columns
`ROWS BETWEEN <start> AND <finish>
  - `PRECEDING`: # of rows **before current row**
  - `FOLLOWING`: # of rows **after current row**
  - `UNBOUNDED PRECEDING`: every row since beginning of dataset
  - `UNBOUNDED FOLLOWING`: every row up to end of dataset
  - `CURRENT ROW`: stop calculating at the current row
# Variables
`DECLARE @var_name var_data_type`
### Types:
  - `VARCHAR(n)`
  - `INT`
  - `DECIMAL(p,s)` or `NUMERIC(p,s)`
    - p: total # of decimal digits that will be stored, both to the left & the right of the decimal point
    - s: number of decimal digits that will be stored to the right of the decimal point
`SET @var_name = 'whatevs'`
- need a separate `SELECT` statement to show the value

# `WHILE` loops
- evaluates a boolean condition
- `BEGIN` keyword required after the `WHILE` clause
- `END` keyword also required after the code to be evaluated
- `BREAK` exits the while loop
- `CONTINUE` will 