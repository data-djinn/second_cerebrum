[[Databricks Certified Associate Developer for Apache Spark]] [[spark]]

# date functions
| function         | description                                                                                                                                                                                      |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `date_format`    | converts a date/timestamp/string to a value of string in the format specified by the date format given by the second argument                                                                    |
| `add_months`     | returns the date that is numMonths after startDate                                                                                                                                               |
| `dayofweek`      | extracts the day of the week as an integer from a given date/timestamp/string                                                                                                                    |
| `from_unixtime`  | converts the number of seconds from unix epoch (1970-01-01 00:00:00 UTC) to a string representing the timestamp of that moment in the current system time zone in the yyyy-MM-dd HH:mm:ss format |
| `minute`         | Extracts the minutes as an integer from a given date/timestamp/string                                                                                                                            |
| `unix_timestamp` | Converts time string with given pattern to Unix timestamp (in seconds)                                                                                                                           |

[Datetime patterns - Spark 3.2.1 Documentation (apache.org)](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html#:~:text=Spark%20uses%20pattern%20letters%20in%20the%20following%20table,07%3B%20Jul%3B%20July%20%2022%20more%20rows%20)

