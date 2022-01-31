[[Python]]

# What is OOP?
- code as interaction of objects
    - great for building frameworks and tools
    - **maintainable & reuseable code!!**
- compare to common "procedural style" of programming: 1-->2-->3
    - series of steps
    - good for data analysis

# Core Principles of OOP
### Inheritance
- extending functionality of existing code
### Polymorphism
- creating a unified interface
### Encapsulation
- bundling of data and methods

## Objects
 - ==data structure incorporating information about state & behavior==
     - class **customer**:
         - information (email, phone)
         - behavior (place order, cancel order)
     - **encapsulation**: bundling data with code operating on it
## Classes
- ==blueprint for objects outlining possible states & behaviors==
![[Pasted image 20211109224314.png]]

- in python, everything is an object
- every object has a class

### Attributes & methods
- state <--> attributes <--> variables <--> `obj.my_attribute`
    - encode the state of an object
- behavior <-->methods <--> function() <--> `obj.my_method()`
    - encode the behavior of an object
- list all attributes & methods by calling `dir(obj)`
- object = attributes + methods

#### a basic class:
- `class <name>`: starts a class definition
- code inside `class` is indented
- use `pass` to create an "empty" class
- use ClassName() to create an object of ClassName

#### add methods to a class:
- method definition = function definition *within a class*
- **use `self` as the 1st argument in method definition**
- ignore `self` when calling method on an object

#### what is self?
- classes are templates - how to refer data of a particular object?
- `self` is a stand-in for a particular object used in a class definition
- python takes care of `self` when method called from an object:
`ExampleClass().method("Laura")` will be interpreted as `ExampleClass.method(ExampleClass(), "Laura)"`

#### we need attributes
- **encapsulation**: bundling data with methods that operate on data
- Attributes are created by assignment (=) in methods
```
class Employee:
 def set_name(self, new_name):
 self.name = new_name

 # Add set_salary() method
 def set_salary(self, new_salary):
 self.salary = new_salary
 
# Create an object emp of class Employee 
emp = Employee()

# Use set_name to set the name of emp to 'Korel Rossi'
emp.set_name('Korel Rossi')  

# Set the salary of emp to 50000
emp.set_salary(50000)


class Employee:
    def set_name(self, new_name):
        self.name = new_name

    def set_salary(self, new_salary):
        self.salary = new_salary 

    def give_raise(self, amount):
        self.salary = self.salary + amount

    # Add monthly_salary method that returns 1/12th of salary attribute
    def monthly_salary(self):
        return self.salary/12

    
emp = Employee()
emp.set_name('Korel Rossi')
emp.set_salary(50000)

# Get monthly salary of emp and assign to mon_sal
mon_sal = emp.monthly_salary()

# Print mon_sal
print(mon_sal)
```

### __init__ constructor
- adding attributes to objects can quickly become unsustainable
- better strategy is to add data to the object *when creating it*
    - Add a special method called **Constructor** : `__init__()`
        - called every time an object is created
- **If possible, try to avoid calling attributes outside the constructor**
    - easier to know all the attributes
    - defining all attributes in the constructor ensures all are created when object is created
        - don't have to worry about calling a non-existent attribute
```
# Import datetime from datetime
from datetime import datetime

class Employee:
    
    def __init__(self, name, salary=0):
        self.name = name
        if salary > 0:
          self.salary = salary
        else:
          self.salary = 0
          print("Invalid salary!")
          
        # Add the hire_date attribute and set it to today's date
        self.hire_date = datetime.today()
        
   # ...Other methods omitted for brevity ...
      
emp = Employee("Korel Rossi", -1000)
print(emp.name)
print(emp.salary)
```
## Best Practices
1. **Initialize all attributes in `__init__()`**
2. **Naming**:
    1. `CamelCase` for classes
    2. `lower_case_snake` for functions and attributes
3. **Keep `self` as `self`
4. **Use docstrings** 

