[[Functional Programming]] [[10 reasons to use haskell]]

# Foundations

### Haskell platform
##### Glasgow Haskell Compiler (GHC)
- haskell is a compiled language
- job of the compiler is to transform human-readable source code into machine-readable binary
- at the end of compilation, you're left with an executable binary file
- main benefit of a compiled language over an interprer is that the compiler can perform analysis and optimization of the code you've written
- because of the powerful type system, **if it compiles, it works**

```haskell
-- hello.hs my first haskell file!! :)
main = do  -- start of my function, 'main'
	print "Hello World!"
```
- compile with `ghc hello.hs`, generating 3 files:
1. `hello` (`hello.exe` if on Windows): binary executable
	- simply run with `./hello`
	- defailt behavior is to execute the logic in main
	- by default, all Haskell programs you're compiling need to have a `main`, which plays a similar role to `__main__` in python
	- many optional flags:
		- `-o` to change the output binary file's name (`ghc hello.hs -o helloworld`)
1. `hello.hi`
2. `hello.o`

##### interactive interpreter
- `ghci` to start interactive interpreter
- can use like a python REPL, i.e. `2 + 2` -> `4`
- 2 ways to import existing files:
	- pass filename as argument: `ghc hello.hs`
	- `:l`/`:load` command inside the interpreter: `:l hello.hs`
		- you can then call the imported functions, e.g. `main`
		- unlike compiling functions, your files don't need a `main` to be loaded into GHCi
	- any time you load a file, you'll overwrite existing definitions of functions & variables
	- Haskell is unique in having both strong compiler support & easy-to-use interactive environment
##### stack tool
- the compiler is strict: if you're accustomed to writing a program, running it, and quickly fixing any errors you've made, haskell will frustrate you
- Haskell strongly rewards taking time & thinking through problems before running programs
- the trick to writing haskell code with minimal frustration is to write code in little bits, and play with each bit interactively as it's written
```haskell
-- poorly written program
messyMain :: IO()
messyMain = do
	print "Who is the email for?"
	recipient <- getLine
	print "What is the title?"
	title <- getLine
	print "who is the author"
	author <- getLine
	print ("Dear " ++ recipient ++ ", \n" ++ "thanks for buying " ++ title ++ ".\n Sincerely,\n" ++ author)
```
- the key issue above is that the code is one big monolithic function named `messyMain`
- **it's good practice to write modular code** in any language, but **especially Haskell**
- try pulling the 3 parts (recipient, body, signature) out into their own functions
`toPart recipient = "Dear " ++ recipient ++ ",\n"`
- suggest testing each function as you go - write code in an editor
##### Packages
- 


# Types

## Complex Types

# I/O

# Contexts

# Modules & Stacks

# Error Handling / Common libraries