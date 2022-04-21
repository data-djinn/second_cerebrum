[tech]

# Tips
## Understand data model methods
- top-level function or top-level syntax --> corresponding ___
    - x + y --> `__add__`
    - init x --> `__init__`
    - repr(x) --> `__repr__`
```
class Polynomial:
    def __init__(self, *coeffs):
        self.coeffs = coeffs

    def __repr__(self):
        return 'Polynomial(*{!r})'.format(self.coeffs)
    
    def __add__(self, other):
        return Polynomial(*(x + y for x, y in zip(self.coeffs, other.coeffs)))
    
    def __len__(self):
        retrun len(self.coeffs)
```

## Metaclasses
-