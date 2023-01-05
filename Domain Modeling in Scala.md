[[Scala]]
# Tools
## Classes
- template for the creation of object instances
```scala
class Person(var name: String, var vocation: String)
class Book(var title: String, var author: String, var year: Int)
class Movie(var name: String, var director: String, var year: Int)

val p = Person("Rodney F Mullen", "Skateboarder")

// access fields with dot notation
p.name  // "Rodney F Mullen"
p.vocation  // "Skateboarder"

// since class params are var fileds, we can mutate them
p.name = "Tony Hawk"
p.vocation = "Skate God"
```
- parameters can be either mutabel `var` or immutable `val`

- classes can also have methods & additional fields that are not part of constructors
	- defined in the body of the class

```scala
class Person(var firstName: String, var lastName: String):

// initialization begins
  val fullName = firstName + " " + lastName

  // a class method
  def printFullName: Unit =
    // access the `fullName` field, which is created above
    println(fullName)

  printFullName
// initialization ends
```

- class constructor parameters can also have default values
```scala
class Socket(val timeout: Int = 5_000, val linger: Int = 5_000):
  override def toString = s"timeout: $timeout, linger: $linger"

// lets users of your code create classes in a variety of different ways
val s = Socket()  // timeout: 5000, linger: 5000
val s = Socket(2_500)  // timeout: 2500, linger: 5000
val s = Socket(10_000, 10_000)  //timeou: 10000, linger: 10000
val s = Socket(timeout = 10_000)  // timout: 10000, linger: 5000
val s = Socket(linger = 10_0000)  // timout: 5000, linger: 10000
```

#### Auxiliary constructors
- define a class to have multiple constructors so consumers of your class can build it in different ways

```scala
import java.time.*

// [1] the primary constructor
class Student(
  var name: String,
  var govtId: String
):
  private var _applicationDate: Option[LocalDate] = None
  private var _studentId: Int = 0

  // [2] a constructor for when the student has completed
  // their application
  def this(
    name: String,
    govtId: String,
    applicationDate: LocalDate
  ) =
    this(name, govtId)
    _applicationDate = Some(applicationDate)

  // [3] a constructor for when the student is approved
  // and now has a student id
  def this(
    name: String,
    govtId: String,
    studentId: Int
  ) =
    this(name, govtId)
    _studentId = studentId
```

## Objects
- class that has exactly one instance (singleton)
- evaluated lazily when its members are referenced
- objects in scala enable grouping methods and fields under one namespace, similar to `@staticmethod` in python
```scala
object StringUtils:
  def truncate(s: String, length: Int): String = s.take(length)
  def containsWhitespace(s: String): Boolean = s.matches(".*\\s.*")
  def isNullOrEmpty(s: String): Boolean = s == null || s.trim.isEmpty

StringUtils.truncate("Chuck Bartowski", 5) // "Chuck"

import StringUtils.{truncate, containsWhiteSpace, isNullOrEmpty}  // use * to import all 
truncate("Chuck Bartowski", 5) // "Chuck"
containsWhitespace("Sarah Walker") // true 
isNullOrEmpty("John Casey") // false

// objects can also contain fields, which are also accessed like static members:
object MathConstants {
	val PI = 3.14159
	val E = 2.71828
}

println(MathConstants.PI)  // 3.14159
```

## Companion objects
- ==an `object` that has the same name as a class, & is declared in the same file as the class==
	- corresponding class is called the object's *companion class*
	- like a static method in python
- a companion class or object can access the private members of its companion
- used for methods and values that are not specific to instances of the companion class
	- e.g.: `Circle` has a member named `area` which is specific to each instance
	- companion object has a **method that's not specific to a class instance, and is available to every instance**
```scala
import scala.math._

class Circle(val radius: Double) {
	def area: Double = Circle.calculateArea(radius)
}

object Circle {
	private def calculateArea(radius: Double): = Pi * pow(radius, 2.0)
} // because calculateArea is private, it can't be accessed by other code

val circle1 = new Circle(5.0)
circle1.area 
```
#### Useful for:
- static methods under a shared namespace
	- methods can be public or private
	- if `calculateArea` was public, it would be accessed as `Circle.calculateArea`
- can contain **`apply` methods**: ==factory methods to construct new instances==
- can also contain **`unapply` methods**: ==used to deconstruct objects== (such as with pattern matching)
```scala
class Person {
  var name = ""
  var age = 0
  override def toString = s"$name is $age years old"
}

object Person {
  // a one-arg factory method
  def apply(name: String): Person = {
    var p = new Person
    p.name = name
    p
  }

  // a two-arg factory method
  def apply(name: String, age: Int): Person = {
    var p = new Person
    p.name = name
    p.age = age
    p
  }
}

val joe = Person("Joe")
val fred = Person("Fred", 29)

//val joe: Person = Joe is 0 years old
//val fred: Person = Fred is 29 years old
```

## Traits
- similar to an interface in [Java]
- can contain:
	- abstract methods & fields
	- concrete methods & fields
- can be used as an interface, defining only abstract members that will be implemented by other classes
```scala
trait Employee {
	def id: Int
	def firstName: String
	def lastName: String
}

// traits can also contain concrete members
trait HasLegs {
	def numLegs: Int  // abstract
	def walk(): Unit  // abstract
	def stop() = println("Stopped walking")  // concrete method
}

trait HasTail {
	def tailColor: String
	def wagTail() = println("Tail is wagging")
	def stopTail() = println("Tail is stopped")
}
```
- note how each trait only handles very specific attributes & behaviors: `HasLegs` deals only with legs, and `HasTail` deals only with tail-related functionality
	- Traits let you build small modules like this
