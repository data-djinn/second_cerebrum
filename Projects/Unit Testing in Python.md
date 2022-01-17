[[Things all DevOps practictitioners do (WIP)]] [[Python]]

# Manual testing is inefficient

## life cycle of a function
- ![[Pasted image 20211018165112.png]]

- A function is tested after the first implementation and then any time the function is modified, which happens mainly when new bugs are found, new features are implemented or the code is refactored.
- unit tests automate the repetitive testing process & saves time

# Unit Testing Intro
### Step 1:
- create file
- **Use "test_" naming convention**
	- indicates to python that it conatins unit tests
	- also called "test modules"

### Step 2: Import pytest & your function
`import pytest
import my_function`

### Step 3: unit tests are python functions
`def test_my_function():"`

### Step 4: Assert statements
`assert {boolean expression}`

### Step 5: run script in the command line
- this way, you can modify your original function & run the same tests repeatedly

### Example:
```
# Import the pytest package
import pytest

# Import the function convert_to_int()
from preprocessing_helpers import convert_to_int

# Complete the unit test name by adding a prefix
def test_on_string_with_one_comma():
  # Complete the assert statement
  assert convert_to_int("2,081") == 2081
```
```
In [1]: !pytest test_convert_to_int.py
============================= test session starts =====================
platform linux -- Python 3.6.7, pytest-5.2.2, py-1.9.0, pluggy-0.13.1
Matplotlib: 3.1.1
Freetype: 2.6.1
rootdir: /tmp/tmp_ktxtzck
plugins: mock-1.11.2, mpl-0.10
collecting ... 
collected 1 item                                                               

test_convert_to_int.py F                                                 [100%]

========= FAILURES ============================
________________________ test_on_string_with_one_comma _________________________

    def test_on_string_with_one_comma():
>     assert convert_to_int("2,081") == 2081
E     AssertionError: assert '2081' == 2081
E      +  where '2081' = convert_to_int('2,081')

test_convert_to_int.py:8: AssertionError
============================== 1 failed in 0.20s =====================
```

## Understanding test result report
1. general info
2. test result
    - how many tests found to run
    - test module name, followed by result:
| Character | Meaning | When                                                                   | Action                        |
| --------- | ------- | ---------------------------------------------------------------------- | ----------------------------- |
| F         | Failure | an exception (assertion or otherwise) is raised when running unit test | Fix the function or unit test |
| .         | Passed  | no exception raised when running unit test                             | everything is fine!           |

3. information on failed tests
- Line raising the exception is marked by `>`
- line containing `where` displays return values
4. Test summary result
