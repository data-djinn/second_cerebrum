[[Functional Programming]] [[Apache Spark]] [[Data Engineering]]

# High-level features
- it's a high-level programming language
	- abstracts away pointers & memory management
	- write high-level with the use of lambdas & high-order functions
```scala
nums.map(i => i * 2) // long form 
nums.map(_ * 2) // short form
nums.filter(i => i > 1) nums.filter(_ > 1)
```
- rather than this:
```import scala.collection.mutable.ListBuffer def double(ints: List[Int]): List[Int] = val buffer = new ListBuffer[Int]() for i <- ints do buffer += i * 2 buffer.toList val oldNumbers = List(1, 2, 3) val newNumbers = double(oldNumbers)```
- just write this:
`val newNumbers = oldNumbers.map(_ * 2`

- it has a concise, readable syntax
	- `val nums = List(1,2,3)`
	- `val p = Person("Martin", "Odersky")`
- it's statically-typed
- expressive type system
- it's a functianal programming language
- it's also a object-oriented programming language
	- every value is an instance of a class
	- every operator is a method
	- all types inherit from a top-level class `Any`, whose immediate children are `AnyVal` (*value types*, such as `Int` or `Boolean`) and `AnyRef` (reference types, as in Java)
	- this means that the Java distinction between primitive types and boxed types (e.g. `int` vs `Integer`) isn't present in Java
	- **Fusion of FP & OOP:
		- Functions for the logic
		- Objects for the modularity
- contextual abstractions provide a clear way to implement *term inference*
- it runs on the JVM (and on the browser!)
	- security
	- performancememory management
	- portability & platform independence
	- the ability to use the wealth of Java & JVM libraries
- interacts seamlessly with pre-existing Java code
	- use java classes & libraries seamlessly in your Scala applications
	- use Scala code in your Java applications
- it's used for server-side applications (including microservices), big data applications, and can also be used in the browser with Scala.js

### Benefits of Scala 3 over Scala 2
- create algebraic data types (ADTs) with enums
- more concise & readable syntax:
	- "quiet" control structure syntax is easier to read
	- optional braces (less visual noise)
- more clear grammar

## FP/OOP fusion
- functions for the logic
- Objects for their modularity
	- e.g. the classes in the standard library
		- e.g. `List` is actually built from a combination of specialized types, including traits named `Iterable`,  `Seq`, and `LinearSeq`
		- also contains many 
```scala
val xs = List(1, 2, 3, 4, 5)
xs.map(_ + 1)  // List(2, 3, 4, 5, 6)
xs.filter(_ < 3) // List(1, 2)
xs.find(_ > 3) // Some(4)
xs.takeWhile(_ < 3)  // List(1, 2)
```
- `List` class is immutable, so all the above methods return new values

## Dynamic feel
- scala's *type inference* makes the language feel dynamically typed, even though it's statically typed
```scala
val a = 1
val b = "Hello World!"
val c = List(1, 2, 3, 4, 5)
val stuff = ("fish", 42, 1_234.5)
```
- also true when passing anonymous functions to higher-order functions:
```scala
list.filter(_ > 4)
list.map(_ * 2)
list.filter(_ < 4).map(_ * 2)
```
- and when defining methods
```scala
def add(a: Int, b: Int) = a + b
// scala3 union type parameter
def help(id: Username | Password) =
	val user = id match
	case Username(name) => lookupName(name)
	case Password(hash) => lookupPassword(hash)
	// more code here
val b: Password | Username = if (true) name else password
```

## Concise Syntax
- creating types like traits, classes, and enumerations are consise
```scala
trait Trail:
	def wagTail(): Unit
	def stopTail(): Unit

enum Topping:
	case Cheese, Pepperoni, Sausage, Mushrooms, Onions

 class Dog extends Animal, Tail, Legs, RubberyNose

case class Person(
	firstName: String,
	lastName: String,
	age: Int
)
```

## Seamless Java integration
- all of the 1000s of java libraries are available in your scala projects
- a scala `String` is just a Java `String`, with additional capabilities added to it
- scala seamlessly uses the date/time classes in the Java `java.time._` package
- use Java collections in Scala
	- Scala includes methods to convert them to Scala collections for the additional functionality

## Standard library methods
- you will rarely need to write custom `for` loop again, due to the dozens of pre-built functional methods in the scala standard library
	- save time
	- make code more consistent across different applications
