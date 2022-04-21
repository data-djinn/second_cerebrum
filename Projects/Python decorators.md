[[Python functions]]
### Closures:
==tuple of variables that are no longer in scope, but that a function needs in order to run==
`func.__cl`

```
def foo():
    a = 5
    def bar():
        print(a)
    return bar

func = foo()

func()
------
5
```
- Python attaches any non-local variable to the function as a `func.__closure__` tuple
- access via `func.__closure__[0].cell_contents`
- if original value is deleted, the value persists in the `.__closure__` object
```
def return_a_func(arg1, arg2):
  def new_func():
    print('arg1 was {}'.format(arg1))
    print('arg2 was {}'.format(arg2))
  return new_func
    
my_func = return_a_func(2, 17)

print(my_func.__closure__ is not None)
print(len(my_func.__closure__) == 2)

# Get the values of the variables in the closure
closure_values = [
  my_func.__closure__[i].cell_contents for i in range(2)
]
print(closure_values == [2, 17])
```

# Decorators
==wrapper that you can place around a function that can modify inputs/outputs/behavior of the function itself==
**equivalant**
![[Pasted image 20211210211150.png]]
	- Modify the behavior of a function without changing the code of the function itself
	
#### Functions are just another type of object
- Just like lists, floats, dicts, strings, ints, floats, etc
- Can be assigned to variables
- Add to a list
  - Call an element of a list, and pass it argument
- Add to a dictionary
- Do not include parentheses when assigning to a variable
- Need to reference the function object itself
	- Pass an argument to another function

Nested functions: define functions inside another function
Aka Inner functions, helper functions, child functions
	- Makes your code easier to read

Dict of functions:
	# Add the missing function references to the function map
	```function_map = {
	  'mean': mean,
	  'std': std,
	  'minimum': minimum,
	  'maximum': maximum
	}
	data = load_data()
	print(data)
	```
	`func_name = get_user_input()`

  # Call the chosen function and pass "data" as an argument
	`function_map[func_name](data)`
	
##### Nested function example:
```
	def create_math_function(func_name):
	  if func_name == 'add':
	    def add(a, b):
	      return a + b
	    return add
	  elif func_name == 'subtract':
	    # Define the subtract() function
	    def subtract(a, b):
	      return a - b
	    return subtract
	  else:
	    print("I don't know that one")
	    
	add = create_math_function('add')
	print('5 + 2 = {}'.format(add(5, 2)))
	
	subtract = create_math_function('subtract')
	print('5 - 2 = {}'.format(subtract(5, 2)))
```
	
