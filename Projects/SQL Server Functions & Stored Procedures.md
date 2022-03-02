[[SQL Server]] [[Data Engineering]]
# Variables 
```DECLARE @ShiftStartTime AS time = '08:00 AM'

-- Create @StartDate
DECLARE @StartDate AS date

-- Set StartDate to the first StartDate from CapitalBikeShare
 SET 
	@StartDate = (
    	SELECT TOP 1  StartDate
    	FROM CapitalBikeShare 
    	ORDER BY StartDate ASC
		)

-- Create ShiftStartDateTime
DECLARE @ShiftStartDateTime AS datetime

-- Cast StartDate and ShiftStartTime to datetime data types
SET @ShiftStartDateTime = CAST(@StartDate AS datetime) + CAST(@ShiftStartTime AS datetime) 

SELECT @ShiftStartDateTime
```
- think of variables like lockers:
  - variable name is the name on the locker
  - size of the locker depends on the datatype used
  - variable value = locker's contents
 # User Defined Functions (UDFs)
 - routines that:
     - can accept input parameters
     - perform an action
     - return result (single scalar value or table)
 - reduce execution time
 - reduce network traffic
 - allow for modular programming
     - separates functionality into independent, interchangeable modules
     - allows code reuse
     - improves code readability
```
CREATE FUNCTION GetTomorrow()
    RETURNS date AS BEGIN
        RETURN (SELECT DATEADD(day, 1, GETDATE()))
END

CREATE FUNCTION SumRideHrsSingleDay (@DateParm date)
RETURNS numeric
AS
BEGIN
RETURN
(SELECT SUM(DATEDIFF(second, StartDate, EndDate))/3600
FROM CapitalBikeShare
WHERE CAST(StartDate AS date) = @DateParm)
END
```

## Table valued UDFs
#### Inline table valued functions (ITVF)
- no begin/end block required if the function body is a single statement

| inline                       | multi-statement                       |
| ---------------------------- | ------------------------------------- |
| RETURN results of SELECT     | DECLARE table variable to be returned |
| table column names in SELECT |                                       |
| no table variable            |                                       |
| no BEGIN END needed          | BEGIN END block required              |
| no INSERT                    | INSERT data into table variable       |
| ==faster performance==       |                                       |

```
-- Create the function
CREATE FUNCTION CountTripAvgDuration (@Month CHAR(2), @Year CHAR(4))
-- Specify return variable
RETURNS @DailyTripStats TABLE(
	TripDate	date,
	TripCount	int,
	AvgDuration	numeric)
AS
BEGIN
-- Insert query results into @DailyTripStats
INSERT @DailyTripStats
SELECT
    -- Cast StartDate as a date
	CAST(StartDate AS date),
    COUNT(ID),
    AVG(Duration)
FROM CapitalBikeShare
WHERE
	DATEPART(month, StartDate) = @Month AND
    DATEPART(year, StartDate) = @Year
-- Group by StartDate as a date
GROUP BY CAST(StartDate AS date)
-- Return
RETURN
END
```

# UDFs in action
- execute scalar with SELECT
`SELECT dbo.GetTomorrow()``
- execute scalar with EXEC & store result
```
DECLARE @TotalRideHrs AS numeric
EXEC @TotalRideHrs = dbo.GetRideHrsOneDay @DateParm = '1/15/2017'
SELECT
    'Total Ride Hours for 1/15/2017:',
    @TotalRideHrs
```
- it's possible to use UDFs in the where clause, but it can impact performance
- Declare table variables:
```
-- Create @StationStats
DECLARE @StationStats Table(
	StartStation nvarchar(100), 
	RideCount int, 
	TotalDuration numeric)
-- Populate @StationStats with the results of the function
INSERT INTO @StationStats 
SELECT TOP 10 *
-- Execute SumStationStats with 3/15/2018
FROM dbo.SumStationStats('3/15/2018') 
ORDER BY RideCount DESC
-- Select all the records from @StationStats
SELECT * 
FROM @StationStats
```

## Maintaning UDFs
- `ALTER` keyword
- `CREATE OR ALTER`
- Determinism improves performance
    - a function is deterministic when it returns the same result given:
        - the same input parameters
        - the same database state
```
SELECT  
    OBJECTPROPERTY(
        OBJECT_ID('[dbo].[GetRideHrsOneDay]'),
        'IsDeterministic'
    )
```
returns `1` or `0` (T/F)
- when a function is deterministic, SQL Server can index the results
- review if native SQL functions are deterministic

## Schemabinding
==specifies the schema is bound to the database object that it references==
==prevents changes to the schema if schema bound objects are referencing it==
```
-- Update SumStationStats
CREATE OR ALTER FUNCTION dbo.SumStationStats(@EndDate AS date)
-- Enable SCHEMABINDING
RETURNS TABLE WITH SCHEMABINDING
AS
RETURN
SELECT
	StartStation,
    COUNT(ID) AS RideCount,
    SUM(DURATION) AS TotalDuration