#### Example `List` methods
```scala
List.range(1, 3) // List(1, 2) 
List.range(start = 1, end = 6, step = 2) // List(1, 3, 5) 
List.fill(3)("foo") // List(foo, foo, foo) 
List.tabulate(3)(n => n * n) // List(0, 1, 4) 
List.tabulate(4)(n => n * n) // List(0, 1, 4, 9) 

val a = List(10, 20, 30, 40, 10) // List(10, 20, 30, 40, 10) 
a.distinct // List(10, 20, 30, 40) 
a.drop(2) // List(30, 40, 10) 
a.dropRight(2) // List(10, 20, 30)
a.dropWhile(_ < 25) // List(30, 40, 10) 
a.filter(_ < 25) // List(10, 20, 10) 
a.filter(_ > 100) // List() 
a.find(_ > 20) // Some(30) 
a.head // 10 a.headOption // Some(10) 
a.init // List(10, 20, 30, 40) 
a.intersect(List(19,20,21)) // List(20) 
a.last // 10 
a.lastOption // Some(10) 
a.map(_ * 2) // List(20, 40, 60, 80, 20) 
a.slice(2, 4) // List(30, 40) 
a.tail // List(20, 30, 40, 10) 
a.take(3) // List(10, 20, 30) 
a.takeRight(2) // List(40, 10) 
a.takeWhile(_ < 30) // List(10, 20) 
a.filter(_ < 30).map(_ * 10) // List(100, 200, 100) 

val fruits = List("apple", "pear")
fruits.map(_.toUpperCase) // List(APPLE, PEAR) 
fruits.flatMap(_.toUpperCase) // List(A, P, P, L, E, P, E, A, R) 

val nums = List(10, 5, 8, 1, 7) 
nums.sorted // List(1, 5, 7, 8, 10) 
nums.sortWith(_ < _) // List(1, 5, 7, 8, 10) 
nums.sortWith(_ > _) // List(10, 8, 7, 5, 1)
```

## Built-in best practices
- scala idioms encourage best practices
- e.g. create immutable `val` declarations
	- immutable collection classes like `List` and `Map` are also encouraged
`val c = Map(1 -> "one") // immutable!`
- case classes are primarily intended for use in [[Domain Modeling in Scala]], and their parameters are immutable:
```scala
case class Person(name: String)
val p = Person("Michael Scott")
p.name // Michael Scatt
p.name = "Joe" // compiler error! (reassignment to val name
```
- scala collections classes support higher-order functions, and you can pass methods (not shown) and anonymous functions into them:
```scala
a.dropWhile(_ < 25)
a.filter( < 25)
a.takewhile(_ < 30)
a.filter(_ < 30).map(_ * 10)
nums.sortWith(_ < _)
nums.sortWith(_ < _)
```
- match expressions let you use pattern matching, and they truly are *expressions* that return values:
```scala
val numAsString = i match
	case 1 | 3 | 5 | 7 | 9 => "odd"
	case 2 | 4 | 6 | 8 | 10 => "even"
	case _ => "too big!"
```
- because they can return values, they're often used as the bodf of a method:
```scala
def isTruthy(a: Matchable) = a match
	case 0 | "" => false
	case _ => true
```

## Ecosystem libraries
- scala libraries for functional programming like [[Cats]] or [[Zio]] are leading-edge libraries in the FP community
	- type-safe
	- high-performance
	- concurrent
	- async
	- resource-safe
	- testable
	- modular
	- binary-compatible
	- efficient

## Strong type system
- vast improvements over scala2
### Simplification
- ~~`implicit`~~ => `given`, `using`

### Eliminating inconsistencies via dropped/changed/added features
- intersection types
- union types
- implicit function types
- trait parameters
- generic tuples

### Safety
- multiversal equality
- restricting implicit conversions
- Null safety
- Safe initialization
```scala
// enumeration
enum Color:
	case Red, Green, Blue

//extension methods
extenstion (c: Circle)
	def circumference: Double = c.radius * math.Pi * 2
	def diameter: Double = c.radius * 2
	def area: Double = math.Pi * c.radius * c.radius
```

### Performance
- opaque types: "operations on these wrapper types must not create any extra overhead at runtime while still providing a type safe use at compile time


## Scala REPL
- start with `scala3` command (or just `scala`, depending on your install)
```scala
scala> 1 + 1 val //res0: Int = 2
scala> 2 + 2 val //res1: Int = 4
// use result vars in subsequent expressions
scala> val x = res0 * 10 //valx: Int = 20
```

# Variables
2 types of variables
1. `val`: *Immutable variable*. Always prefer this one, unless you really need mutability
2. `var` *mutable variable*. Use selectively

#### Declare variable types
- you can explicitly declare its type, of let the compiler infer the type
```val x: Int = 1 // explicit
val x = 1 // implicit, compiler infers the Int type
```

