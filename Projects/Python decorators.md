[[Python]] [[Python functions]]
# Closures
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