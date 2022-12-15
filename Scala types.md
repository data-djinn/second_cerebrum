![[Pasted image 20221206204116.png]]

## Scala type hierarchy
- `Any` is the supertye of all types ("top type")
	- defines certain universal methods such as `equals`, `hashCode`, and `toString`
- subtype `Matchable` marks all types we can perform pattern matching on
- subtypes `AnyVal` & `AnyRef`
	- `AnyVal` represents non-nullable value types
		- `Double`
		- `Float`
		- `Long`
		- `Short`
		- `Byte`
		- `Char`
		- `Unit` (carries no meaningful information: only one instance  `()`)
			- also used as scala's `None` value
		- `Boolean`
	- `AnyRef` represents reference types (non-value types, user-defined types)

```scala
val list: List[Any] = List(
	"a string",
	732,  // Int
	'c',  // Char
	true,  // Boolean
	() => "a lambda func returning a string"
)

list.foreach(element => println(element))
```

# Scala's "value types"
- full-blown objects that extend `AnyVal`
```scala
val i: Int = 1 // could leave out type here
val b: Byte = 1  // would befault to Int if type not specified
val l: Long = 1  // ""
val s: Short = 1 // ""
val d: Double = 2.0
val f: Float = 3.0  // would default to Float if type not specified
```
- can also use the characters `L`, `D`, and `F`
```scala
val x = 1_000L  // val x: Long = 1000
val y = 2.2D  // val y: Double = 2.2
val z = 3.3F  // val z: Float = 3.3
// can also declare Strings & chars implicitly
val s = "Bill"
val c = 'a'
```

## `BigInt` & `BigDecimal
- `Double` and `Float` are approximate decimal numbers
- use `BigInt` & `BigDecimal` for precise arithmetic, e.g. currency
- all math operators supported

## Strings
- support interpolation by preceding the string with `s` and putting a `$` before the variable name
- easy to create multi-line strings
```scala
val firstName = "John"
val mi = 'C'
val lastName = "Doe"
println(s"Name: $firstname $mi $lastName")

// enclose larger expressions in curly braces
println(s"2 + 2 = ${2 + 2}")

// multiline string
val quote = """The essense of Scala:
			|Fusion of functional and OOP
			|in a typed setting.""".stripMargin
```

# Type casting
![[Pasted image 20221207065101.png]]
```scala
val b: Byte = 127
val i: Int = b // 127

val face: Char = '☺'
val number: Int = face  // 9786

// you can only cast to a type if there is no loss of info - otherwise need to be explicit
val x: Long = 987654321
val y: Float = x.toFloat // 9.8765434E8 (note that `.toFloat` is required because the cast
val z: Long = y  //  Error
```

# `Nothing` & `null`
- `Nothing` is a subtype of all types, also called the **bottom type**
	- no value that has the type `Nothing`
	- commonly used to signal non-termination, such as thrown exception, program exit, or infinite loop
		- i.e. type of an expression that does not evaluate to a value, or a method that does not return normally
- `Null` is a subtype of all reference types (i.e. any subtype of `AnyRef`)
	- it has a single value identified by the keyword literal `null`
	- **BAD PRACTICE**
	- used for interoperability with other JVM languages