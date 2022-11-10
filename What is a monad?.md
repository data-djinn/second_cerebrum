[[Functional Programming]] [[Haskell]]

- extremely powerful design pattern
- ==allow a user to chain operations while the monad manages secret work behind the scenes==
- can be used with *0* math knowledge!
basic example:
```typescript
function square(x: number): number {
	return x * x
}

function addOne(x: number): number {
	return x + 1
}

addOne(square(2)) => 5
```

- what if we want to store a log of all the processing done on the inputs? e.g.:
```typescript
addOne(square(2)) => {
	result: 5,
	logs: [
		"Squared 2 to get 4",
		"Added 1 to 4 to get 5"
	]
}
```
#### Code with Logging:
```typescript
interface NumberWithLogs {
	result: number
	logs: string[]
}

function square(x: number): NumberWithLogs {
	return {
		result: x * x,
		logs: [ `Squared ${x} to get ${x * x}.` ]
	}
}

function addOne(x: NumberWithLogs): NumberWithLogs {
 return {
	 result: x.result + 1,
	 logs: x.logs.concat([
		 `Added 1 to ${x.result} to get ${x.result + 1}.`
		 ])
     }
}
```
- **issues**:
	- `square(square(2))` doesn't work because square expects a `number`, not `NumberWithLogs`
	- `addOne(5)` doesn't work because `addOne` expects `NumberWithLogs`, not `number`
- **solution**: Monad!
```typescript
function wrapWithLogs(x: number): NumberWithLogs {
	return {
		result: x
		logs: []
	}
```
- always concatenating the logs array is verbose - how to abstract away?
```typescript
function runWithLogs(
	input: NumberWithLogs,
	transform: (_: number) => NumberWithLogs
	}: NumberWithLogs {
	const newNumberWithLogs = tronsform(input.result)
	return {
		result: newNumberWithLogs.result,
		logs. input.logs.concat(newNumberWithLogs.logs)
	}
}
```
now you can use: `runWithLogs(wrapWithLogs(5), addOne)`
new function definitions:
```typescript
interface NumberWithLogs {
	result: number
	logs: string[]
}

function square(x: number): NumberWithLogs {
	return {
		result: x * x,
		logs: [ `Squared ${x} to get ${x * x}.` ]
	}
}

function addOne(x: number): NumberWithLogs {
 return {
	 result: x + 1,
	 logs: [ `Added 1 to ${x} to get ${x + 1}.`]
     }
}
```
all together, this is a Monad pattern!
- with this setup, we can chain arbitrary function calls in any order
	- all while managing busywork/complex things behind the scenes
- we can also add new transformations to run with
- log concatination is completely hidden away in `runWithLogs`
```typescript
const a = wrapWithLogs(5)
const b = runWithLogs(a, addOne)
const c = runWithLogs(b, square)
const d = runWithLogs(d, multiplfByThree)
```

### Monads have 3 components
- Wrapper type (`NumberWithLogs`)
- Wrap function (`WrapWithLogs`)
	- allows entry into monad ecosystem
	- also known as `return`, `pure`, `unit`
- Run function (`runWithLogs`)
	- runs transformations on monadic values
	- takes wrapper type & a transform function that accepts unwrapped type, returning the wrapper type
	- aka `bind`, `flatMap`, `>>=`
### another popular monad is an `Option` or `Maybe` type
- a number has to be a `number`
- an `Option<number>` is a number OR nothing
1. Wrapper Type
	- `Option<T>`: `Option<number>`, `Option<string>`, ect (`T` indicates generic)
2. **Wrap Function**
	- turns `T`s into `Option<T>`s
3. **Run function**
	- runs transformatios


#### Without `Option`:
```typescript
function getPetNickname(): string | undefined {
	const user: User | undefined = getCurrentUser()
	if (user === undefined) {
		return undefined
	}
	
	const userPet: Pet | undefined = getPet(user)
	if (userPet === undefined) {
		return undefined
	}
	
	const userPetNickname: string | undefined = getNickname(userPet)
	
	return userPetNickname
}
```

#### With `Option`
```typescript
function getPetNickname(): Option<string> {
	const user: Option<User> = getCurrentUser()
	const userPet: Option<Pet> = run(user, getPet)
	const userPetNickname = Option<string> = run(userPet, getNickname)
	return userPetNickname
}
```

![[Pasted image 20221110124609.png]] 

- "programmable statements": write seemingly normal statements in code, while having extra logic embedded in every step

![[Pasted image 20221110124729.png]]

### Other common monads
- NumberWtihLogs / Writer = accumulation of log data
- Option = possibility of missing values
- Future/Promise = possibility for values to only become available later
- list = abstracts away branching computation
```typescript
const doors = [ 'red', 'green', 'blue' ];
const doorsAndCoinPossibilities = run(
	doors,
	door => {
		return [
			door + ' heads',
			door + ' tails'
		]
	}
)
==========
[
	'red heads',
	'red tails',
	'green heads',
	'green tails',
	'blue heads',
	'blue tails'
]
```
`flatMap`, essentially