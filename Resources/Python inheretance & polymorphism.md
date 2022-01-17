[[Object-oriented programming in Python]] [[Python]][[Integrating your classes with Standard Python]]
## enable *efficient & consistent* code reuse with:

# Inheritance
==extending functionality of existing code==

# Polymorphism:
==creating a unified interface==

# Encapsulation
==Bundling of data and methods==

## Instance-level data vs. class-level data
#### Instance-level uses `self` to bind attributes to a particular instance
#### Class-level data is shared among all instances of a class
- define attribute directly in the class body
    - "Global Variable" within the class
    - don't use `self` to define class attribute
    - use `ClassName.ATTR_NAME` to access the class attribute variable
```
class Player:
    MAX_POSITION = 10
    
    def __init__(self):
        self.position = 0

    # Add a move() method with steps parameter
    def move(self, steps):
        if self.position + steps < Player.MAX_POSITION:
            self.position = steps + self.position
        else:
            self.position = Player.MAX_POSITION
    

       
    # This method provides a rudimentary visualization in the console    
    def draw(self):
        drawing = "-" * self.position + "|" +"-"*(Player.MAX_POSITION - self.position)
        print(drawing)

p = Player(); p.draw()
p.move(4); p.draw()
p.move(5); p.draw()
p.move(3); p.draw()
```
### Why use class attributes?
- Global constants related to the class
- minimal/maximal values for attributes
- commonly used variables and constants, e.g. `pi` for a `Circle` class

### Class methods
- Methods are already "shared": same code for every instance
- Class methods can't use instance-level data
- defined with `@classmethod` decorator
- use `cls` parameter instead of `self`
```
# import datetime from datetime
from datetime import datetime

class BetterDate:
    def __init__(self, year, month, day):
      self.year, self.month, self.day = year, month, day
      
    @classmethod
    def from_str(cls, datestr):
        year, month, day = map(int, datestr.split("-"))
        return cls(year, month, day)
      
    # Define a class method from_datetime accepting a datetime object
    @classmethod
    def from_datetime(cls, datetime):
      return cls(datetime.year, datetime.month, datetime.day)

# You should be able to run the code below with no errors: 
today = datetime.today()     
bd = BetterDate.from_datetime(today)   
print(bd.year)
print(bd.month)
print(bd.day)
```
#### use class methods for alternative constructors
- class can only have one `__init()__`
- 