# Built in data types
[[Scala types]]
- in scala, everything is an object
```scala
val b: Byte = 1
val i: Int = 1
val l: Long = 1
val s: Short = 1
val d: Double = 2.0
val f: Float = 3.0
```
- because `Int` and `Double` are the default numeric types, you typically create them without explicitly declaring the data type:
```scala
val i = 1234 // defaults to Int
val j = 1.0 // defaults to Double
```
- you can also append the characters L, D, and F (and their lowercase equivalents) to numbers to specify they are `Long`, `Double`, or `Float` values
```
val x = 1_000L // val x: Long = 1000
val y = 2.2D // val y: Double = 2.2
val z = 3.3F // val z: Float = 3.3
```
- use `BigInt` and `BigDecimal` for really large numbers


# Control Structures
[[Scala control structures]]
### `if`/`else`
```scala
  if x < 0 then
	  println("negative")
else if x==0 then
	println("zero")
else
	println("positive")
// note this is really an expression, so you can assign the result to a variable
val x = if a <
```
### `for` loops
```scala
val ints = List(1, 2, 3, 4, 5)
for i <- ints do println(i) // i <- ints is a generator!
// use nested if expr (referred to as "guards"
for
	i <- ints
	if i > 2
do println(i)
// use multiple generators and guards
for
	i <- 1 to 3
	j <- 'a' to 'c'
	if i == 2 
	// implicit AND logical operator
	if j == 'b'
do
println(s"i = $i, j = $j")
```
#### `for` *expressions*
	- use the `yield` keyword instead of `do`
```scala
val doubles = for i <- ints yield i * 2 // val doubles: List[Int] = List(2, 4, 6, 8, 10)
// the following are syntactically equivalent
val doubles = for (i <- ints) yield i * 2 
val doubles = for (i <- ints) yield (i * 2) 
val doubles = for { i <- ints } yield (i * 2)
```

### `match` expressions
```scala
val i = 1
// Later in the code...
val result = i match
	case 1 => println("one")
	case 2 => println("two")
	case _ => println("other")
// can be used with any data type
val p = Person("Fred")
p match
	case Persan(name) if name == "Fred" =>
		println(s"$name says, Yubba dubba doo")

	case Person(name) if name == "Bam Bam" =>
		println(s"$name says, Bam bam!")

	case _ => println("Watch the Flintstones!")

// test a variale against many different types of patterns
def getClassAsString(x: Matchable): String = x match
	case s: String => s"'$s' is a String"
	case i: Int => "Int"
	case d: Double => "Double"
	case l: List[?] => "List"
	case _ => "Unknown"

//examples
getClassAsString(1) // Int
getClassAsString("hello") // 'hello' is a String
getClassAsString(List(1, 2, 3))
```
### `while` loops
```scala
while x >= 0 do x = f(x)

var x = 1
while
	x > 3
do
	println(x)
	x += 1
```

### `try`/`catch`/`finally`
```scala
try
	writeTextToFile(text)
catch
	case ioe: IOException => println("Got an IOException.")
	case nfe: NumberFormatException => println("Got a NumberFormatException.")
finally
	println("Clean up your resources here.")
```

# Domain Modeling
[[Domain Modeling in Scala]]
## OOP-style
- 2 main tools for **data encapsulation**:
	- **traits**
	- **classes**
### Traits
- can be used as simple interfaces
- can also contain abstract and concrete methods & fields
- can have parameters just like classes
- ==organize behaviors into small, modular units==
	- later, when you want to create concrete implementations of attributes & behaviors, classes and objects can extend traits, mixing in as many traits as needed to achieve the desired behavior
```scala
trait Speaker:
	def speak(): String  // has no body, so it's abstract

trait TailWagger:
	def startTail(): Unit = println("tail is wagging")
	def stopTail(): Unit = println("tail is stopped")

trait Runner:
	def startRunning(): Unit = println("I'm running")
	def stopRunning(): Unit = println("Stopped running")

class Dog(name: String) extends Speaker, TailWagger, Runner:
	def speak(): String = "Woof!"

class Cat(name: String) extends Speaker, TailWagger, Runner:
	def speak(): String = "Meow"
	override def startRunning(): Unit = println("Yeah... I don't run")
	override def stopRunning(): Unit = println("I never started running :)")

val rover = Dog("Rover")
println(rover.speak())  // prints "Woof!"

val morris = Cat("Morris")
println(morris.speak())  // prints "Meow"
morris.startRunning()
```