## Class data
- `self` binds data to a particular instance
- data shared among all instances of a class: 
    - `CLASS_ATTR = "'Global variable' within the class"`
    - don't use `self` to define class attribute
    - use `ClassName.CLASS_ATTR` to access the class attribute value
    - use as global constant related to the class
        - minimal/maximal values for attributes
        - commonly used values and constants, e.g. `pi` for a `Circle` class
        - can assign with methods
## Class methods
- *can't use instance-level data*
```
@classmethod
 def some_funt(cls, value):
    pass
```
- First argument must be `cls`
- main use is defining methods that return an instance of the class, without using `__init_()`
    - class can only have 1 init 
- use class method to create objects
    - `return` to return an object
    - `cls(...)` will call `__init__()`

## Class Inheritance
Class attributes can be inherited, and the value of class attributes can be overwritten in the child class
```
class Counter:
    def __init__(self, count):
       self.count = count

    def add_counts(self, n):
       self.count += n

class Indexer(Counter):
   pass
```

| True                                                                              | False                                                                                 |
| --------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Class `Indexer` is inherited from `Counter`                                       | if `ind` is an `Indexer` object, then running `ind.add_counts(5)` will cause an error |
| Inheritance represents "is-a" relationship                                        | Every `Counter` object is an `Indexer` object                                         |
| if `ind` is an `Indexer` object, then isintsance(ind, Counter) will return `True` | Inheritance can be used to add some of the parts of one class to another class        |
| running `ind = Indexer()` will cause an error                                     |                                                                                       |

```
class Employee:
  MIN_SALARY = 30000    

  def __init__(self, name, salary=MIN_SALARY):
      self.name = name
      if salary >= Employee.MIN_SALARY:
        self.salary = salary
      else:
        self.salary = Employee.MIN_SALARY
  def give_raise(self, amount):
    self.salary += amount      
        
# MODIFY Manager class and add a display method
class Manager(Employee):
  def display(self):
    print(f"Manager {self.name}")

mng = Manager("Debbie Lashko", 86500)
print(mng.name)

# Call mng.display()
mng.display()
```

- customize constructors by creating a subclass:
    - can run constructor of the parent class foirst by `Parent.__init__(self, args...)
- add methods as usual
- you can use data from both the class & the subclass
```
class Employee:
    def __init__(self, name, salary=30000):
        self.name = name
        self.salary = salary

    def give_raise(self, amount):
        self.salary += amount

        
class Manager(Employee):
    def display(self):
        print("Manager ", self.name)

    def __init__(self, name, salary=50000, project=None):
        Employee.__init__(self, name, salary)
        self.project = project

    # Add a give_raise method
    def give_raise(self, amount, bonus=1.05):
        amount_calc = amount * bonus
        Employee.give_raise(self, amount_calc)
    
    
mngr = Manager("Ashta Dunbar", 78500)
mngr.give_raise(1000)
print(mngr.salary)
mngr.give_raise(2000, bonus=1.03)
print(mngr.salary)
------------------------
79550.0 
81610.0
```

```
import pandas as pd

# Define LoggedDF inherited from pd.DataFrame and add the constructor
class LoggedDF(pd.DataFrame):
  
  def __init__(self, *args, **kwargs):
    pd.DataFrame.__init__(self, *args, **kwargs)
    self.created_at = datetime.today()
    
  def to_csv(self, *args, **kwargs):
    # Copy self to a temporary DataFrame
    temp = self.copy()
    
    # Create a new column filled with self.created_at
    temp["created_at"] = self.created_at
    
    # Call pd.DataFrame.to_csv on temp, passing in *args and **kwargs
    pd.DataFrame.to_csv(temp, *args, **kwargs)
