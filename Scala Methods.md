[[Scala]]
- in scala 2, methods must be *encapsulated* in a class, trait, etc
- scala 3 permits "top-level" method definitions, defined anywhere!

features include:
- generic (type) params
- default parameter values
- multiple parameter groups
- context-provided parameters (closures?)
- keyword parameters

```scala
def methodName(param1: Type1, param2: Type2): ReturnType = 
	// method body
end methodName  // optional

def add(a: Int, b: Int): Int = a + b

val x = add(1, 2)
```

- [[Scala collections]] have methods:
```scala
val x = List(1, 2, 3)

x.size // 3
x.contains(1) // true
x.map(_ * 10) // List(10, 20, 30)
```
### Methods that take no parameters ("arity-0")
- if the method has side-effects, declare the method with parentheses
- if the method does not perform side-effects, leave the parentheses off


## Controlling visibility in classes
- in classes, traits, objects, & enums, methods are public by default
```scala
class Dog:
	def speak() = println("Woof")

val d = new Dog
	d.speak()
```

- methods can also be marked as `private`
- this makes them private to the current class, so *they can't be called or overwritten in subclasses*
```scala
class Animal:
	private def breathe() = println("I'm breathing")

class Cat extends Animal:
	// this method won't compile!
	override def breathe() = println("Yo, I'm totally breathing")
```

- if you want to make a method private to the current class & also allow subcclasses to call or override it, mark the method as `protected`:
```scala
class Animal:
	private def breathe() = println("I'm breathing")
	def walk() = 
		breathe()
		println("I'm walking here")
	protected def speak() = println("hi?")

class Cat extends Animal:
	override def speak() = println("Meow")

val cat = new Cat
cat.walk()
cat.speak()
cat.breathe()  // won't comile because it's private
```

the protected setting means:
- the method (or field) can be accessed by other instances of the same class
- it is not visible by other code in the current package
- it is available to subclasses

### Objects can contain methods
```scala
object StringUtils {

  /**
   * Returns a string that is the same as the input string, but
   * truncated to the specified length.
   */
  def truncate(s: String, length: Int): String = s.take(length)

  /**
    * Returns true if the string contains only letters and numbers.
    */
  def lettersAndNumbersOnly_?(s: String): Boolean =
    s.matches("[a-zA-Z0-9]+")

  /**
   * Returns true if the given string contains any whitespace
   * at all. Assumes that `s` is not null.
   */
  def containsWhitespace(s: String): Boolean =
    s.matches(".*\\s.*")

}
```

## Extension methods
- there are many situaions where you would like to add functionality to closed classes
```scala
case class Circle(x: Double, y: Double, radius: Double)

extension (c: Circle)
	def circumference: Double = c.radius * math.Pi * 2
	def diameter: Double = c.radius * 2
	def area: Double = math.Pi * c.radius * c.radius
```

# Main methods
- adding `@main` annotation to a method turns it into entry point of an executable program:
`@main def hello(): Unit = println("Hello, world!")`
- to run this program, save the line of code in a file named e.g. `Hello.scala` & run it with: `scala Hello.scala` --> `Hello, world!`
- an `@main` annotated method can be written either at the top-level (as shown), or inside a statically accessible `object` (*not* a `class`)
- you can add multiple `@main` entrypoints - if there are multiple, sbt will ask you which one you want to run
### command line args
- parameters of your `@main` method becomes the parameter of your program
```scala
@main def run(x: int, s: String): Unit = 
	println(s"Hello, I got ${x} and ${s}")
```
then: `sbt:mypackage> run 42 foo`
### Variable number of args
```scala
@main def run(x: Int*): Unit = 
	println(s"Hello, I got ${x} and ${s}")
```

### Custom argument parsing
```scala
import scala.util.CommandLineParser

given CommandLineParser.FromString[Color] with
	def fromString(value: String): Color = 
		Color.valueOf(value)

enum Color:
	case Red, Green, Blue

@main def run(color: Color): Unit =
	println(s"The color is ${color.toString}")
```
