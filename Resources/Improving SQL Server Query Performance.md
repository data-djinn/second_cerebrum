[[SQL Server]]
# Misc. notes
- Median function is many more times expensive than median
##### MEAN (`AVG(l.SomeCol)`)
![[Pasted image 20220131122534.png]]
##### MEDIAN (`PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY l.SomeCol) OVER(PARTITION BY l.SomeCategory) AS Median`
![[Pasted image 20220131122501.png]]

|               | Median                   | Mean                    |
| ------------- | ------------------------ | ----------------------- |
| **Est. Cost** | 95.7%                    | 4.3%                    |
| **Duration**  | 68.5s                    | 0.37s (multi-threaded!) | 
| **CPU**       | 68.5s                    | 8.1s                    |
| **Reads**     | 72,500,000M (many joins) | 40,000K                 |
| **Writes**    | 88,000                   | 0                       |
- prefer simple filters in `WHERE`, make sure you're indexing for joins
# Improving legibility
- be consistent with formatting
- use UPPER CASE for all SQL keywords
- create a new line for each major keyword (`SELECT, FROM, WHERE`, etc)
- indent code:
  - sub-queries
  - `ON` statements
  - `AND`/`OR` conditions
  - to avoid long lines of code, i.e. multiple column names
- complete the query with a semi-colon `;`
- alias where required using `AS`
- use comment block up top to explain query at a high-level
### Aliasing
- avoid repetitive use of long table/column names
- easily identify joined tables and associated columns
- identify new columns
- identify sub-queries
- avoid ambiguity when columns from joined tables share the same name
- rename columns
### Query order
- order of the syntax written is different from the processing order in the database
**Syntax:**
1. SELECT
2. FROM
3. WHERE
4. ORDER BY
**Processing:**
1. FROM
2. ON
3. JOIN
4. WHERE
5. GROUP BY
6. HAVING

7. SELECT

8. DISTINCT
9. ORDER BY
10. TOP

# Filtering with WHERE & HAVING
## WHERE processing order
- after `FROM`, but before `SELECT`
  - **Any aliases declared in SELECT statement are thus not valid for `WHERE` filtering**
  - use sub-queries instead
  - simple filters very fast
  - alternatively, you can do the same calculation as the alias and use that to filter
    - *Note:* this will cause the calculation to be applied to every row in the query, increasing query time
    - **Applying functions to columns in the WHERE filter condition could increase query times**
    - keep the `WHERE` clause simple - avoid functions

## HAVING processing order
- **PREFER `WHERE` WHEN POSSIBLE**
  - this prevents unnecessary grouping calculations on unwanted rows
- **don't use having for individual or ungrouped rows**
- use `WHERE` for individual rows, and `HAVING` for numeric filters on grouped rows

## SELECT processing order
- `SELECT *` is bad for performance & returns duplicate rows with joins
  - only select the columns you need
- use `TOP 1 PERCENT` to return a portion of the rows
  - flip order by to ASC/DESC to see the "bottom" with `TOP`
    - `LIMIT`: postgresql
    - `ROWNUM` oracle
- **Use `ORDER BY` with caution - slows query performance

# Managing Duplicates
- caused by poor database design, poorly written queries, or both
- using `GROUP BY` has the same impact
- Is there another way? e.g. a lookup table?
- `UNION` removes duplicates, vs. `UNION ALL` does not
- **USE `DISTINCT()` WITH CAUTION - can significantly increase query time
- before using distinct, check if:
  - is there a lookup table we could use instead?
  - can we use GROUP BY instead? (e.g. we are aggregating)