- later in your code, classes can mix multiple traits to build larger components:
```scala
class IrishSetter(name: String) extends HasLegs, HasTail:
  val numLegs = 4
  val tailColor = "Red"
  def walk() = println("I’m walking")
  override def toString = s"$name is a Dog"

// IrishSetter class implements the abstract methods that are defined in HasLegs & HasTail
val d = new IrishSetter("Big Red") // "Big Red is a Dog"
```

## Abstract Classes
- better to use than traits when:
	- you want a base class that takes constructor arguments
	- the code will be caled from Java code
```scala
abstract class Pet(name: String) {
  def greeting: String
  def age: Int
  override def toString = s"My name is $name, I say $greeting, and I’m $age"
}

class Dog(name: String, var age: Int) extends Pet(name) {
  val greeting = "Woof"
}

val d = new Dog("Fido", 1)
```
- with scala3 traits can have parameters, so you can use traits in the same situation:
```scala
trait Pet(name: String):
  def greeting: String
  def age: Int
  override def toString = s"My name is $name, I say $greeting, and I’m $age"

class Dog(name: String, var age: Int) extends Pet(name):
  val greeting = "Woof"

val d = Dog("Fido", 1)
```
- traits are more flexible & should be preferred to abstract classes most of the time
	- can mix in mulitple traits, but only extend one class 
- **use classes whenever you want to create instances of a particular type, & traits when you want to decompose & reuse behavior**

## Enums (scala3 only!)
- define a type that consists of a finite set of named values
	- sets of constants like days in the week, months in year, etc
```scala
enum CrustSize:
  case Small, Medium, Large

enum CrustType:
  case Thin, Thick, Regular

enum Topping:
  case Cheese, Pepperoni, BlackOlives, GreenOlives, Onions

import Crustsize.*
val currentCrustSize = Small

// can be compared using ==, and matched on
if currentCrustSize == Large then
	println("You get a prize!")

currentCrustSize match
	case Small => println("small")
	case Medium => println("medium")
	case Large => println("large")


// enums can be parameterized
enum Color(val rgb: Int):
	case Red extends Color(0xFF0000)
	case Green extends Color(0x00FF00)
	case Blue extends Color(0x0000FF)

// enums can also have members (like fields & methods)
enum Planet(mass: Double, radius: Double):
	private final val G = 6.67300E-11
	def surfaceGravity = G * mass / (radius * radius)
	def surfaceWeight(otherMass: Double) = otherMass * surfaceGravity

case Mercury extends Planet(3.303e+23, 2.4397e6)
case Earth extends Planet(5.976e+24, 6.37814e6)
```

#### Compatible with Java Enums
- exend the class `java.lang.Enum`:
```scala
enum Color extends Enum[Color] { case Red, Green, Blue }

Color.Red.CompareTo(Color.Green) // val res0: Int = -1
```

### Case Classes
- model immutable data structures
`case class Person(name: String, relation: String)`
- fields `name` & `relation` are public & immutable by default
`val christina = Person("Christina", "niece")`
- because of immutability, Scala compiler can generate many helpful methods for you:
	- an `unapply` method is generated, which allows you to perform pattern matching on a case class (that is, `case Person(n, r) => ...)`
	- a `copy` method is generated in the class, which is very useful to create modified copies of an instance
	- `equals` and `hashCode` methods using structural equality are generated, allowing you to use instances of a case c
	- a default `toString` method is generated (helpful for debugging)
```scala
// Case classes can be used as patterns
christina match {
  case Person(n, r) => println("name is " + n)
}

// `equals` and `hashCode` methods generated for you
val hannah = Person("Hannah", "niece")
christina == hannah       // false

// `toString` method
println(christina)        // Person(Christina,niece)

// built-in `copy` method
case class BaseballTeam(name: String, lastWorldSeriesWin: Int)
val cubs1908 = BaseballTeam("Chicago Cubs", 1908)
val cubs2016 = cubs1908.copy(lastWorldSeriesWin = 2016)
// result:
// cubs2016: BaseballTeam = BaseballTeam(Chicago Cubs,2016)

```
### Support for functional programming
- in FP, you don't mutate data structures - it thus makes sense that constructor fields default to `val`
	- since instances of a class can't be changed, they can easily be shared without fearing mutation or race conditions
- instead of mutating an instance, you can use the `copy` method as a template to create a new (potentially unchanged) instance
	- "update as you copy"
- having an `unapply` method auto-generated for you also lets case classes be used in advanced ways with pattern matching
### Case objects
- are to objects what case classes are to classes: provide a number of automatically-generated methods to make them more powerful
- particularly useful whenever you need a singleton object that needs a little extra functionality, such as being used with pattern matching in `match` expressions
- useful when you need to pass immutable messages around:
```scala
sealed trait Message
case class PlaySong(name: String) extends Message
case class IncreaseVolume(amount: Int) extends Message
case class DecreaseVolume(amount: Int) extends Message
case object StopPlaying extends Message
// assuming the methods below are defined somewhere else:
def handleMessages(message: Message): Unit = message match
  case PlaySong(name)         => playSong(name)
  case IncreaseVolume(amount) => changeVolume(amount)
  case DecreaseVolume(amount) => changeVolume(-amount)
  case StopPlaying            => stopPlayingSong()
```
# OOP
## Traits
```

# FP