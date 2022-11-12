[[Nix]]

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
##### Let expressions
- define local variables for inner expressions
`let a = "foo"; in a` -> `"foo"`
- first assign variables, then `in`, then an expression that uses the defined variables
- the value of the whole let expression will be the value of the expression after the `in`
- `let a = 3; in let b = 4; in a + b` -> `7`
- you can't assign twice to the same variable; however, you can shadow outer variables
`let a = 3, in let a = 8, in a` -> `8`
- you also cannot refer to variables in a `let` expression outside of it
##### With expressions
- like python's `from module import *`
- adds all identifiers from an attribute set`
`longName = { a = 3; b = 4; }`
`with longName; a + b` -> 7
- if the symbol exists in the outer scope & would also be introducet by `with`, it **will not shadow the outer scope**
	- `let a = 10; in with longName; a + b` -> 14
- refer to the attribute set to access the symbol
	- `let a = 10; in with longName; longName.a + b` -> 7
- 