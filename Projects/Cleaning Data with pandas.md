[[Python]] [[Data Engineering]] [[Data Quality]]
# Constraints
## Data type constraints
- `df.dtypes` for description of data type
- use `df.info()` & `df.describe()` for quick stats
- to string string chars and convert to `int`:
```
df['str_col'] = df['str_col'].str,strip('*')
df['int_col'] = df['str_col'].astype('int')

assert df['str_col'].dtype == 'int'
```
- use method `df.astype('category')` for non-arithmetical code values

## Data range constraints
How to handle:
- drop data
`movies.drop(movies[movies['avg_rating'] > 5]. index)
    - could accidentally drop useful information
- setting custom mins & maxs
`movies.loc[movies['avg_rating'] > 5, 'avg_rating'] = 5` (convert bad data to max)
- treat as missing & impute
- setting custom value depending on business assumptions
### Convert `object` data type to dates
`df['date'] = pd.to_datetime(df['object_field_w_date_vals']).dt.date`
- drop out-of-range-data:
  - via filtering:
  `df = df[df['date'] < dt.date.today()]`
  - via `.drop()`:
  `df.drop(df[df['subscription_date'] > dt.date.today()]`
-  hardcode dates with upper limit:
`df.loc[df['date'] > dt.date.today(), 'date'] = dt.date.today()`
## Uniqueness constraints
##### Duplicate values
- if **all** columns have the same values, it may be a bug/system design flaw
  - e.g. malformed join/merge
- if **most** columns have the same values, it may be data entry/human error

### How to find
`duplicates = df[df.duplicated()]`
- change the arguments of the `.duplicated()` method for more informative results:
  - `subset`: list of column names to check for duplication
  - `keep`: whether to keep **first** (`first`), **last** (`last`), or **all** (`False`) duplicate values
```
duplicates = df.duplicated(subset=['first_name', 'last_name', 'address'], keep=False)
df[duplicates].sort_values(by='first_name')
```

### `.groupby()` & `.agg()`
- lets you group by a set of columns and returns statistical values for specific columns when the aggregation is being performed
```
df = df.groupby(by=['first_name', 'last_name', 'address])\
  .agg({height':'max', 'weight': 'mean}).reset_index()
```

### How to fix
**`df.drop_duplicates()` method**
- same arguments as `.duplicated()`
# Text & categorical data quality
## Categorical & membership constraints
- **categorical data**: set of variables (usually integers) that represent pre-defined categories
- value inconsistency: they can't go beyond these predefined categories
  - if they do, may indicate **data entry** or **data parsing** errors
    - e.g. `married`, `Married`, `unmarried`, `not married`
    - check with: `df['col_in_question'].value_counts()` (Series only)
- collapsing too many categories to few:
  - collapsing continuous data into groups
##### fix capitalization errors 
`df['col'] = df['col'].str.upper() # or .lower()`
##### remove leading & trailing spaces
`df['col'] = df['col'].str.strip() # or .lower()`
##### Create categories/groups
```
demographics['income_group'] = pd.cut(demographics['household_income'],
  bins=[0,200000,500000, np.inf], labels=['0-200k', '200k-500k', '500k+'])
```

##### Map categories to fewer ones: [collapsing categories]
- `operating_system` col from: `'Microsoft', 'MacOS', 'IOS', 'Android', 'Linux'` to --> `'DesktopOS', 'MobileOS'`
```
mapping={'Microsoft':'DesktopOS', 'MacOS':'DesktopOS', 'Linux':'DesktopOS', 'IOS':'MobileOS', 'Android':'MobileOS'}
devices['operating_system'] = devices['operating_system'.replace(mapping)
devices['operating_system'].unique()
-----------------------------------
array(['DesktopOS', 'MobileOS'], dtype=object)
```

### How to fix
#### drop rows with incorrect categories
![[Pasted image 20220324151617.png]]
- keep a table of all possible values of categorical data
- identify with anti-join
- fix with inner joins
```
inconsitstent_categories = set(study_data['blood_type]).difference(categories['blood_type'])

inconsistent_rows = study_data['blood_type'].isin(inconsistent_categories)
#returns a series of boolean values that we then use to subset original df
inconsistent_data = study_data[inconsistent_rows] # returns inconsistent rows
consistent_data = study_data[~inconsistent_rows]
```

## Cleaning text data
![[Pasted image 20220325140649.png]]
### Common problems:
##### Data inconsistency
`phones['Phone number'] = phones['Phone number'].str.replace('+', '00')`
##### Fixed length violations
```
# store len of each row in 'phone number' col
digits = phones['Phone number'].str.len()
# Replace violations with NaN
phones.loc[digits < 10, 'Phone number'] = np.nan  
```
check your work:
```
sanity_check = phone['Phone number'].str.len()
assert sanity_check.min() >= 10
assert phone['Phone number'].str.contains('+|-'.any() == False # '|' is OR logic
```
you can use regex too!
`phones['Phone number'] = phones['Phone number'].str.replace(r'\D+', '')`
##### Typos
# Advanced data quality
## Uniformity
| column      | unit                          |
| ----------- | ----------------------------- |
| temperature | `32`deg. C == `89.6`deg. F    |
| weight      | `70`kg == `11` st.            |
| date        | `26-11-2019` == `26 Nov 2019` |
| money       | `$100` == `10763.90` Yen      |

#### look for anomalies:
```
import matplotlib.pyplot as plt
plt.scatter(x='Date', y='Temperature', data=temperatures)
plt.title('Temperature in C March 2019 - NYC')
plt.xlabel('Dates')
plt.ylabel('Temperature in Celsius')
plt.show()
```
![[Pasted image 20220326131441.png]]
circled data points are likely Fahrenheit

#### Fix anomalies:
```
temp_fah = temperatures.loc[temperatures['Temperature'] > 40, 'Temperature']
temp_cels = (temp_fah - 32) * (5/9)
temperatures.loc0[temperatures['Temperature' > 40, 'Temperature'] = temp_cels
```
```
# Find values of acct_cur that are equal to 'euro'
acct_eu = banking['acct_cur'] == 'euro'

# Convert acct_amount where it is in euro to dollars
banking.loc[acct_eu, 'acct_amount'] = banking.loc[acct_eu, 'acct_amount'] * 1.1

# Unify acct_cur column by changing 'euro' values to 'dollar'
banking.loc[acct_eu, 'acct_cur'] = 'dollar'

# Assert that only dollar currency remains
assert banking['acct_cur'].unique() == 'dollar'
```

#### Fix date anamolies with `datetime`
| date               | `datetime` format |
| ------------------ | ----------------- |
| 25-12-2019         | `%d-%m-%Y`        |
| December 23th 2019 | `%c`              |
| 12-25-2019         | `%m-%d-%Y`        |

`pandas.to_datetime()`
- can recognize most formats automatically! 
- will error out if the date format is not uniform
```
birthdays['Birthday'] = pd.to_datetime(birthdays['Birthday']
  ,infer_datetime_format=True
  ,errors='coerce'
)
```
alternatively:
`birthdays['Birthday']' = birthdays['Birthday'].dt.strftime('%d-%m-%Y')`
#### ambiguous dates like `2019-03-08`
- convert to `NA` and treat accordingly
- infer format by understanding data source
- infer format by understanding previous and subsequent data in dataframe

## Cross-field validation
==use multiple fields in a dataset to sanity check data integrity==
Check parts against total:
```
sum_of_tickets = flights[['economy_class', 'business_class', 'first_class']].sum(axis=1)
passengers_equ = sum_classes == flights['total_passengers']
inconsistent_pass = flights[~passenger_equ]
consistent_pass = flights[passenger_equ]
```
check age against birthday:
```
import pandas as pd
import datetime as dt

users['Birthday'] = pd.to_datetime(users['Birthday'])
today = dt.date.today()
age_manual = today.year - users['Birthday'].dt.year
age_equ = age_manual == users['Age']
inconsistent_age = users[~age_equ]
consistent_age = users[age_equ]
```
#### what to do with inconsistencies?
- drop data
- set to missing and impute
- apply rules from domain knowledge

## Data completeness
##### Find missing data
- occurs when no data value is stored for a variable in an observation
- can be represented as `NA`, `nan`, `0`, `.`, etc.
`binary_isNull_array = df.isna()`
`null_counts = binary_isNull_array.sum()`  

##### Visualize missing data
```
import missingno as msno
import matplotlib.pyplot as plt
msno.matrix(airquality)
plt.show()
```
![[Pasted image 20220328111123.png]]
`missing = airquality[airquality['CO2'].isna()]`
`complete = airquality[~airquality['CO2'].isna()]`
![[Pasted image 20220328111427.png]]

```
sorted_airquality = airquality.sort_values(by = 'Temperature')
msno.matrix(sorted_airquality)
plt.show()
```
![[Pasted image 20220328111350.png]]

### Missingness types
##### Missing Completely at Random (MCAR)
- no systematic relationship between missing data and other values
  - e.g. data entry errors

##### Missing at Random (MAR)
- Systematic relationship between missing data and other observed values
  - e.g. missing ozone data for high temperatures

##### Missing not at random (MNAR)
- Systematic relationship between missing data and unobserved values
  - e.g. missing temperature values for high temperatures (thermometer broke!)

### How to deal with missing data
##### Simple approaches:
- drop missing data
`airquality_dropped = airquality.dropna(subset = ['CO2'])`
- impute with statistical measures (mean, median, mode)
 `co2_mean = airquality['CO2'].mean()`
 `airquality_imputed = airquality.fillna({'CO2': co2_mean})`
##### Complex approaches:
- imputing using algorithm
- imputing with ML model
# Record linkage
## Comparing strings
### Minimum edit distance
- least possible amount of steps needed to transition from one string to another
  - e.g. intention <--> execution
    1. delete I
    2. add C between E & N
    3. Substitute N with E
    4. Substitute T with X
    5. Substitute N with U
    - min edit distance = 5
#### min edit distance algos
##### packages:
- nltk
- **fuzzywuzzy**
- textdistance
| algorithm          | operations                                       |
| ------------------ | ------------------------------------------------ |
| Dameau-Levenshtein | insertion, substitution, deletion, transposition |
| levenshtein        | insertion, substitution, deletion                |
| Hamming            | substitution only                                |
| Jaro distance      | transposition only                               |
```
from fuzzywuzzy import fuzz

fuzz.WRatio('Reeding', 'Reading')
---------------------------------
86
```
- score from 0 to 100
  - 0 means no similarity
  - 100 is a perfect match
- `WRatio` is highly robust against partial string comparisons with different ordering

```
from fuzzywuzzy import process

string = "Houston Rockets vs Los Angeles Lakers"
choices = pd.Series([
  'Rockets vs Lakers'
  ,'Lakers vs Rockets'
  ,'Houston vs Los Angeles'
  ,'Heat vs Bulls'
])

process.extract(string, choices, limit = 2)
------------------------------------------
[('Rockets vs Lakers', 86, 0), ('Lakers vs Rockets', 86, 1)]
```

#### [collapsing categories] using string similarity
remember `.replace()`?
- use string similarity if there are too many variations!
![[Pasted image 20220331085944.png]]
![[Pasted image 20220331085959.png]]
```
for each state in categories['state']:
  # find potential matches in states with typoes
  matches = process.extract(state, survey['state'], limit = survey.shape[0])
  for potential_match in matches:
    if potential_match[1] >= 80
      # replace typo with correct category
      survey.loc[survey['state'] == potential_match[0], 'state'] = state
```

## Generating pairs
### `recordlinkage` pkg
act of linking data from different sources regarding the same entity
- use when regular joins won't work
#### process
  - clean 2 or more dataframes
  - generate pairs of potentially matching records
  - compare/score these pairs based on string similarity & other metrics
  - link them

- cartesian product (all possible pairs) grow exponentially the more data you have
- can become unmanageable quickly 
### "Block" to create pairs based on a matching column (like state)
```
import recordlinkage

indexer = recordlinkage.Index()

indexer.block('state')
pairs = indexer.index(census_a, census_B)
```
![[Pasted image 20220331101525.png]]

```
compare_cl = recordlinkage.Compare()

# Find exact matches for pairs of date_of_birth and state
compare_cl.exact('date_of_birth', 'date_of_birth', label='date_of_birth')
compare_cl.exact('state', 'state', label='state')

# Find similar matches for pairs of surname and address_1 using string similarity
compare_cl.string('surname', 'surname', threshold=0.85, label='surname')
compare_cl.string('address_1', 'address_1', threshold=0.85, label='address_1')

potential_matches = compare_cl.compute(pairs, census_a, census_B)
potential_matches.sum(axis=1)>= 3 # need all 3 fields to match!
```
![[Pasted image 20220331161249.png]]
![[Pasted image 20220331161302.png]]
## Linking DFs
![[Pasted image 20220331182121.png]]
![[Pasted image 20220331182210.png]]  
`full_census = census_a.append(census_b_new)`