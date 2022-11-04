[[Python]]
# DocStrings
`Function.__doc__`
	- Doc property

Use functions to avoid repetition
- Avoid copy mistakes

#### Do one thing per function
- Code becomes more flexible
- More easily understood
- Simpler to test/debug
- Easier to change

==Refactoring is improving code by changing one small thing at a time==

### Pass by assignment 
| immutable | mutable         |
| --------- | --------------- |
| int       | list            |
| float     | dict            |
| bool      | set             |
| string    | bytearray       |
| bytes     | objects         |
| tuple     | functions       |
| frozenset | everything else |
| None          |                 |

```python
# Use an immutable variable for the default argument
def better_add_column(values, df=None):
  """Add a column of `values` to a DataFrame `df`.
  The column will be named "col_<n>" where "n" is
  the numerical index of the column.
  Args:
    values (iterable): The values of the new column
    df (DataFrame, optional): The DataFrame to update.
      If no DataFrame is passed, one is created by default.
  Returns:
    DataFrame
  """
  # Update the function to create a default DataFrame
  if df is None:
    df = pandas.DataFrame()
  df['col_{}'.format(len(df.columns))] = values
  return df
```
- When you need to set a mutable variable as a default argument, always use None and then set the value in the body of the function. This prevents unexpected behavior like adding multiple columns if you call the function more than once



# Using context managers

Two ways to define a context managers
	- Class based
	- Function based
```python
		@contextlib.contextmanager
		def my_context():
			#set up
			Yield
			#clean up
```
1. Define a function
2. (optional) add any set up code your context needs
3. Use the "yield" keyword
    a. Like a return statement, but indicates the function will continue
4. (optional) add any teardown code your context needs
5. Add the `@contextlib.contextmanager` decorator
	
	
### Add a decorator that will make timer() a context manager
	@contextlib.contextmanager
	def timer():
	  """Time the execution of a context block.
	
	  Yields:
	    None
	  """
	  start = time.time()
	  # Send control back to the context block
	  yield timer()
	  end = time.time()
	  print('Elapsed: {:.2f}s'.format(end - start))
	
	with timer():
	  print('This should take approximately 0.25 seconds')
	  time.sleep(0.25)

## Context manager patterns
| open    | close    |
| ------- | -------- |
| lock    | release  |
| change  | reset    |
| enter   | exit     |
| start   | stop     |
| setup   | teardown |
| connect | disconnect         |
