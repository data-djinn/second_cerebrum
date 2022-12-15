[[Scala]]
# Classes
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

# Objects
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
```

# Companion objects
