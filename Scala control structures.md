[[Scala]]
# `if`/`then`/`else`
```scala
if x < 0 then
  println("negative")
  println("reeee")
else if x == 0 then
  println("zero")
else
  println("positive")
```
- **always return a result**
	- so, you can use `if`/`else` expressions as the body of a method:
```scala
def compare(a: Int, b: Int): Int =
  if a < b then
    -1
  else if a == b then
    0
  else
    1
```
- prefer *expressions* (returns values) over *statement* (side-effectful, no return val)

# `for` loops
- iterate over elements in a collection
```scala
val ints = Seq(1,2,3) // ints: Seq[Int] = List(1, 2, 3)
for i <- ints 
do 
	val x = i * 2
	println(x)

// can have multiple generators
for 
    i <- 1 to 2
    j <- 'a' to 'b'
    k <- 1 to 10 by 5
do
	println(s"i = $i, j = $j, k = $k")
```

- use `for` loops with `Map`:
```scala
val states = Map(
	"AK" -> "Alaska",
	"AL" -> "Alabama",
	"AR" -> "Arizona"
)
for (abbrev, fullName) <- states do println(s"$abbrev: $fullName")  // effectful

// use expressions to return values
val list =
	for i <- 10 to 12
	yield i * 2
// list: IndexedSeq[Int] = Vector(20, 22, 24)
```
- `list` is a `Vector` type that contains the values shown
- `for` expression starts to iterate over the values in the range `(10, 11, 12)`
	- first multiplies `10 * 2`, then yelds that result (`20`)
	- then yields result of `11 * 2` (yielded values are stored in abstracted away temp holding place)
	- then yields result of `12 * 2`
	- then loop completes and yields final result
- equivalent to:
`val list = (10 to 12).map(i => i * 2)`
- use `for` expressions whenever you need to traverse all the elementns in a collection & apply an algorithm to those elements to create a new list
- can also use them as the body of a method that returns a value
```scala
def between3and10(xs: List[Int]): List[Int] =
  for
    x <- xs
    if x >= 3
    if x <= 10
  yield x

between3and10(List(1, 3, 7, 11))   // : List[Int] = List(3, 7)
```

# `while` loops
```scala
var i = 0
while i < 3 do
	println(i)
	i += 1
```

# `match` expressions
```scala
// `i` is an integer
val day = i match
  case 0 => "Sunday"
  case 1 => "Monday"
  case 2 => "Tuesday"
  case 3 => "Wednesday"
  case 4 => "Thursday"
  case 5 => "Friday"
  case 6 => "Saturday"
  case _ => "invalid day"   // the default, catch-all
```
- matches are considered in the order written
	- default case *must* come last
- **best practice**: use `@switch` annotation for simple `match` expressions
	- provides a compile-time warning if the switch can't be compiled to a `tableSwitch` or `lookupswitch`
```scala
val evenOrOdd = i match
  case 1 | 3 | 5 | 7 | 9 => println("odd")
  case 2 | 4 | 6 | 8 | 10 => println("even")
  case _ => println("some other number")

// use guards to handle in the cases
i match
  case 1 => println("one, a lonely number")
  case x if x == 2 || x == 3 => println("two’s company, three’s a crowd")
  case x if x > 3 => println("4+, that’s a party")
  case _ => println("i’m guessing your number is zero or less")

i match
  case a if 0 to 9 contains a => println(s"0-9 range: $a")
  case b if 10 to 19 contains b => println(s"10-19 range: $b")
  case c if 20 to 29 contains c => println(s"20-29 range: $c")
  case _ => println("Hmmm...")
```

- because `match` expressions return a value, they can be used as the body of a method
```scala
def isTruthy(a: Matchable) = a match 
	case 0 | "" | false => false
	case _ => true
```

#### Many different types of patters suuprted
- constant patterns (`case 3 =>`)
- sequence patters (`case List(els: _*) =>`)
- tuple patterns (`case (x, y) =>`)
- constructor patterns (`case (x, y) => `)
- type test patterns (`case p: Person =>`)

# `try`/`catch`/`finally`
- catch & manage exceptions
- **uses the same syntax that `match` expressions use**
	- supports pattern matching on the different possible expections that can occur
```scala
var text = ""
try
  text = openAndReadAFile(filename)
catch
  case fnf: FileNotFoundException => fnf.printStackTrace()
  case ioe: IOException => ioe.printStackTrace()
finally
  // close your resources here
  println("Came to the 'finally' clause.")
```
