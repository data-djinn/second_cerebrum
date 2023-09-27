[[dart]] [[flutter]]

# Syntax basics
## Variables
```dart
var name = 'Bob';
```
- stores a reference to a `String` object 
- type is inferred if not declared
	- `Object` & `dynamic` type are not restricted to any specific type

### Null Safety
- **prevents unintentionally accessing the value of `null` vars** (null dereference errors)
```dart
String? name // defaults to null
```
- non-nullable vars don't have a default value & the initial value must always be set
- can't access properties or call methods on an expression with a nullable type
### `late` variables
- declare a non-nullable variable that's initialized after its declaration
- lazily initializing a variable
```dart
late String description

void main() {
	description = 'Feijoada!'
	print(description);
}
```
- if you mark a variable as `late` but initialize it at its declaration, then the initializer runs the first time the variable is used
	- variable may not be needed, and initializing it is costly
	- you're initializing an instance variable, and its initializer needs access to `this`
```dart
late String temperature = readThermometer(); // lazily initialized
```

### `final` & `const`
- use when you never intend to change a variable
- `final` vars can only be set once
- `const` is a `final` var that is known at compile time (e.g. the speed of light, etc)
```dart
final name = exampleClass.getName();

const bar = 100000;
const double atmopheric_pressure = 1.01325 * bar;
const foo = []; // immutable
var bar = const [];  // mutable
bar = [1, 2, 3];
foo = [1]; // ERROR!
```
- if the const variable is at the class lever, mark it a `static const`

## Operators
| Description                | Operator                                                      | Associates |
| -------------------------- | ------------------------------------------------------------- | ---------- |
| unary postfix              | `expr++`, `expr--`, `()`, `[]`, `?[]`, `.`, `?.`, `!`         | None       |
| unary prefix               | `-expr`, `!expr`, `~expr`, `++expr`, `--expr`, `await expr`   | None       |
| multiplicative             | `*`, `/`, `%`, `~/` (divide & return integer)                 | left       |
| additive                   | `+`, `-`                                                      | left       |
| shift                      | `<<`, `>>`, `>>>`                                             | left       |
| bitwise `AND`              | `&`                                                           | left       |
| bitwise `XOR`              | `^`                                                           | left       |
| bitwise `OR`               | `|`                                                           | left       |
| relational & type tests    | `>=`, `>`, `<=`, `<`, `as`, `is`, `is!`                       | None       |
| equality                   | `==`, `!=`                                                    | None       |
| logical `AND`              | `&&`                                                          | left       |
| logical `OR`               | `||`                                                          | left       |
| if null                    | `expr1 ?? expr2` (evaluates & returns expr2 if expr1 is null) | left       |
| conditional                | `expr1 ? exprIfTrue : exprIfFalse`                            | right      |
| cascade                    | `..`, `?..`                                                   | left       |
| \[operation &\] assignment | `=`, `*=`, `/=`, `+=`, `-=`, `&=`, `^=`, etc                  | right      |

example:
```dart
if ((n % i == 0) && (d % i == 0)) ...
```

```dart
int a;
int b;

a = 0;
b = ++a; // Increment a before b gets its value.
assert(a == b); // 1 == 1

a = 0;
b = a++; // Increment a after b gets its value.
assert(a != b); // 1 != 0

a = 0;
b = --a; // Decrement a before b gets its value.
assert(a == b); // -1 == -1

a = 0;
b = a--; // Decrement a after b gets its value.
assert(a != b); // -1 != 0
```

##### Cascade notation
`..`, `?..` let you make a sequence of operations on the same object
```dart
var paint = Paint()
  ..color = Colors.black
  ..strokeCap = StrokeCap.round
  ..strokeWidth = 5.0;

// equivalent to:
var paint = Paint();
paint.color = Colors.black;
paint.strokeCap = StrokeCap.round;
paint.strokeWidth = 5.0;
```
if the object the cascade operates on can be null, then use a "null-shortening" cascade (`?..`)