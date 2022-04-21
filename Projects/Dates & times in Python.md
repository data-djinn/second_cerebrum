[[Python]]

# Dates and calendars
- python has a native `Date` class, helping to:
  - figure out duration between dates
  - order from earliest to latest via `sorted(my_list_of_dates)`
  - know which day of week/year
  - filter by date
```
from datetime import date

two_hurricane_dates = [date(2016, 10, 7), date(2017, 6, 21)]
# Attributes
year = two_hurricane_dates[0].year
month = two_hurricane_dates[0].month
day = two_hurricane_dates[0].day
# Methods
weekday = two_hurricane_dates[0].weekday() # 0 = Monday!
```

## Math with Dates
```
from datetime import date, timedelta

l = [date(2016, 10, 7), date(2017, 6, 21)]
min(l) -> 2016-10-07
delta = l[1] - l[0] -> type(timedelta)
td = timedelta(days=29)
l[0] + td -> 2016-11-04
```
- be careful reaching 100s of years into the past - the calendar system has changed since then

## Dates <--> strings

# Combining Dates and Times
# Time Zones & Daylight Savings Time
# Dates & Times in Pandas