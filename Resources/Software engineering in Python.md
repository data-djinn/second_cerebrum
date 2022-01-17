[[Python]] [[Object-oriented programming in Python]]
## PEP 8
- 
# Modularity
- *avoid* long, complicated, hard-to-read scripts - `import pkgs`
##### Modules contain definitions of functions, classes, and variables that can then be utilized in other python programs
- improve readability
- improve maintainability
- solve problems only once
## Leverage packages, classes, and methods
### Packages
Python 3.3+ packages should contain **empty** `__init__` files if all parts live in the same directory hierarchy, i.e.:
    **regular packages** are self-contained meaning all parts live in the same directory hierarchy.==When importing a package and the Python interpreter encounters a subdirectory on the sys.path with an __init__.py file, then it will create a single directory package containing only modules from that directory,== rather than finding all appropriately named subdirectories outside that directory.
    [Traps for the Unwary in Python’s Import System](http://python-notes.curiousefficiency.org/en/latest/python_concepts/import_traps.html)
 ==Never add a package directory, or any dir inside a package, directly to the python path==
 - always use the -m switch when running a module from the command line, otherwise this happens automatically

*The below attempts will likely all **fail** due to ??
```
project/
    setup.py
    example/
        __init__.py
        foo.py
        tests/
            __init__.py
            test_foo.py
```
```
# working directory: project/example/tests
./test_foo.py
python test_foo.py
python -m test_foo
python -c "from test_foo import main; main()"

# working directory: project/example
tests/test_foo.py
python tests/test_foo.py
python -m tests.test_foo
python -c "from tests.test_foo import main; main()"

# working directory: project
example/tests/test_foo.py
python example/tests/test_foo.py

# working directory: project/..
project/example/tests/test_foo.py
python project/example/tests/test_foo.py
python -m project.example.tests.test_foo
python -c "from project.example.tests.test_foo import main; main()"
```
- *don't* edit `sys.path`, instead (from working dir: `project/`) use:
`python -c "from example.tests.test_foo import main; main()"`
or:
`python -m example.tests.test_foo`

Use:
```
import sys    
print("In module products sys.path[0], __package__ ==", sys.path[0], __package__)
```
etc. in each file to debug

- keep `main` modules minimal - far more robust to move functionality to functions or object in a separate module, and import that module from the main module. that way, inadvertently executing the main module twice becomes harmless. keeping main modules small and simple also helps to avoid potential problems with object serialisation & multiprocessing (`asyncio`)
- don't rename or create files with same name as implicit python packages like `main` or `socket`,`import`,`path`,`package`
- when a submodule is loaded *anywhere*, it is automatically added to the global namespace of the package:
```
$ echo "import logging.config" > weirdimport.py
$ python3
>>> import weirdimport
>>> import logging
>>> logging.config
<module 'logging.config' from '/usr/local/Cellar/python3/3.4.3/Frameworks/Python.framework/Versions/3.4/lib/python3.4/logging/config.py'>
```
issues with pickle, multiprocessing and the main module (see PEP 395)??
- packages can contain `__init__.py`
    - Python 2 uses this file to import key functions from other files in same package dir
    - Python 3+ *leave empty*

### Methods
```
def world():
    print("Hello, world!")
```
 -->`hello.py`
 
 ```
 # import hello module
 import hello
 
 # call function
 hello.world()
 ```
 -->`main_program.py`
 - need to call func by referencing the module name in dot notation
 - could also use `from hello import world` & call the function directly as `world()`

### Variables
```
def world():
    print("Hello, world!")

shark = "baby"
```
  ```
 # import hello module
 import hello
 
 # call function
 hello.world()
 
 print(hello.shark)
-------------------
Hello, World!
baby
 ```

### Classes
```
# Define a function
def world():
    print("Hello, World!")

# Define a variable
shark = "Sammy"

# Define a class
class Octopus:
    def __init__(self, name, color):
        self.color = color
        self.name = name

    def tell_me_about_the_octopus(self):
        print("This octopus is " + self.color + ".")
        print(self.name + " is the octopus's name.")
```
--> `hello.py`

- though modules are *often* definitions, they can also implement code:
```
# Define a function
def world():
    print("Hello, World!")

# Call function within module
world()
```
```
import hello
-------------------
Hello, World!
```

# Documentation
- show users how to use your project
- prevent confusion & frustration from your collaborators
- prevent 
# Testing
- save time over manual testing
- find & fix more bugs
- run tests anytime & anywhere
# Version control
[[git]]