## Classes
- fields are typically mutable in OOP, so we use `var` parameters
```scala
class Person(var firstName: String, var lastName: String):  // creates a constructor
	def printFullName() = println(s"$firstName $lastName")

val p = Person("John", "Stephens")  // uses the constructor
println(p.firstName)  // "John"
p.lastName = "Legend"
p.printFullName()  // "John Legend"
```

## FP-style
- **algebraic data types to define the data**
- **Traits for functionality on the data**
### Enums & Sum types
- Sum types are one way to model algebraic data types (ADTs) in scala
	- used when data can be represented with different choices (e.g. a pizza's size, crust type, toppings, etc.)
	- modeled with Enumerations (sum types that only contain singleton values)
```scala
enum CrustSize:
	case Small, Medium, Large

enum CrustType
	case Thin, Thick, Regular

enum Topping:
	case Cheese, Pepperoni, BlackOlives, GreenOlives, Onions

import CrustSize.*
val currentCrustSize = Small

// enums in a `match` expression
currentCrustSize match
	case Small => println("Small crust size")
	case Medium => println("Medium crust size")
	case Large => println("Large crust size")

if currentCrustSize == Small then println("Small crust size")

// below is an example of a sum type, without enumeration (because Succ case has params)
enum Nat:
	case Zero
	case Succ(pred: Nat)
```

### Product types
- ADT that has only one shape, e.g. a singleton object, or an immutable structure with accessible fields, represented with a `case` class
	- a `case` class has all the functionality of a `class`, with additional features:
		- case class constructor parameters are public `val` fields by default, so fields are immutable
		- accessor methods are generated for each parameter
	- `unapply` method is generated, which lets you use case classes in more ways in `match` expressions
	- `copy` method is generated in the class - this provides a way to create updated copies of the object without changing the original object
	- `equals` and `hashCode` methods are generated to implement structural equality
	- default `toString` method is generated (helpful for debugging)
```scala
case class Person(
	name: String,
	vocation: string
)

val p = Person("Reginald Kenneth Dwight", "Singer")
p  // default to toString method

p.name
p.name = "Joe"  // Errer" can't reassing a val field

// when you need to make a change, use the `copy` method
val p2 = p.copy(name="Elton John")
```

# Methods
- classes, case classes, traits, enums, and objects all contain methods
```scala
def methodName(param1: Type1, param2: Type2): ReturnType =
	//method body goes here

def sum(a: Int, b: Int) = a + b
def concatenate(s1: String, s2: String) = s1 + s2

// you can assign defaultvalues
def makeConnection(url: String, timeout: Int = 5000): Unit =
	println(s"url=$url, timeout=$timeout")

// you can use named parameters when calling the method
makeConnection(
	url = "https://localhost",
	timout = 6000
	)
```

# First-class Functions
### lambdas (anonymous functions)
- help keep your program concise & readable
### higher-order functions (HOFs)
- function that takes another function as a parameter
- `map` method of the `List` class is one example 
```scala
val a = List(1, 2, 3).map(i => i * 2)  // List(2, 4, 6)
val b = List(1, 2, 3).map(_ * 2)  // equivalent as above

// this would be more verbose without lambdas:
def double(i: Int): Int = i * 2
val a = List(1, 2, 3).map(i => double(i))
val b = List(1, 2, 3).map(double)
```

### immutable collections in the standard library
- `List`, `Vector`, `Map`, `Set` are all immutable
	- so, they return a new collection containing the updated data
	- thus it's common to "chain" opetaions together
```scala
val nums = (1 to 10).toList  // List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

val x = nums.filter(_ > 3)
			.filter(_ < 7)
			.map(_ * 10)
// result: x == List(40, 50, 60)
```

# Singleton objects
- `object` keyword creates a Singleton object: a class that can only have exactly one instance
	- used to create collections of utility methods
	- *companion object* has the same name as the class ("companion class") it shares a file with
- They're used to implement traits to create modules

## Utility methods
- Singleton `object`s' methods can be accessed like static methods in a Java class
```scala
object StringUtils
	def isNullOrEmpty(s: String): Boolean = s == null || s.trim.isEmpty
	def leftTrim(s: String): String = s.replaceAll("^\\s+", "")
	def rightTrim(s: String): String = s.replaceAll("\\s+$", "")

// because StringUtils is a singleton, its methods can be called directly on the object
val x = StringUtils.isNullOrEmpty("")  // true
val x = StringUtils.isNullOrEmpty("a")  // false
```

## Companion Objects
- companion class or object can access the private members of its companion
	- use for methods & values which aren't specific to instances of the companion class
```scala
import scala.math.*
class Circle(radius: Double):
	import Circle.*
	def area: Double = calculateArea(radius)

object Circle:
	private def calculateArea(radius: Double): Double =
		Pi * pow(radius, 2.0)  // raise to the power of 2

val circle1 = Circle(5.0)
circle1.area  // Double = 78.53981633974483
```
- `object`s can be used to implement traits to create modules
	- this technique takes 2 traits and combines them to create a concrete `object`:
```scala
trait AddService:
	def add(a: Int, b: Int) = a + b

trait MultiplyService:
	def multiply(a: Int, b: Int) = a * b

object MathService extends AddService, MultiplyService

// use the object
import MathService.*
println(add(1, 1))  // 2
println(multiply(2, 2)) // 4
```

# Collections
### `List`
- immutable, linked-list class
```scala
val a = List(1, 2, 3)  // a: List[Int] = List(1, 2, 3)

// range methods
val b = (1 to 5).toList // b: List[Int] = List(1, 2, 3, 4, 5) 
val c = (1 to 10 by 2).toList // c: List[Int] = List(1, 3, 5, 7, 9) 
val e = (1 until 5).toList // e: List[Int] = List(1, 2, 3, 4) 
val f = List.range(1, 5) // f: List[Int] = List(1, 2, 3, 4) 
val g = List.range(1, 10, 3) // g: List[Int] = List(1, 4, 7)
```
- all functional methods, returning a new collection with the updated elements
```scala
// a sample list
val a = List(10, 20, 30, 40, 10)      // List(10, 20, 30, 40, 10)

a.drop(2)                             // List(30, 40, 10)
a.dropWhile(_ < 25)                   // List(30, 40, 10)
a.filter(_ < 25)                      // List(10, 20, 10)
a.slice(2,4)                          // List(30, 40)
a.tail                                // List(20, 30, 40, 10)
a.take(3)                             // List(10, 20, 30)
a.takeWhile(_ < 30)                   // List(10, 20)

// flatten
val a = List(List(1,2), List(3,4))
a.flatten                             // List(1, 2, 3, 4)

// map, flatMap
val nums = List("one", "two")
nums.map(_.toUpperCase)               // List("ONE", "TWO")
nums.flatMap(_.toUpperCase)           // List('O', 'N', 'E', 'T', 'W', 'O')

val firstTen = (1 to 10).toList            // List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

firstTen.reduceLeft(_ + _)                 // 55
firstTen.foldLeft(100)(_ + _)              // 155 (100 is a “seed” value)
```

### `Tuple
- type that allows you to easily put a collection of different types in the same container
```scala
//given this class
case class Person(name: String)
// create a tuple
val tup = (11, "eleven", Person("Eleven"))

// access them by index
t(0)  // 11
t(1)  // "eleven
t(2)  // Person("Eleven"()

// can also use an "extractor approach to assign the tuple fields to var names
val (num, str, person) = t 

// result:
// val num: Int = 11 
// val str: String = eleven 
// val person: Person = Person(Eleven)
```
- tuples are nice when you want to group a collection of heterogeneous types into a single collection

# Contextual abstractions
- under certain circumstances, you can omit some parameters of method calls that are considered repetitive
- those parameters are called *Context Parameters* because they are inferred by the compiler from the context surrounding the method call
```scala
val addresses: List[Address] = ...
addresses.sortBy(address => (address.city, adress.street))
```
- the `sortBy` method takes a function that returns, for every address, the value to compare it with the other addresses
	- in this case, we pass a function that returns a pair containing the city name & the street name
	- note that we only indicate *what* to compare, but not *how* to perform the comparison
	- how does the sorting algorithm know how to compare pairso of strings? ==context parameter==
- you can also pass it explicitly:
`addresses.sortBy(address => (address.city, address.street))(using Ordering.Tuple2(Ordering.String, Ordering.String))`

# Top level definitions
- all kinds of definitions can be written at the "top level" of your source code files
	- no need to put these definitions in a `package`, `class`, or other construct
	- e.g. create a file named `MyCoolApp.scala` and put this into it:
```scala
import scala.collection.mutable.ArrayBuffer

enum Topping:
  case Cheese, Pepperoni, Mushrooms

import Topping.*
class Pizza:
  val toppings = ArrayBuffer[Topping]()

val p = Pizza()

extension (s: String)
  def capitalizeAllWords = s.split(" ").map(_.capitalize).mkString(" ")

val hwUpper = "hello, world".capitalizeAllWords

type Money = BigDecimal

// more definitions here as desired ...

@main def myApp =
  p.toppings += Cheese
  println("show me the code".capitalizeAllWords)
```

