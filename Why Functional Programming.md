[[Functional Programming]]
https://spectrum.ieee.org/functional-programming
- must companies struggle with code fragility/complexity
	- every new feature that gets added to the code increases its complexity, which then increases the chance that somethnig will break
	- software often grows so complex that the developers avoid changing it more than is absolutely necessary, for fear of breaking something
	- many companies employ whole teams of developers to just keep existing systems going
- trajectory of the current software industry is towards increasing complexity, longer product-development times, and a greater fragility of production systems
- rather than just throwing more people at the problem, the answer is ==functional programming==


#### Problem: global state
- in hardware, you can't have a resistor shared by e.g. both the keyboard & the monitor's circuitry
- programmers do this all the time, however! 
	- *Shared global state*: variables are owned by no one process, but can be changed by any number of processes, even simultaneously
- Now imagine that every time you ran your microwave, your dishwasher's settings changed from "normal cycle" -> "pots & pans"... That's shared global state
- many functions have side effects that change the shared global state, giving rise to unexpected consequenced

#### Problem: null references
- a reference to a place in memory points to nothing at all
- if you try to use this reference, an error ensues
- so, programmers have to remember to check whether something is null before trying to read or change what it references
- nearly every popular language today has this flaw
- functional programming **disallows null references**
	- instead, there is a construct called `Maybe` [[What is a monad?]]
		- can be `None` or some value
	- working with a `Maybe` forces devs to always consider both cases


- Functional programming also requires that data be *immutable*
	- once you set a variable to some value, it is *forever* that value
- functional programming does not have statements - **only expressions** like those found in math
	- `x = x + 1` is a standard statement in many programming languages
	- in math, that expression does not have a solution
	- so, elementary mathmatical logic can now be employed when writing code & reasoned about when reading it
	- use algebraic substitution to help reduce code complexity in the same way you reduced complexity of equations in algebra class!
- 