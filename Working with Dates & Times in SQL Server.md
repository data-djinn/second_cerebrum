[[SQL Server]]

# Building a date
```
SELECT
  GETDATE() AS DateTime_LTz
  ,GETUTCDATE() AS DateTime_UTC
  ,SYSDATETIME() AS DateTime2_Ltz -- DateTime2 data type is double precision
  ,SYSUTCDATETIME() AS DateTime2_UTC -- double precision
  ,DATEPART(YEAR, @dt) AS Parsed_Year
  -- can also use DAY, MONTH, DAYOFYEAR, WEEKDAY, WEEKOFYEAR, MINUTE, SECOND, milli/nanoseconds too!
  ,DATENAME(MONTH, @dt) AS Parsed_Month_Name
  ,DATEADD(DAY, -1, @dt) AS NextDay,
  ,DATEADD(HOUR, -3, DATEADD(DAY, -4, @dt)) AS t_minus_4days_3hrs
  ,DATEDIFF(SECOND, @startTime, @endTime) AS seconds_elapsed
  -- can also use MINUTE, HOUR, DAY (returns int, rounded up!)
```

## Date math w/ leap years
### Getting info on 2012 Leap Year
```
DECLARE
	@LeapDay DATETIME2(7) = '2012-02-29 18:00:00';

-- Fill in the date parts and intervals as needed
SELECT
	DATEADD(DAY, -1, @LeapDay) AS PriorDay,
	DATEADD(DAY, 1, @LeapDay) AS NextDay,
    -- For leap years, we need to move 4 years, not just 1
	DATEADD(YEAR, -4, @LeapDay) AS PriorLeapYear, -- 2008 was also a leap year
	DATEADD(YEAR, 4, @LeapDay) AS NextLeapYear,
	DATEADD(YEAR, -1, @LeapDay) AS PriorYear;
-------------------------------------------------
```
| PriorDay            | NextDay             | PriorLeapYear       | NextLeapYear        | PriorYear           |
| ------------------- | ------------------- | ------------------- | ------------------- | ------------------- |
| 2012-02-28 18:00:00 | 2012-03-01 18:00:00 | 2008-02-29 18:00:00 | 2016-02-29 18:00:00 | 2011-02-28 18:00:00 | 
		

```
DECLARE
	@PostLeapDay DATETIME2(7) = '2012-03-01 18:00:00';

-- Fill in the date parts and intervals as needed
SELECT
	DATEADD(DAY, -1, @PostLeapDay) AS PriorDay,
	DATEADD(DAY, 1, @PostLeapDay) AS NextDay,
	DATEADD(YEAR, -4, @PostLeapDay) AS PriorLeapYear,
	DATEADD(YEAR, 4, @PostLeapDay) AS NextLeapYear,
	DATEADD(YEAR, -1, @PostLeapDay) AS PriorYear,
    -- Move 4 years forward and one day back
	DATEADD(DAY, -1, DATEADD(YEAR, 4, @PostLeapDay)) AS NextLeapDay,
    DATEADD(DAY, -2, @PostLeapDay) AS TwoDaysAgo;
-----------------------------------------------------
```
| PriorDay            | NextDay             | PriorLeapYear       | NextLeapYear        | PriorYear           | NextLeapDay         | TwoDaysAgo          |
| ------------------- | ------------------- | ------------------- | ------------------- | ------------------- | ------------------- | ------------------- |
| 2012-02-29 18:00:00 | 2012-03-02 18:00:00 | 2008-03-01 18:00:00 | 2016-03-01 18:00:00 | 2011-03-01 18:00:00 | 2016-02-29 18:00:00 | 2012-02-28 18:00:00 | 

```
DECLARE
	@PostLeapDay DATETIME2(7) = '2012-03-01 18:00:00',
    @TwoDaysAgo DATETIME2(7);

SELECT
	@TwoDaysAgo = DATEADD(DAY, -2, @PostLeapDay);

SELECT
	@TwoDaysAgo AS TwoDaysAgo,
	@PostLeapDay AS SomeTime,
    -- Fill in the appropriate function and date types
	DATEDIFF(DAY, @TwoDaysAgo, @PostLeapDay) AS DaysDifference,
	DATEDIFF(HOUR, @TwoDaysAgo, @PostLeapDay) AS HoursDifference,
	DATEDIFF(MINUTE, @TwoDaysAgo, @PostLeapDay) AS MinutesDifference;
```
| TwoDaysAgo          | SomeTime            | DaysDifference | HoursDifference | MinutesDifference |
| ------------------- | ------------------- | -------------- | --------------- | ----------------- |
| 2012-02-28 18:00:00 | 2012-03-01 18:00:00 | 2              | 48              | 2880              |