```

## Variables 
- because python stores 2 equivalent objects in different memory locations, they are not considered `==`
  - enforce equality with `__eq__`
    - standard python calls this method whenever 2 objects of a class are compared using `==`
    - accepts 2 arguments, `self` & `other`
    - returns `Bool`, e.g.:
`    def __eq__(self, other):
        return (self.number == other.number) and (type(self) == type(other))`
    - Python always calls the child's `__eq__()` method when comparing a child object to a parent object
  - also:
    - `__ne__()`: !=
    - `__ge__()`: >=
    - `__le__()`: <=
    - `__gt__()`: >
    - `__lt__()`: <
    - `__hash__()` to use objects as dictionary keys & in sets
      - should assign an integer to an object such that equal objects have equal hashes
```
class BankAccount:
   # MODIFY to initialize a number attribute
    def __init__(self, number, balance=0):
        self.balance = balance
        self.number = number
      
    def withdraw(self, amount):
        self.balance -= amount 
    
    # Define __eq__ that returns True if the number attributes are equal 
    def __eq__(self, other):
        return self.number == other.number   

# Create accounts and compare them       
acct1 = BankAccount(123, 1000)
acct2 = BankAccount(123, 1000)
acct3 = BankAccount(456, 1000)
print(acct1 == acct2)
print(acct1 == acct3)
```

## String __repr__ (representation)
- calling `print(MyClass)` returns the object's address in memory by default
- Two methods:
  1. `__str__()`
          - executed when we call `print(obj)` or `str(obj)`
          - informal, for end user
```
class Customer:
  def __init__(self, name, balance):
    self.name, self.balance = name, balance
  
  def __str__(self):
    cust_str = f"""
    Customer:
      name: {self.name}
      balance: {self.balance}
      """
      return cust_str
      
print(cust) # implicitly calls __str__()
```
  2. `__repr__()`
        - executed when we call `repr(obj)`, or when we print in console without calling `print()` explicitly
        - formal, for developer
        - *repr*oducible *repr*esentation
        - fallback for `print()`
```
class Customer:
  def __init__(self, name, balance):
    self.name, self.balance = name, balance
  
  def __repr__(self):
    return "Customer('{self.name}', {self.balance})"

cust = Customer('Maya', 3000)
cust # implicitly calls __repr__()
-----------------------------------
Customer('Maya', 3000)
```

## Exceptions are classes
- standard exceptions are inherited from `BaseException` or `Exception`
### Custom exceptions
> - inherit form `Exception` class or one of its subclasses
```
class SalaryError(ValueError): pass
class BonusError(SalaryError): pass

class Employee:
  MIN_SALARY = 30000
  MAX_BONUS = 5000

  def __init__(self, name, salary = 30000):
    self.name = name    
    if salary < Employee.MIN_SALARY:
      raise SalaryError("Salary is too low!")      
    self.salary = salary
    
  # Rewrite using exceptions  
  def give_bonus(self, amount):
    if amount > Employee.MAX_BONUS:
       print("The bonus amount is too high!")  
        
    elif self.salary + amount <  Employee.MIN_SALARY:
       print("The salary after bonus is too low!")
      
    else:  
      self.salary += amount
```
- It's better to include an `except` block for a child exception before the block for a parent exception, otherwise the child exceptions will be always be caught in the parent block, and the `except` block for the child will never be executed
  - list the except blocks in the increasing order of specificity, i.e. children before parents, otherwise the child exception will be called in the parent except block
  
  # Best practices
  ## Polymorphism
  - using a unified interface to operate on objects of different classes
  - Liskov substitution principle: **Base class should be interchangeable with any of its subclasses without altering any properties of the program**
    - i.e., wherever `BankAccount` obj works, `CheckingAccount` should work also
    - **Syntactically**: function signatures are compatible (args, returned values)
    - **Semantically**: the state of the object and the program remains consistent
      - subclass method doesn't strengthen input conditions
      - subclass method doesn't weaken output conditions
      - no additional exceptions
      - changing additional attributes in subclass's methods
   
   ### Violating LSP:
   ##### Syntactic incompatibility
   `BankAccount.withdraw()` requires 1 param, but `CheckingAccount.withdraw()` requires 2
  
  ##### Subclass strengthening input conditions
  `BankAccount.withdraw()` accepts any amount, but `CheckingAccount.withdraw()` assumes that the amount is limited
  
   ##### Subclass weakening output conditions
   `BankAccount.withdraw()` can only leave a positive balance or cause an error, whereas `CheckingAccount.withdraw()` can leave balance negative
   
  