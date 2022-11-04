[[Data Engineering]]
# Variables
- variables are needed to set values, e.g. `DECLARE @var data_type`
    - must start with `@`
    - variable data types:
        - `VARCHAR(n)`: variable-length text field
        - `INT`: integer values from -2,147,483,647 to 2,147,483,647
        - `DECIMAL(p, s)` or `NUMERIC(p, s)`:
            - `p`: total number of decimal digits that will be stored, both to the left & to the right of the decimal point
            - `s`: number of decimal digits that will be stored to the right of the decimal point
     - use `SET` or `SELECT` to assign value to variable
```sql
DECLARE @snack VARCHAR(10);
SET @snack = 'Cookies';
SELECT @snack;
```

# While loop
- `WHILE` evaluates a true or false condition
- after the `WHILE` statement, there should be a line with the `BEGIN` keyword
- Next include code to run until the `WHILE` condition is true
- after the loop, add the keyword `END`
- `BREAK` will cause an exit out of the loop
- `CONTINUE` will cause the loop to continue
```sql
DECLARE @ctr INT
SET @ctr = 1
WHILE @ctr < 10
    BEGIN
        SET @ctr = @ctr + 1
        -- do something
        IF @ctr = -1
            BREAK
    END
```

# Derived tables (subquery)
==query which is treated like a temporary table==
- always contained within the main query
- they are specified in the `FROM` clause
- can contain intermediate calculations to be used by the main query or different joins than in the main query

# Common table expressions (CTE)
- need to declare the return columns up front:
`with cte(field1, field2) as ...`

# Window Functions
==data within a table is processed as a group==
- Create the window with `OVER` clause
- `PARTITION BY` creates the frame
    - if you don't include `PARTITION BY`, the frame is the entire table
- to arrange the results, use `ORDER BY`
- allows aggregations to be created at the same time as the window
### Common window functions
- `FIRST_VALUE()` returns the first value in the window
- `LAST_VALUE()` returns the last value in the window
- `LEAD()` returns the value from the next row
- `LAG()` returns the value from the previous row
- `ROW_NUMBER()` sequentially numbers the rows in the window


# Integrity Constraints
1.  Attribute constraints (e.g. data types)
2.  Key constraints (primary keys)
3.  Referential integrity constraints, enforced through foreign keys

### Why constraints?
-   Constraints give the data structure
-   Constraints help with consistency, and thus data quality
-   Data quality is a business advantage / data science prerequisite
-   Enforcing is difficult, but PostgreSQL helps

### Postgres Data Types:
| name                    | aliases       | description                         |
| ----------------------- | ------------- | ----------------------------------- |
| bigint                  | int8          | signed eight-byte integer           |
| bigserial               | serial8       | autoincrementing eight-byte integer |
| bit [ n ]               |               | fixed-length bit string             |
| bit varying [ n ]       | varbit [ n ]  | variable-length bit string          |
| boolean                 | bool          | logical Boolean                     |
| box                     |               | rectangular box on a plane          |
| bytea                   |               | binary data ("byte array")          |
| character [ n ]         | char [ n ]    | fixed-length character string       |
| character varying [ n ] | varchar [ n ] | variable-length character string    |
| cidr                    |               | IPv4 or IPv6 network address        |


`CAST` function is used for on-the-fly calculation:
```sql
SELECT transaction_date, amount + CAST(fee AS integer) AS net_amount
FROM transactions
```

Data types:
-   Are enforced on columns ('attributes')
-   Define the so-called 'domain' of a column
-   Define what operations are possible
-   Enforce consistent storage of values

The most common types:
-   Text: character strings of any length
-   Varchar: [ (x) ]: a maximum of n characters
-   Char [ (x) ]: a fixed-length string of n characters
-   Boolean: can only take three states, e.g. TRUE, FALSE, and NULL (unkownn)
-   Date, time, and timestamp: various formats for date and time calculations
-   Numeric: arbitrary precision numbers, e.g. 3.1457
-   Integer: whole numbers in the range of -2147483648 and +2147483648