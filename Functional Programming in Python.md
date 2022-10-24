[[Functional Programming]] [[Python]]

## Use immutable data structures
- use `collections.namedtuple` or `@dataclass(frozen=True)` for objects 
- use `tuple` for iterable/collections

# FP Primitives
## `filter()`
- allows you to apply a filter **function** over an iterable
- returns a smaller iterable
	- every iterable item is passed to the filter function (typically lambda, but could be defined)
	- returns `True`: included, or `False`: excluded from result
- return an iterator yielding those items for which function(item) is True. If function is None, return the items that are true"
- use a `lambda` func
	- `filtered_tuple = filter(lambda tup: tup.a_property is True, my_tuples)`
		- returns a filter object, which is an iterable (`next(filtered_tuple)`)
		- `tuple(filtered_tuple)` will return all filtered items as a tuple
		- equivalent to `(s for s in scientist if x.nobel is True)`
			- returns a generator object
- *WHY?* do it this way?
	- very declarative & composable
	- functional programming, when done well, creates building blocks that can be reused in different contexts
	- self-contained
	- lack of side-effects facilitates multiprocessing/parallelization

## `map()`
- make a `map object` iterator that computes the givens function using arguments from each of the iterables
- stops when the shortest iterable is exhausted
- used to transform arrays into different arrays:
`mapped_tuple = tuple(map(lambda tup: (tup.a_property, tup.int_property - 100), my_named_tuple))`
- this approach (as well as more pythonic comprehensions) allows you to create new, derived iterables without destroying original input, and without side-effects
- again, making parallel computation much easier

## `reduce()`
- need to `from functools import reduce`
- apply a function of two argiments *cumulatively* to the items of an iterable from left to right, so as to **reduce the iterable to a single value**
	- e.g. `reduce((lambda x, y: x+y),  [1, 2, 3, 4]`--> `(((1+2)+3)+4)`
	- this is equivalent to `sum(x for x in [1, 2, 3, 4]))`
	- optional `initial` argument to begin with & use as a default value when a value is missing in one of the iterable
- comparable to a cumulative window function in sql, in that it accumulates the results of all f(x) outputs
#### `itertools.groupby(iterable[, keyfunc])`
- creates an iterator which returns (key, sub-iterator) grouped by each value of key(value)

# Parallel processing with `multiprocessing`
- 