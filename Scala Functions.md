[[Scala]]

# Anonymous Functions
- block of code that's passed as an argument to a higher-order function
- a function definition that is not bound to an identifier
`val ints = List(1, 2, 3)`
`val doubledInts = ints.map(_ * 2)  // List(2, 4, 6)`
equivalent to:
```scala
val doubledInts = ints.map((i: Int) => i * 2)
val doubledInts = ints.map((i) => i * 2)
val doubledInts = ints.maps(i => i * 2)
```

`val doubledInts = ints.map((i) => i * 2)`

because there's only one argument, the parentheses are optional
- scala lets you use the `_` symbol instead of a variable name when the parameter appears only once in your function, the code can be simplified to:
`val doubledInts = ints.map(_ * 2)`

- if an anonymous function consists of one method that takes a single argument, you don't need to explicitly name & specify the argument - you can write only the name of the method:
`ints.forearch(doubledInts)`

# Function Variables
- anonymous functions can be assigned to a variable to create a *function variable*:
`val double = (i: Int) => i * 2`
- the double function now has a type `Int => Int`
- invoke like `val res = double(2)  // 4`
- you can also map it: `List(1, 2, 3).map(double)  // List(2, 4, 6)`
- you can store other functions of the same type (`Int => Int`) in a `List` or `Map:
```scala
val triple = (i: Int) => i * 3

val  functionList = List(double, triple)
val functionMap = Map(
	"2x" -> double,
	"3x" -> triple
)
funtionList.getClass  // List[Int => Int]
functionMap.getClass // Map[String, Int => Int]
```
#### Method eta expansion
- in scala3, you can treat methods as any other variable in the same way

# higher order functions
==function that takes other functions as input parameters, and/or returns a function as a result== (think `map` & `filter`)

## `filter`
`def filter(p: A => Boolean): List[A]`
- `p` stands for *predicat*e*: a function that returns a `Boolean` value
- `A` is the type held in the list
- `p: A => Boolean` means that whatever function you pass in must take the type `A` as an input parameter and return a `Boolean`

## Writing methods that take function parameters
1. In your method's parameter list, define the signature of the function you want to accept
2. Use that function inside your method
`def sayHello(f: () => Unit): Unit = f()`
- `f` is the name of the function input parameter
- `()` signifies `f` is a function, that takes no input parameters
- `Unit` indicates that `f` does not return a meaningful result
- `f()` at the end invokes the function that's passed in
```scala
def helloJoe(): Unit = println("Hello, Joe")
sayHello(helloJoe) // prints "Hello, Joe")
```
- you can pass any function that matches `f`'s signature
	- e.g. all the below would match the signature `f: (Int, Int) => Int`:
		- `def add(a: Int, b: Int): Int = a + b`
		- `def multiply(a: Int, b: Int): Int = a * b`
		- `def subtract(a: Int, b: Int): Int = a - b`
  *because functional programming is like creating a series of algebraic equations, it's common to think about types a lot when designing functions and applications - "think in types"*
### take function parameter along with other params
```scala
def executeNTimes(f: () => Unit, n: Int): Unit =
	for i <- 1 to n do f()

def executeAndPrint(f: (Int, Int) => Int, i: Int, j: Int): Unit =
	println(f(i, j))

def sum(x: int, y: int) = x + y
executeAndPrint(sum, 3, 11)
```
