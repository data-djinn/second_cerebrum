[[Python]] [[Software engineering in Python]]

# Starting a package
### why build a package?
- if you're copying and pasting code from one project to another, consider using packages
-  make your code easier to use by bundling it up and making it importable (just like pandas, numpy, etc.)
-  stops you from having multiple versions of the same code spread across many files
  -   avoid copy & pasting code
  -   keep your functions up to date
  -   give your code to others

## File layout
##### Script: a python file which is run like `python myscript.py`
- does one set of tasks
##### package: a directory full of Python code to be imported like `import numpy`
- all code in this dir is related & works together
- **subpackages**: a smaller package inside another package
##### Module: python file inside a package which stores the package code
##### Library: either a package or a collection of packages

### Directory tree of a package
```
my_simple_package/ 
|-- simple_module.py # contains funcs & all our code
|-- __init__.py # empty file
```

### Documentation
- helps your users use your code
- document each:
  - function
  - class
  - class method
- use `help(my_func)` to access python documentation

```python
def count_words(filepath, words_list):
  """count the total number of tiems these words appear.
  
  [explain what filepath and words_list aro]
  
  [and what is returned]
  """
```

- use reST style
- `pyment` can generate docstrings
  -  run from the terminal
  -  any documentation style
  -  modify one documentation style to another

`pyment -w -o reST textanalysis.py`
- `-w `: overwrite file
- `-o reST textanalysis.py`: output in reST style
- creates a template with headings & the parameter names
  - you'll need to fill in the rest of the detail

#### Package, subpackage, and module documentation
- package documentation  is placed in a string at the top of the package's `__init__.py` file
  - this should summarize the package
  - same with subpackage
- module documentation is written at the top of the module file

## Import Structure
- packages don't start with internal imports
  - this means, when you import the package, you cannot access any of the subpackages or modules
  - to access the subpackages, you need to import them separately each time you use them
  - subpackages don't know about the modules they contain either, so they have to be imported too
- **stop this with internal imports, to import the modules into the subpackages**
  ##### absolute import (preferred)
 `from mysklear import preprocessing`
 ##### relative import (shorter)
 `from . import preprocessing`
 
`from .module import function`
`from ..subpackage.module import function`
 
# Make your package installable 
- if you move the script and the package apart, you will no longer be able to import your package
  - python scripts can search for packages inside its parent directory, but it won't search outside of it
- if you install it, you can import the package no matter where the script is located, just like numpy
- to make the package installable, **use `setup.py` script**
  - contains metadata about the package & is required to publish your package
- part of the package, but not part of the source code
  - therefore we create a new folder, inside the package directory, to keep the source code
  - new folder inside the package directory to keep the source code
  - very common to name them the same
![[Pasted image 20220302113759.png]]

```python
from setuptools import setup, find_packages

setup(
  author="James Fulton",
  description="an example package"
  name="my_package"
  version="0.1.0",
  packages=find_packages(include=["my_package", "my_package.*"])
)
```
now use `pip install -e .`
- `-e` for editable to include changes without needing to reinstall
## Dealing with dependencies
**use `setup(...install_requires=['pandas', sqlalchemy],...)` parameter**
- when someone else uses pip to install your package, pip will install these packages as well
- in the above example, any version of pandas & sqlalchemy is allowed
- to define specific package versions:
`install_requires=['pandas>=1.0', 'sqlalchemy==1.4,<3']
- allow as many package versions as possible
- get rid of unused dependencies
- specify the version of python required:
`setup(...python_requires='>=2.7, !=3.0.*',...)`

### Choosing dependency & package version
- check package history or release notes
- test different versions

### Making an environment for other developers
- in this case, you want to know exactly which versions you are using
  - having the exact same packages makes it easier for your team to hunt & squash bugs
- you can show all the package versions you have installed with the `pip freeze > requirements.txt` commond
  - this exports the output into a text file which you include with your package
  - then anyone can install all packages in this file using `pip install -r requirements.txt`

### including licenses & writing READMEs
#### Open source licenses [found here](https://choosealicense.com/licenses/)
- allows users to:
  - use your package
  - modify your package
  - distribute versions of your package
#### README
- right up top, along with `setup.py` & `requirements.txt`
- The "front page" of your package
- displayed on Github or PyPI
- should include:
  - package title
  - description & features
  - installation
  - usage examples
  - contributing
  - license

#### MANIFEST.in
- lists all the extra files to include in your package distribution
  - important because, by default, your license & README will not be included when someone downloads your package
```
include LICENSE
include README.md
```

# Adding licenses & READMEs
# Style and unit test for high quality
[[Unit Testing in Python]]
- good packages brag about how many tests they have
  - 91% of the pandas package code has tests
- helps you track down bugs, & signals to your users that your package can be trusted to perform as intended
##### Each function in your package should have a test function
```python
def get_ends(x:list): -> Any
  """Get the first and last element in a list"""
  return x[0], x[-1]
def test_get_ends():
  assert get_ends([1,5,39,0]) == (1,0), "int test failed"
  assert get_ends(['n', 'e', 'r', 'd']) == ('n', 'd'), "string test failed"
```

- **keep tests in their own dir, in the top folder of the package**
![[Pasted image 20220408212311.png]]
- copy the structure of the code directory
![[Pasted image 20220408212637.png]]
- inside the test module, there should be a test function for each function defined in the source module
![[Pasted image 20220408212652.png]]
  - you will need to import the functions you are testing from the main package
  - this should be done as an *absolute import*

##### run tests with `pytest`
```python
cd ~/my_python_project
pytest
```
- `pytest` looks inside the `test` directory
  - searches for all modules that start with `test_`
    - inside these modules, it will look for functions that start with `test_`, and **it will run those functions**
# Register and publish your package to PyPi