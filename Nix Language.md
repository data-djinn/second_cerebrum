[[Nix]]

# Basics
- DSL for writing packages
- used to write expressions that produce derivations
- `nix build` tool is used to build derivations from an expression
- begin interactive session with `nix repl`
##### Operators
- supports basic arithmetic, boolean, comparison operations: `+`, `-`, `*`, `/`, `||`, `&&`, `!`, `!=`, `==`, `<`, `>`, `<=`, `>=`
- quit with `:q`
- help with `:?`
- using `/` without spaces will parse as a relative path from the current directory
	- divide with `builtins.div`
- `-` is allowed in identifier names, since many packages use dash
	- subtract by adding space in between
##### Strings
- strings are encoded in `"` or `''` , but *not `'`*
- interpolate whole nix expressions inside strings weth the `${}` syntax
##### Lists
- sequence of expressions, **delimited by spaces**
- like everything else in Nix, lists are *immutable*
	- adding/removing elements is possible, but only by returning a new list
##### Attribute sets (dictionaries)
- keys can only be strings or unquoted identifiers
`dict = { foo = "bar"; a-b = "baz"; "123" = "num"; }`
`dict.a-b` --> `"baz"`
- inside an attribute, you cannot normally refer to elements of the same attribute set (`{ a = 3, b = a+1 }` --> error)
- to do this, use **recursive attribute sets**: `rec {a = 3; b = a+4}`
	- very convenient when defining packages!
##### if expressions
- `if a > b then "yes" else "no"`
- must always have the else branch - an expression must have a value in all cases
##### `let` expressions
- define local variables for inner expressions
`let a = "foo"; in a` -> `"foo"`
- first assign variables, then `in`, then an expression that uses the defined variables
- the value of the whole let expression will be the value of the expression after the `in`
- `let a = 3; in let b = 4; in a + b` -> `7`
- you can't assign  to the same variable; however, you can shadow outer variables
```nix
let a = 3; a = 8; in a # will throw error
let a = 3; in let a = 8; in a # --> 8
```
- you also cannot refer to variables in a `let` expression outside of it
`let a = (let c = 3; in c); in c` -> error
- you can refer to variables in the let expression when assigning variables, i.e. with recursive attribute sets:
`let a = 4; b = a +5; in b`

##### With expressions
- like python's `from module import *`
- adds all identifiers from an attribute set`
`longName = { a = 3; b = 4; }`
`with longName; a + b` -> 7
- if the symbol exists in the outer scope & would also be introducet by `with`, it **will not shadow the outer scope**
	- `let a = 10; in with longName; a + b` -> 14
- refer to the attribute set to access the symbol
	- `let a = 10; in with longName; longName.a + b` -> 7
- you can't assign twice to the same variable
- you can shadow outr variables however

#### Laziness
- nix expressions are only evaluated when needed
`let a = builtins.div 4 0; b = 6; in b` -> `6`
- since a is not needed, there's no error about division by 0
- that's how we can have all packages defined on demand, yet have access to specific packages very quickly

# Nix functions & imports
- help to build reusable components in a big repository like nixpkgs
- the nix manual has a great explanation of functions [here](https://nixos.org/manual/nix/stable/expressions/language-constructs.html#functions)
- **all functions are anonymous & only have a single parameter**
`param_name: <<body of the function>>` --> `<<lambda>>`
- store functions in variables:
```nix
double = x: x*2
double 3  # returns 6
```

- how to crueate a function that accepts more than one parameter?
```nix
mul = a: (b: a*b)
mul # --> <<lambda>>
mul 3  # --> <<lambda>>
(mul 3) 4 # --> 12
```
- calling `mul 3` returns a lambda function of `b: 3*b`
- use parentesis to pass more complex expressions: `mul (6+7) (8+9)`
- since functions only have one parameter, it's straightforward to use partial application
```nix
foo = mul 3
foo 4  # returns 12
foo 5  # returns 5
```

#### Arguments set
- pattern match over a set in the parameter
```nix
# define a func that accepts a single param, accessing their attrs a & b
mul = s: s.a*s.b
mul { a = 3; b = 4; }  # returns 12

# define an arguments set
mul = { a, b }: a*b
mul { a = 3; b = 4}  # returns 12
```
- only a set with **exactly the attributes required by the function** is accepted

#### Default & variadic attributes
- specify **default values** of attributes in the arguments set
```nix
mul = { a, b ? 2 }; a*b
mul { a = 3; }  # returns 4
```
**you can also allow passing more attributes (*variadic*) than the expected ones**
```nix
mul = { a, b, ... }: a*b
mul { a = 3; b = 4; c = 2; }  # returns 12, can't access c attribute
# solution is to give a name to the set with the @ pattern:
mul = s@{ a, b, ... }: a*b*s.c
mul { a = 3; b = 4; c = 2; }  # returns 24
```
- advantages of ordered sets:
	- named unordered arguments: you don't have to remember the order of the arguments
	- you can pass sets
- disadvantages:
	- **partial application does not work with arguments sets**: you have to specify the whole attribute set, not part of it
similar to python `\**kwargs`
#### Imports
- parse `.nix` files
- Composability: define each component in a seperate `.nix` file, then compose by importing those files
[[Nix flakes]]

`a.nix`:
`3`

`b.nix`:
`4`

`mul.nix`:
`a: b: a*b`

```nix
a = import ./a.nix
b = import ./b.nix
mul = import ./mul.nix
mul a b  # returns 12
```
- the scope of the imported file does not inherit the scope of the importer
`test.nix`:
`x`
```nix
let x = 5; in import ./test.nix  # throws undefined variable 'x' error
```
- how to pass info to the module? using functions, like we did with `mul.nix`
`test.nix`:
```nix
{ a, b ? 3, trueMsg ? "yes", falseMsg ? "no" }:
if a > b
	then builtins.trace trueMsg true
	else builtins.trace falseMsg false
```

`import ./test.nix { a = 5; trueMsg = "ok"; }`
>>>>>>> aeaaaa2 (2022-11-17)
