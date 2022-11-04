[[Things all DevOps practictitioners do (WIP)]] [[Python]]

# Manual testing is inefficient

## life cycle of a function
- ![[Pasted image 20211018165112.png]]

- A function is tested after the first implementation and then any time the function is modified, which happens mainly when new bugs are found, new features are implemented or the code is refactored.
- unit tests automate the repetitive testing process & saves time

# Unit Testing Intro
- a test is meant to assess the result of a particular behavior (function), and make sure that the results align with what is expected
	- behavior is the way in which some system/function **acts in response to a particular situation/input**
- test a small, independent piece of code: a "unit of work"(e.g. python function or class)
	-  if the function is made up of two inner functions that make some kind of sense on their own, you should have unit tests for those functions too
- In contrast, integration tests check if multiple units work well together when they're connected.
- Finally, **end-to-end tests** check the entire software/pipeline at once

- unit tests help us to isolate problems in our code
		- if we have 5000 unit tests testing our 20,000 statement app, and 1 is failing, it **should be very clear where the problem in our code is**
			- if our unit tests are really good, we should quickly be able to isolate the problem to the lines that the failing unit test "covers" and also be able to know what the code is doing wrong & what it should be doing instead
1. Arrange: prepare everything for our test
	- lining up the dominoes so that the function can do its thing in one, state-changing step
	- e.g. preparing objects, starting/killing services, entering records into a db, etc.
2. **ACT**: singlular, state-changing action (func/method) that kicks off the behavior we want to test
	- this behavior is what carries out of the changing of the system under test
	- we look at the resulting changed state to make a judgement about the behavior
3. **ASSERT**: check if the resulting state looks how we'd expect after the dust has settled
4. **CLEANUP**: test picks up after itself, so the other tests aren't being accidentally influenced by it

### Step 1:
- create file
- **Use "test_" naming convention**
	- indicates to python that it contains unit tests
	- also called "test modules"

### Step 2: Import pytest & your function
`import pytest`
`import my_function`

### Step 3: unit tests are python functions
`def test_my_function():"`

### Step 4: Assert statements
`assert {boolean expression}`

### Step 5: run script in the command line
- this way, you can modify your original function & run the same tests repeatedly

### Example:
```python
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


## other benefits of testing
#### Unit tests serve as documentation
- infer what each function based on the boolean `assert` statements
- type `!cat test_example.py` in the IPython console to view a test module
#### more trust
![[Pasted image 20220420105139.png]]
#### Reduced downtime
- if bad code is pushed to the repo, users will lose trust
- **Continuous Integration** runs all unit tests when all code is pushed

# Assert statements
`assert boolean_expression, message`
- if the assert statement passes, nothing is printed
- if it fails, the message should explain what failed
  - print values of any variable of choice that may be useful for debugging

### Beware of float values!
- due to how python stores floats, the return value may be off by .0000001
  - `assert 0.1 + 0.1 + 0.1 == 0.3` --> `False`
  - instead, use `pytest.approx()` to wrap expected return value:
  - `assert 0.1 + 0.1 + 0.1 == pytest.approx(0.3)` --> `True`
- `pytest.approx()` also works for numpy arrays containing floats
  - `asert np.array([0.1 + 0.1, 0.1 + 0.1 + 0.1]) == pytest.approx(np.array([0.2, 0.3]))`

## testing for exceptions instead of return values
```python
with pytest.raises(ValueError) as exception info # type of exception to check for
  raise ValueError
  # if context raises ValueError, silence it
  
  # if the code does not raise ValueError, raise Failed
```
- use this to test if the correct error is raised when e.g. incorrect parameter is passed to function
- within the context, `exception_info` will store the `ValueError`

## Well-tested function
*how many tests should be considered enough?*
**test for these argument types:**
    - bad arguments: function raises an exception instead of returning a value
    - special arguments:
        1. boundary values, the point along the spectrum of possible vals at which the arg is valid
        2. argument values that require special logic to produce return val (e.g. `0`)
    - normal arguments: test 2 or 3
![[Pasted image 20220420192407.png]]
# Test-driven development
- write tests **before the function is written in code**
  - this ensures that tests cannot be deprioritized or
- time for writing unit tests factored in implementation time
- requirements are clearer and implementation is easier - forces you to think about edge cases and the 3 argument types before building

# Test organization & execution
## Organization
![[Pasted image 20220420195933.png]]
- within each test module, it can be difficult to tell when the test for one func ends & another begins
- solve this with a `test` class - just a container for unit tests of the same function
```python
class TestMyFunc(object): # always put the argument object
  def test_example(self):
    ...
  
  def test_example_2(self):
    ...
    
class TestAnotherFunc(object):
  def test_another_example(self):
    ...
```

## Execution
`cd tests`
`pytest`
- recurses into directory subtree of `tests`
  - filenames starting with `test_` -> test module
  - Classnomes starting with `Test` -> test Class
  - function names starting with `test_` unit test 
 - `pytest` collects all `test_` unit tests & runs them all
![[Pasted image 20220420233855.png]]
![[Pasted image 20220420234446.png]]

#### The `-x` flag: stop after first failure
`pytest -x my_app.py`

- the above would run all tests
- to run a subset of tests:
`pytest example_dir/example_test.py`