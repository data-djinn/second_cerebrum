[[Functional Programming]]

# High-level features
- it's a high-level programming language
	- abstracts away pointers & memory management
	- write high-level with the use of lambdas & high-order functions
```
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
	- 