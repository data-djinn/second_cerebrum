[[Python]] [[MongoDB]]

- clean datasets to prep them for text mining or sentiment analysis
- process email content to feed a machine learning algorithm that decides whether an email is spam
- parse and extract specific data from a website to build a database
# Basic Concepts of string manipulation
### Stride
`my_string = 'Awesome day'`
`print(my_string[0:6:2])`
`-----------`
`Aeo`
`print(my_string[::-1])`
`-----------`
`yad emosewA`

### Splitting
`my_string = 'This string will be split`
`my_string.split(sep=' ', maxsplit=2)` (starts splitting on the left)
`my_string.rsplit(sep=' ', maxsplit=2)` (starts splitting on the right)

| escape sequence | char            |
| --------------- | --------------- |
| \n              | newline         |
| \r              | carriage return |

`my_string = 'This string will be split\n in two`
`my_string.splitlines()`
`->['This string will be split', 'in two']`

### Joining
- concat strings from list or other iterable
`my_list = ['this', 'would', 'become', 'a', 'string']`
`print(' '.join(my_list)) -> 'this would be a string'`

### stripping characters
- `.strip("char")` strips leading & trailing characters from left to right (use `char` arg to explicitly strip a specific char
`my_string = ' This string will be stripped\n'`
`my_string.strip() -> 'This string will be stripped`
`.rstrip()` & `.lstrip()` remove characters from the left & right end *only*

## Substrings
### Finding
- search target string for a specified substring
`my_string.find(substring, [start, end])` -->  index of where substring is found, -1 if not found
```
my_string = "Where's Waldo?"
my_string.find('Waldo', 0, 6) # will not be found, end is too soon
--------------------------------
-1
```

`.index()` method is identical, except it raises a `ValueError` exception instead of returning -1 if substring is not found

### Counting occurrences
- `string.count(substring, [start, end])` returns number of occurrences of the specified substring 
```
my_string = 'How many fruits do you have in your fruit basket?'
my_string.count('fruit') -> 2
my_string.count('fruit', 0, 16) -> 1
```

### Replacing substrings
- replace occurances of substring with new substring
`string.replace(old, new, [count])`
```
my_string = 'The red house is between the blue house and the old house'
my_string.replace('house', 'car', 2)
print(my_string) -> 'The red car is between the blue car and the old house'
```

# String formatting/manipulation
## by position
- insert a custom string or variable in predefined text:
```
custom_string = 'String formatting is {adjective1} and {adjective2}'
custom_string.format(adjective1=fun, adjective=easy) 
```
- if referencing values in a dict, don't use quotes in the key
#### Format specifier
- specify data type to be used: `{index:specifier}`
- `print('only {0:f}% of the {1:s} produced worldwide is {2:s}!'.format(0.5155675, 'data', 'analyzed')
- format datetimes:
- `print('Today's date is {:%Y-%m-%d %H:%M}'.format(datetime.now()))`
## f-strings: formatted string literals
- python 3.6+
- minimal syntax: add `f` prefect to string
- allow us to convert expressions into different types:
  - `!s`: string version
  - `!r`: string literal version (to print characters that are typically reserved for python usage, e.g. `/`
  - `!a`: same as `!r` but escape the non-ASCII characters
  - `e`: scientific notation
  - `d`: digit
  - `f`: float
  - `datetime`
```
name = "Python"
print(f'Python is called {name!r} due to a comedy series')
```

- allow us to perform inline operations
```
my_num = 4
my_multiplier = 7.00
print(f'{my_num:d} times {my_multiplier:.2f} is {my_num * my_multiplier}')
```

- can also call functions
```
def my_func(a, b):
  return a + b
  
print(f'If you sum up 10 and 20 the result is {my_func(10,20)}')
```
## Template method
- simpler syntax
- slower than f-strings
- limited: don't allow format specifiers
- good when working with externally formatted strings

### Basic syntax
```
from string import Template

my_string = Template('Data science has been called $identifier')
my_string.substitute(identifier='sexiest job of the 21st century')
```

### substitution
- use many `$identifier`
- use variables
```
from string import Template
job = 'Data science'
name = 'sexiest job of the 21st century'
my_string = Template('$title has been called $description')
my_string.substitute(title=job, description=name)
```

- use `${identifier}` when valid characters follow identifier
`my_string = Template('I find Python very ${noun}ing but my sister has lost $noun')`

- use `$$` to escape the dollar sign
- raises error when placeholder is missing
  - consider **safe substitution**
    - always tries to return a usable string
    - missing placeholders will appear in resulting string
```
favorite = dict(flavor = 'chocolate')
my_string = Template('I love $flavor $cake very much')
my_string.safe_substitute(favorite) -> 'I love chocolate $cake very much'
```

# Regular Expressions
==string containing a combination of normal characters and special metacharacters that describes patterns to find text or positions within a text==
`r'st\d\s\w{3,10}'
- always use `r` raw string designator
- **normal characters match themselves* (`st`)
- **metacharacters represent types of characters:**
  - `\d`: digit
  - `\D`: non-digit
  - `\s`: whitespace char
  - `\S`: non-whitespace char
  - `\w`: word char
  - `\W`: non-word char
- **or, "ideas" such as location or quantity:**
  - `{3,10}`: char immediately to the left (`\w`) should appear between 3 & 10 times
- use **re** module
- find all matches of a pattern: `re.findall(r'regex_pattern', string)`
- split string at each match: `re.split(r'regex_pattern', string)`
- replace one or many matches with a string: `re.sub(r'regex_pattern',new_string, string)`

## Repeated characters
- `re.search(regex_pattern, string)`
- use **quantifier**:
  - metacharacter that tells the regex engine how many times to match a character immediately to its left
  - `{n, m}`, where `n` is the number of times, `m` is max number of times (optional)
  - `+`: 1 or more times
  - `*`: 0 or more times
  - `?`: 0 or 1 times only
  
## Regex metacharacters
- `.match()` anchors the search at the beginning of the string, unlike `.search()`
- `.search()` will scan the string and return any matching substring
### Special Characters
- `.`: matches any char (except newline)
- `^`: start of the string
- `$`: anchors regex to the end of the string
- `\`: escape special characters
- `|`: OR operator (e.g. `soccer|football`)
- `[abc]`: set of characters (`^[abc]` inverts this function (anti-match)
  - `email_address = r"[A-Za-z0-9!#%&*\$\.]+@\w+\.com"`
  - `password = r"[a-zA-Z0-9*$#%!&.]{8,20}"`

## Greedy vs non-greedy matching
### Greedy
- **match as many characters as possible**
- ***returns the longest match***
- backtracks when too many characters match
- gives up characters one at a time
- standard quantifiers are greedy by default (`*`, `+`, `?`, `{num, num}`)
![[Pasted image 20220410172925.png]]
![[Pasted image 20220410174817.png]]

### Non-greedy (lazy)
- **match as few characters as needed**
- ***returns the shortest match***
- append `?` to greedy quantifiers to make them lazy
- backtracks when too few characters matched
- expands chars one at a time
![[Pasted image 20220410175239.png]]
![[Pasted image 20220410180009.png]]
##### Remove HTML tags
`string_notags = re.sub(r"<.+?>", "", string)`