##### to round dates in SQL Server, you have to do it in conjunction w/ `DATEADD()`/`DATEDIFF()`
```
DECLARE
	@SomeTime DATETIME2(7) = '2018-06-14 16:29:36.2248991';

-- Fill in the appropriate functions and date parts
SELECT
	DATEADD(DAY, DATEDIFF(DAY, 0, @SomeTime), 0) AS RoundedToDay,
	DATEADD(HOUR, DATEDIFF(HOUR, 0, @SomeTime), 0) AS RoundedToHour,
	DATEADD(MINUTE, DATEDIFF(MINUTE, 0, @SomeTime), 0) AS RoundedToMinute;
-------------------------------------------------
```
| RoundedToDay        | RoundedToHour       | RoundedToMinute     |
| ------------------- | ------------------- | ------------------- |
| 2018-06-14 00:00:00 | 2018-06-14 16:00:00 | 2018-06-14 16:29:00 |
- `DATEDIFF()` returns an INTEGER data type, so  **it will overflow if you try to round to the second**

## `CAST()` vs. `CONVERT()`
### `CAST()`
- Supported since SQL Server 2000
- useful for converting one data type to another data type, including date types
- **no control over formatting from dates to strings**
- *ANSI SQL standard*
```
DECLARE
	@CubsWinWorldSeries DATETIME2(3) = '2016-11-03 00:30:29.245',
	@OlderDateType DATETIME = '2016-11-03 00:30:29.245';

SELECT
	-- Fill in the missing function calls
	CAST(@CubsWinWorldSeries AS DATE) AS CubsWinDateForm,
	CAST(@CubsWinWorldSeries AS NVARCHAR(30)) AS CubsWinStringForm,
	CAST(@OlderDateType AS DATE) AS OlderDateForm,
	CAST(@OlderDateType AS NVARCHAR(30)) AS OlderStringForm;
-------------------------------------------------------------------
```
| CubsWinDateForm | CubsWinStringForm       | OlderDateForm | OlderStringForm     |
| --------------- | ----------------------- | ------------- | ------------------- |
| 2016-11-03      | 2016-11-03 00:30:29.245 | 2016-11-03    | Nov  3 2016 12:30AM |
```
DECLARE
	@CubsWinWorldSeries DATETIME2(3) = '2016-11-03 00:30:29.245';

SELECT
	CAST(CAST(@CubsWinWorldSeries AS DATE) AS NVARCHAR(30)) AS DateStringForm;
---------------------------------------------------------------------------
```
| DateStringForm |
| -------------- |
| 2016-11-03     |

### `CONVERT()`
- Specific to T-SQL (SQL Server)
-  useful for converting date types
-  some control over formatting from dates to strings using the style parameter

##### `CONVERT()` styles
| Style code | Format                          |
| ---------- | ------------------------------- |
| 1/101      | US m/d/y                        |
| 3/103      | British/French d/m/y            |
| 4/104      | German d.m.y                    |
| 11/11      | Japanese y/m/d                  |
| 12/112     | ISO yyyymmdd                    |
| 20/120     | ODBC standard(121 for ms)       |
| 126        | ISO8601 yyyy-mm-dd hh:mi:ss.mmm |
| 127        | yyyy-mm-ddThh:mi:ss.mmmZ        |
- use ISO for files, unix time for systems, 127 for cloud, and local style code for report consumption
```
DECLARE
	@CubsWinWorldSeries DATETIME2(3) = '2016-11-03 00:30:29.245';

SELECT
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries, 0) AS DefaultForm,
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries, 3) AS UK_dmy,
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries, 1) AS US_mdy,
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries, 103) AS UK_dmyyyy,
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries, 101) AS US_mdyyyy;
-----------------------------------------------------------------
```
| DefaultForm         | UK_dmy   | US_mdy   | UK_dmyyyy  | US_mdyyyy  |
| ------------------- | -------- | -------- | ---------- | ---------- |
| Nov  3 2016 12:30AM | 03/11/16 | 11/03/16 | 03/11/2016 | 11/03/2016 |

### `FORMAT()`
- specific to T-SQL, SQL Server 2012+
- useful for formatting a date or number in a particular way for reporting
- much more control over formatting dates than `CAST()` or `CONVERT()`
- Uses .NET framework for conversion
- can be slow if you're processing many rows (single threaded) - avoid when you have many thousands of rows or more
![[Pasted image 20220131091207.png]]

```
DECLARE
	@Python3ReleaseDate DATETIME2(3) = '2008-12-03 19:45:00.033';
    
SELECT
	-- 20081203
	FORMAT(@Python3ReleaseDate, 'yyyyMMdd') AS F1,
	-- 2008-12-03
	FORMAT(@Python3ReleaseDate, 'yyyy-MM-dd') AS F2,
	-- Dec 03+2008 (the + is just a "+" character)
	FORMAT(@Python3ReleaseDate, 'MMM dd+yyyy') AS F3,
	-- 12 08 03 (month, two-digit year, day)
	FORMAT(@Python3ReleaseDate, 'MM yy dd') AS F4,
	-- 03 07:45 2008.00
    -- (day hour:minute year.second)
	FORMAT(@Python3ReleaseDate, 'dd hh:mm yyyy.ss') AS F5;
----------------------------------------------------------
```
| F1       | F2         | F3          | F4       | F5               |
| -------- | ---------- | ----------- | -------- | ---------------- |
| 20081203 | 2008-12-03 | Dec 03+2008 | 12 08 03 | 03 07:45 2008.00 | 