FROM dbo.CapitalBikeShare
-- Cast EndDate as date and compare to @EndDate
WHERE CAST(EndDate AS Date) = @EndDate
GROUP BY StartStation;
```

# Stored procedures
Routines that:
- accept input parameters
- perform actions (`EXECUTE`,`SELECT`, `INSERT`, `UPDATE`, `DELETE`, and call other stored procedures)
- return status (success or failure)
- return output parameters

- reduce execution time
- reduce network traffic
- allow for modular programming
- improved security - prevent SQL injection attacks

| UDFs                              | Stored Procedures                            |
| --------------------------------- | -------------------------------------------- |
| must return values (incl. tables) | return values are optional - no table values |
| embedded `SELECT` execute allowed | cannot embed `SELECT` to execute             |
| No output parameters              | return output parameters & status            |
| no `INSERT`, `UPDATE`, `DELETE`   | `INSERT`, `UPDATE`, `DELETE` allowed         |
| Cannot execute SPs                | Can execute function & SPs                   |
| No error handling                 | Error Handling with `TRY ... CATCH`          |

- *use `SET NOCOUNT ON`* prevents SQL from returning the number of rows to the caller
    - considered best practice, unless the caller expects this
- `RETURN` value is optional

| Output parameters                 | Return values                                    |
| --------------------------------- | ------------------------------------------------ |
| can be any data type              | used to indicate success or failure              |
| can declare multiple per SP       | integer data type only                           |
| cannot be table-valued parameters | 0 indicates success & non-zero indicates failure |

```
CREATE PROCEDURE dbo.cuspSumRideHrsSingleDay
	@DateParm date,
	@RideHrsOut numeric OUTPUT
AS
SET NOCOUNT ON
BEGIN
SELECT
	@RideHrsOut = SUM(DATEDIFF(second, StartDate, EndDate))/3600
FROM CapitalBikeShare
WHERE CAST(StartDate AS date) = @DateParm
RETURN
END
```

# Create, Read, Update, & Delete (CRUD)
#### N-tier architecture:
**Datasource** <--> **Data access layer** <--> **Business Logic Layer** <--> **Presentation Layer**
- stored procedures are stored within the data access layer
- data access layer can only access data source via stored procedures
    - Decouples SQL code from other application layers
    - **Improves Security**, prevents sql injection attacks
    - **Improves Performance**, database caches execution plan of stored procedures
- Use `dbo.cusp_TableNameAction` prefix to mark a user-generated stored procedure (as opposed to system-generated) (replace `Action` with create/read/update/delete
```
CREATE PROCEDURE dbo.cusp_RideSummaryCreate 
    (@DateParm date, @RideHrsParm numeric)
AS
BEGIN
SET NOCOUNT ON
INSERT INTO dbo.RideSummary(Date, RideHours)
VALUES(@DateParm, @RideHrsParm) 

SELECT
	Date,
    RideHours   
FROM dbo.RideSummary
WHERE Date = @DateParm
END;


CREATE PROCEDURE dbo.cuspRideSummaryUpdate
	(@Date date,
     @RideHrs numeric(18,0))
AS
BEGIN
SET NOCOUNT ON
UPDATE RideSummary
SET
	Date = @Date,
    RideHours = @RideHrs
WHERE Date = @Date
END;

CREATE PROCEDURE dbo.cuspRideSummaryDelete
	(@DateParm date,
     @RowCountOut int OUTPUT)
AS
BEGIN
DELETE FROM dbo.RideSummary
WHERE Date = @DateParm
SET @RowCountOut = @@ROWCOUNT
END;
```

# Ways to EXEC(ute)
- no output or return value
- Store return value
- with output parameter
- with output parameter & store return value
- store result set

```
-- *With output parameter* --
DECLARE @RideHrs AS numeric(18,0)
EXEC dbo.cuspSumRideHrsSingleDay
	@DateParm = '3/1/2018',
	@RideHrsOut = @RideHrs OUTPUT
SELECT @RideHrs AS RideHours;


-- *With return value* --
DECLARE @ReturnStatus AS int
EXEC @ReturnStatus = dbo.cuspRideSummaryUpdate
	@DateParm = '3/1/2018',
	@RideHrs = 300

SELECT
	@ReturnStatus AS ReturnStatus,
    Date,
    RideHours
FROM dbo.RideSummary 
WHERE DATE = '3/1/2018';


-- *With output parameter & return value* --
DECLARE @ReturnStatus AS int
DECLARE @RowCount AS int

EXECUTE @ReturnStatus = dbo.cuspRideSummaryDelete 
	@DateParm = '3/1/2018',
	@RowCountOut = @RowCount OUTPUT

SELECT
	@ReturnStatus AS ReturnStatus,
    @RowCount AS 'RowCount';
```

# TRY & CATCH errors
==anticipate, detect, & resolve resolution of errors==
- maintains normal flow of execution
- integrated into initial query design
- without error handling:
    - sudden shutdown or halts of execution
    - generic error messages without helpful context are provided
```
CREATE OR ALTER PROCEDURE dbo.cuspRideSummaryDelete
	@DateParm nvarchar(30),
	@Error nvarchar(max) = NULL OUTPUT
AS
SET NOCOUNT ON
BEGIN
  BEGIN TRY
      DELETE FROM RideSummary
      WHERE Date = @DateParm
  END TRY 
  BEGIN CATCH
		SET @Error = 
		'Error_Number: '+ CAST(Error_Number() AS VARCHAR) +
		'Error_Severity: '+ CAST(Error_Severity() AS VARCHAR) +
		'Error_State: ' + CAST(Error_State() AS VARCHAR) + 
		'Error_Message: ' + Error_Message() + 
		'Error_Line: ' + CAST(Error_Line() AS VARCHAR)
  END CATCH
END;

-- *Catch error* --
DECLARE @ReturnCode AS INT
DECLARE @ErrorOut AS nvarchar(max)
EXECUTE @ReturnCode = dbo.cuspRideSummaryDelete
	@DateParm = '1/32/2018',
	@Error = @ErrorOut OUTPUT
SELECT
	@ReturnCode AS ReturnCode,
    @ErrorOut AS ErrorMessage;
```

# Imputation