# Subqueries & INTERSECT/EXCEPT & IN
- Processed first
### Uncorrelated
- does *not* contain a reference to the outer query
- *can* run independently from outer query
### Correlated
- does contain a reference to the outer query
- *can't* run independently from outer query
- often the same result can be achieved by using `INNER JOIN`
  - because correlated subquery must be run for each row in the outer query, it can be expensive (especially when outer query contains many rows
```
-- NOT IDEAL!
SELECT
	n.CountryName,
	 (SELECT MAX(c.Pop2017) -- Add 2017 population column
	 FROM Cities AS c 
                       -- Outer query country code column
	 WHERE c.CountryCode = n.Code2) AS BiggestCity
FROM Nations AS n; -- Outer query table

-- PREFFERED!
SELECT n.CountryName, 
       c.BiggestCity 
FROM Nations AS n
INNER JOIN  -- Join the Nations table and sub-query
    (SELECT CountryCode, 
     MAX(Pop2017) AS BiggestCity 
     FROM Cities
     GROUP BY CountryCode) AS c
ON n.Code2 = c.CountryCode; -- Add the joining columns
```

### Presence & absence
`INTERSECT` to find common records between 2 `SELECT` statements 
`EXCEPT` finds all the records **not** in common
- remove duplicates
- number & order of columns in the `SELECT` statement must be the same between queries
### Alternatives
`EXTISTS` filters the outer query when there is a matching datum between the outer query & subquery
 - evaluates `TRUE` or `FALSE`
**EXISTS stops searching the sub-query when the condition is true** 
vs 
**IN collects all the results from a sub-query before passing to the outer query**
  - If the columns in the sub-query being evaluated for a non-match contain NULL values, no results are returned

==prefer `Exists` to `IN` in sub-queries==
likewise `NOT EXISTS` vs. `NOT IN`
```
-- NOT IDEAL
SELECT WorldBankRegion,
       CountryName
FROM Nations
WHERE Code2 NOT IN -- Add the operator to compare queries
	(SELECT CountryCode -- Country code column
	 FROM Cities);

-- PREFERRED!
SELECT WorldBankRegion,
       CountryName,
	   Code2,
       Capital, -- Country capital column
	   Pop2017
FROM Nations AS n
WHERE NOT EXISTS -- Add the operator to compare queries
	(SELECT 1
	 FROM Cities AS c
	 WHERE n.Code2 = c.CountryCode); -- Columns being compared
```

Alternative to NOT EXTISTS & NOT IN is:
`LEFT OUTER JOIN WHERE dbo.my_col IS NULL`

# Performance tuning
## Time statistics (SQL Server only)
`STATISTICS TIME` - reports number of milliseconds required to parse, compile, and execute a query
  - **CPU time**: time taken by actual processors to process the query
    - should remain relatively consistent
    - less useful if server processors are running in parallel
  - **Elapsed time**: total duration of the query
    - Variable when analyzing query time stats, because it's sensitive to several factors, including:
      - load on the server
      - network bandwidth b/t client application & server
 ```
 SET STATISTICS TIME ON
 -- SOME STUFF
 SET STATISTICS TIME OFF
 ```
good practice to run the query multiple times & take the average
 
 ## Page read statistics
 ==examine the amount of disk/memory activity generated by the query==
- all data in memory & on disk are stored in 8 kilobyte "pages"
- one page can store many rows, or one value could span multiple pages
- a page can belong to only one table
- SQL server works with pages cached in memory
- if a page is not cached in memory, it is read from a disk and cached in memory
#### Logical reads
- measure of the number of 8 kb pages read from memory in order to process the query
- in general, the more pages that need to be read, the slower the query will run
```
SET STATISTICS IO ON
-- SUM STUFF
SET STATISTICS IO OFF
```
## Indexes
- Structure to improve speed of accessing data from a table
- used to locate data quickly without having to scan the entire table
- useful for improving performance of queries with filter conditions
- applied to table columns
- typically added by a database administrator
#### Clustered index (B-tree)
![[Pasted image 20220128165922.png]]
- like an English dictionary (words are ordered alphabetically)
- table data pages are `ordered by` the column(s) with the index
- only one allowed per table (can only order your pages one way)
- reduces the number of page reads --> speeds up search operations
#### Non-clustered index
- like a textbook index in the back (data is unordered, and the index points to the page numbers containing a search condition) 
- allows data to be unordered in the table data pages
- contains an ordered layer of index pointers to unordered table data pages
- a table can have more than one
- improves insert & update operation performance
## Execution plans
After passing the syntax/table/data checks, a query is passed to the **optimization phase**:
==evaluates multiple execution plans and selects the one optimized for the lowest cost==
- cost parameters include:
  - processor usage
  - memory usage
  - data page reads
Query is then passed to the execution engine...

Execution plan provides information on:
- whether indexes were used
- types of joins used
- location and relative cost of:
  - filter conditions
  - sorting
  - aggregations

![[Pasted image 20220128171325.png]]
- read from right to left
- width of each arrow indicates the size of the data passed to the next step
- hover over a step to see detailed execution plan
- *table scan is bad - clustered index seek is good*
- sort operator is costly

- in the real world, it's common to work with large, complex queries that can run for hours - this is where `STATISTICS` becomes invaluable