[[Python]] [[Object-oriented programming in Python]]
## PEP 8
"Code is read more often than it is written"
```python
import pycodestyle

# Create a StyleGuide instance
style_checker = pycodestyle.StyleGuide()

# Run PEP 8 check on multiple files
result = style_checker.check_files(['nay_pep8.py', 'yay_pep8.py'])

# Print result of PEP 8 style check
print(result.messages)
```
![[Pasted image 20220121152235.png]]
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
```shell
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
##### To debug a `__init__.py` file:
Use:
```python
import sys    
print("In module products sys.path[0], __package__ ==", sys.path[0], __package__)
```

- keep `main` modules minimal - far more robust to move functionality to functions or object in a separate module, and import that module from the main module. that way, inadvertently executing the main module twice becomes harmless. keeping main modules small and simple also helps to avoid potential problems with object serialisation & multiprocessing (`asyncio`)
- don't rename or create files with same name as implicit python packages like `main` or `socket`,`import`,`path`,`package`
- when a submodule is loaded *anywhere*, it is automatically added to the global namespace of the package:
```python
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
```python
def world():
    print("Hello, world!")
```
 -->`hello.py`
 
 ```python
 # import hello module
 import hello
 
 # call function
 hello.world()
 ```
 -->`main_program.py`
 - need to call func by referencing the module name in dot notation
 - could also use `from hello import world` & call the function directly as `world()`

### Variables
```python
def world():
    print("Hello, world!")

shark = "baby"
```
```python
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
```python
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
```python
# Define a function
def world():
    print("Hello, World!")

# Call function within module
world()
import hello
-------------------
Hello, World!
```

# 2 files needed for portability:
![[Pasted image 20220121153449.png]]
## requirements.txt
- how to recreate the env in order to use your package
- list of packages
- & their versions (optional)
- **install with:** `pip install -r requirements.txt`
```
# Needed packages/versions
matplotlib
numpy==1.15.4 # required version
pycodestyle<=2.4.0 # Minimum version
```
## setup.py
- how to install our package
```python
from setuptools import setup

setup(name='my_package',
  version='0.0.1,
  description='An example package'
  author_email='fake_email@email.com'
  packages=['my_package'], # list all the __init__ files in pkg
  install_requires=['matplotlib', # often the same as requirements.txt
                     'pycodestyle'])
```
# Documentation
- show users how to use your project
- prevent confusion & frustration from your collaborators
### Comments
- sprinkle in info for future collaborators
- make your code more readable - keep them useful
- **Explain why it is doing, less *what* it doing**
- better to over-comment than under-comment

### Docstrings
- invoked by the use of triple quotation marks, e.g.:
```python
def my_function(x):
  """High level description of function

  Additional details on function

  :param x: number to square
  :return: description of return value

  >>> square(2)
  4
  """
  return x * x # faster than x ** 2
```
- typically used to document functions, classes, and scripts for your users
- use `help()` to retrieve docstring
# Testing
- save time over manual testing
- find & fix more bugs
- run tests anytime & anywhere
# Version control
[[git]]