## Creating a calendar table
- table which stores date information for easy retrieval
  - set once at the beginning 
  - don't reinvent the wheel, look for calendar table scripts online
- simplify queries which perform complicated date math
- improve performance when filtering on date conditions (such as finding all things which happened on the fifth Tuesday of a month)
- ensure that different developers use the same sets of holidays in their queries

- **General columns**
  - date
  - day name
  - is weekend
  - is holiday
- **calendar Year**
  - calendar month
  - calendar quarter
  - calendar year
- **Fiscal Year**
  - Fiscal week of year
  - fiscal quarter
  - fiscal first day of year
- **Specialized columns**
  - Holiday name
  - Lunar details
  - ISO week of year

### `APPLY()`
- executes a function for each row in a result set
  - performs well
```
SELECT
  fy.FYStart
  , FiscalDayOfYear = DATEDIFF(DAY, fy.FYStart, d.[Date]) + 1
  , FiscalWeekOfYear = DATEDIFF(WEEK, fy.FYStart, d.[Date]) + 1
FROM dbo.Calendar d
  CROSS APPLY
  (
    SELECT FYStart = 
      DATEADD(MONTH, -6
        , DATEADD(YEAR,
          , DATEDIFF(YEAR, 0
            , DATEADD(MONTH, 6, d.date)
          )
        , 0)
      )
  ) fy;
```

#### examples of use:
```
SELECT
	ir.IncidentDate,
	c.FiscalDayOfYear,
	c.FiscalWeekOfYear
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
WHERE
    -- Incident type 4
	ir.IncidentTypeID = 4
    -- Fiscal year 2019
	AND c.FiscalYear = 2019
    -- Beyond fiscal week of year 30
	AND c.FiscalWeekOfYear > 30
    -- Only return weekends
	AND c.IsWeekend = 1;
```


# Dates from parts
### `DATEFROMPARTS(year, month, day)`
```
-- Create dates from component parts on the calendar table
SELECT TOP(1)
	DATEFROMPARTS(c.CalendarYear, c.CalendarMonth, c.Day) AS CalendarDate
FROM dbo.Calendar c
WHERE
	c.CalendarYear = 2017
ORDER BY
	c.FiscalDayOfYear ASC;
---------------------------
```
| CalendarDate  |
| ------------- |
| 2021  7-07-01 |


```
SELECT TOP(10)
	c.CalendarQuarterName,
	c.MonthName,
	c.CalendarDayOfYear
FROM dbo.Calendar c
WHERE
	-- Create dates from component parts
	DATEFROMPARTS(c.CalendarYear, c.CalendarMonth, c.Day) >= '2018-06-01'
	AND c.DayName = 'Tuesday'
ORDER BY
	c.FiscalYear,
	c.FiscalDayOfYear ASC;
------------------------
```
| CalendarQuarterName | MonthName | CalendarDayOfYear |
| ------------------- | --------- | ----------------- |
| Q2                  | June      | 156               |

- `TIMEFROMPARTS(hour, minute, second, fraction, precision)`
### `DATETIMEFROMPARTS(year, month, hour, minute, second, ms)` &  `DATETIME2FROMPARTS(year, month, day, houn, minute)`
```
SELECT
	-- Mark the date and time the lunar module touched down
    -- Use 24-hour notation for hours, so e.g., 9 PM is 21
	DATETIME2FROMPARTS(1969, 07, 20, 20, 17, 00, 000, 0) AS TheEagleHasLanded -- final 0 indicates UTC time
  , DATETIMEFROMPARTS(1969, 07, 21, 18, 54, 00, 000) AS MoonDeparture;
---------------------
```

### `DATETIMEOFFSETFROMPARTS(year, month, day, hour, minute, second, fraction, hour_offset, minute_offset, precision)`
##### Y2.038K
```
SELECT
	-- Fill in the millisecond PRIOR TO chaos
	DATETIMEOFFSETFROMPARTS(2038, 01, 19, 03, 14, 07, 999, 0, 0, 3) AS LastMoment,
    -- Fill in the date and time when we will experience the Y2.038K problem
    -- Then convert to the Eastern Standard Time time zone
	DATETIMEOFFSETFROMPARTS(2038, 01, 19, 03, 14, 08, 0, 0, 0, 3) AT TIME ZONE 'Eastern Standard Time' AS TimeForChaos;
--------------------
```
| LastMoment                         | TimeForChaos                       |
| ---------------------------------- | ---------------------------------- |
| 2038-01-19 03:14:07.9990000 +00:00 | 2038-01-18 22:14:08.0000000 -05:00 |


*Cautionary tales:*
- if any input value is `NULL` , the result will be `NULL`
- Cannot construct data type date, some of the arguments have values which are not valid
- cannot construct data type datetime2, some of the arguments have values which are not valid

# Translate strings to dates (from .csv)
### `CAST()`
- Fast
- ASI standard - *prefer this when possible*
- 