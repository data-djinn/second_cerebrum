[[Software engineering in Python]]

==when used improperly, they will add unnecessary boilerplate & complexity==
website: *refactoring guru* [Refactoring and Design Patterns](https://refactoring.guru/?msclkid=ffeee840ab8d11ec89a675770d596117)
book: *Design Patterns - elements of reusable OOP*
*a good programmer is a good problem solver*

# Composition over inheritance principle
==favor object composition over class inheritance==
- crucial weakness of inheritance as a design strategy is that **a class often needs to be specialized along several different axes at once**
  - this leads to:
    -  proliferation of classes
    -  explosion of subclasses to support every combination
# Design patterns
**recurring problems in software engineering:**
## Creational: how objects are created
### Singleton: type of object (`class`) only implemented once across entire project
- check to see if this object has been created in global scope
- if not, create a new one

### Prototype: alternative way to implement inheritance
- instead of inheriting functionality from a class, it comes from an object that has already been created
  - this creates a flat **prototype chain** that makes it easier to share functionality between objects

### Builder: create object step-by-step using methods, instead of constructor
- you don't always know all the attributes at time of construction
- delegate the building logic to an entirely different class

### Factory: use a function to instantiate an object for you
## Structural: how objects relate to each other
### Facade: simplified API to hide low-level details in your code base
- example: u
# behavioral: how objects communicate with each other



