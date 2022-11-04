[[Python]] [[Software engineering in Python]]
[Type Hints - Guido van Rossum](https://www.youtube.com/watch?v=2wDvzy6Hgxg)
# Type hinting
- python 3.5+
- due to the dynamic nature of Python, inferring or checking the type of an object doesn't happen automatically
  - this makes it harder for devs to understand what exactly is going on in code they haven't written, and 
- *note*: python interpreter does not attach any particular meaning to variable annotations - it is really just interactive documentation
  - it only stores them in the `__annotations__` attribute of a class or module

#### Examples:
```python
primes: List[int] = [] # primes is *intended* to only contain ints

captain: str  # no initial value - this is valid Python!

class Starship:
     stats: Dict[str, int] = {} # expects dict with str key & int value
```
## why type hinting?
- **helps type checkers**: by hinting at what type you want the object to be, the type checker can easily detect if, for instance, you're passing an object with a type that isn't expected
- **helps with documentation**: another dev viewing your code will know what is expected where, thus avoiding `TypeError`
- **helps IDEs develop more accurate tools**
## why static type checker?
- **find bugs sooner** (in annotated code)
- **the larger your project, the more you need it**


## Pydantic enforces type hints at runtime (fail fast), and provides user-friendly errors when data is invalid

- ==use pydantic instead of nested dictionaries==
  - you don't know what kind of data to expect
  - since any key is technically valid, your IDE will not flag a typo - you'll just hit a key error at runtime
  - 
```python

[
{"id": 1, "name": "Anna", "friends": [2, 3, 4], "birthdate": "1992-01-15", "bank_account": 12.3},
{"id": 2, "name": "Bob", "friends": null, "birthdate": "1962-12-31", "bank_account": 0.1},
{"id": 3, "name": "Charlie", "friends": [4], "birthdate": "1992-02-28", "bank_account": 9007199254740993.0},
{"id": 4, "name": "Martin", "friends": [1, 3], "birthdate": "1990-04-28", "bank_account": 9007199254740993}
]
```
```python
import json
from pathlib import Path
from typing import Any, Mapping

import numpy as np


def main(filepath: Path):
    people = get_people(filepath)
    friends_difference = get_account_difference(people)
    for person_id, diff in friends_difference.items():
        print(f"{person_id}: {diff:0.2f} EUR")


def get_account_difference(
    people: Mapping[str, Mapping[str, Any]]
) -> Mapping[int, float]:
    result = {}
    for person in people.values():
        if person["friends"] is not None:
            friends_account = [
                people[friend]["bank_account"] for friend in person["friends"]
            ]
            median = np.median(friends_account)
        else:
            median = None

        if median is None:
            result[person["id"]] = 0
        else:
            result[person["id"]] = person["bank_account"] - median
    return result


def get_people(filepath: Path) -> Mapping[str, Mapping[str, Any]]:
    with open(filepath) as fp:
        people = json.loads(fp.read())
    id2person = {}
    for person in people:
        id2person[person["id"]] = person
    return id2person


if __name__ == "__main__":
    main(Path("people.json"))
```
```