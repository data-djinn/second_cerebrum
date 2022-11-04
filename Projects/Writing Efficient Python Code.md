[[Python]] 
- how to write clean, fast, efficient Python code
- how to profile your code for bottlenecks
- how to eliminate bottlenecks and bad design patterns

**Efficent code**: ==
- ==minimal completion time (fast runtime)==
- ==minimal resource consumption (small memory footprint)
- Python focuses on readability
- use Python's constructs as intended - ***Pythonic***
`import this`
![[Pasted image 20220112220721.png]]
# Examining runtime
- allow us to pick the **optimal** coding approach
- faster code == more efficient code!
## Time our code
- use `%timeit` magic command (enhancements on top of normal python syntax)
  - see all available magic commands with `%lsmagic`
`%timeit rand_nums = np.random.rand(1000)`
- provides an *average* of timing statistics:
  - mean +- standard deviation for number of runs

##### Specify number of loops (`-n`) & runs (`-r`)
`%timeit -r5 -n25`
##### save output to a variable with `var -o`
- allows us to see:
  - `var.timings`: time for each run
  - `var.best`: best time for all runs
  - `var.worst`: worst time for all runs
![[Pasted image 20220310185636.png]]

## Code profiling
- detailed stats on frequency and duration of function calls
- line-by-line analyses
- package used: `line_profiler`
`%load_ext line_profiler`
`%lprun -f my_func my_func(param, param2, ...)`
- lprun *does not do multiple loops*, so may be less accurate
# Efficiently Combining, counting
## zip()
- combines 2 objects into 1 tuple, like a zipper
- returns `<class 'zip'>` object
    - must be unpacked into a list & printed
```python
names = ['Bulbassaur', 'Charmander', 'Squirtle']
hps = [45, 39, 44]
zipped = zip(names,hps)

zipped_list = [#zipped]
--------------
[('Bulbasaur', 45), ('Charmander', 39), ('Squirtle', 44)]
```
### use splat operator when possible
```python
# For loop (bad!)
indexed_names = []
for i,name in enumerate(names):
    index_name = (i,name)
    indexed_names.append(index_name) 
print(indexed_names)

# Rewrite the above for loop using list comprehension (better!)
indexed_names_comp = [(i,name) for i,name in enumerate(names)]
print(indexed_names_comp)

# Unpack an enumerate object with a starting index of one (best)
indexed_names_unpack = [*enumerate(names, 1)] #others start at 0
print(indexed_names_unpack)
```

### map(func, iterable)
```python
# Use map to apply str.upper to each element in names
names_map  = map(str.upper, names)

# Print the type of the names_map
print(type(names_map))

# Unpack names_map into a list
names_uppercase = [*names_map]

# Print the list created above
print(names_uppercase)
```

### Map vs List Comprehension
- list comprehension is more concise and easier to read as compared to map
- list comprehension allows filtering. In map, we have no such facility. For example, to print all even numbers in range of 100, we can write `[n for n in range(100) if n% 2 == 0]`. There is no alternate for it in map
- list comprehension are used when a list of results is required as map only returns a map object and does not return any list
- list comprehension is faster than map when we need to evaluate expressions that are too long or complicated to express
- map is faster in case of calling an already defined function (as no lambda is required)

# Numpy arrays
- alternative to Python lists
### advantages
- **Homogeneous: single data type** 
  - verify with `array.dtype`
  - eliminates overhead needed for type checking
- **Efficiently apply operations element-wise through broadcasting** 
```python
# Create a list of arrival times
arrival_times = [*range(10,60,10)]

# Convert arrival_times to an array and update the times
arrival_times_np = np.array(arrival_times)
new_times = arrival_times_np - 3

# Use list comprehension and enumerate to pair guests to new times
guest_arrivals = [(names[i],time) for i,time in enumerate(new_times)]

# Map the welcome_guest function to each (guest,time) pair
welcome_map = map(welcome_guest, guest_arrivals)

guest_welcomes = [*welcome_map]
print(*guest_welcomes, sep='\n')
---------------------------------
[('Jerry', 7), ('Kramer', 17), ('Elaine', 27), ('George', 37), ('Newman', 47)]

Welcome to Festivus Jerry... You're 7 min late. Welcome to Festivus Kramer... You're 17 min late. Welcome to Festivus Elaine... You're 27 min late. Welcome to Festivus George... You're 37 min late. Welcome to Festivus Newman... You're 47 min late.
```
# Python collections = perf improvements
*All part of python's standard library (built-in)*
### namedtuple: tuple subclass with named fields
## Counter: dict for counting hashable objects
```python
poke_types = ['Grass', 'Dark', 'Fire', 'Fire', ...]
type_counts = {}
for poke_types in poke_types:
    if poke_type not in type_counts:
        type_counts[poke_type] = 1
    else:
        type_counts[poke_type] += 1
        
# PREFERABLY, YOU USE Counter:
from collections import Counter
type_counts = Counter(poke_types)

# SAME OUTPUT
print(type_counts)
-----------------
{'Rock': 41, 'Dragon': 25, 'Ghost': 20,...}
```
### OrderedDict: dict that retains order of entries
### defaultdict: dict that calls a factory function to supply missing values
## Itertools
#### Functional iterators: `count(), cycle(), repeat()`
#### finite iterators: `accumulate(), chain(), zip_longest()`
#### combination generators: `product(), permutations, combinations`
```python
poke_types = ['Grass', 'Dark', 'Fire', 'Fire', ...]
counts = []

for x in poke_types:
    for y in poke_types:
        if x == y:
            continue
        if ((x,y) not in combos) & ((y,x) not in combos):
            combos.appent((x,y)) # note how we are only looking for new combinations, and skping x,x & y,y, and keeping only either x,y or y,x (not

# PREFERABLY, YOU WOULD USE combinations:
from itertools import combinations
combos_obj = combinations(poke_types, 2)

# SAME OUTPUT
combos = [*combos_obj]
print(combos)
-------------
[('Bug', 'Fire'), ('Bug', 'Ghost'), ('Bug', 'Grass'), ...

# Create a combination object with pairs of Pokémon
combos_obj = combinations(pokemon, 2)
print(type(combos_obj), '\n')

# Convert combos_obj to a list by unpacking
combos_2 = [*combos_obj]
print(combos_2, '\n')

# Collect all possible combinations of 4 Pokémon directly into a list
combos_4 = [*combinations(pokemon, 4)]

```
## Deques
 [`deque`](https://docs.python.org/3/library/collections.html#collections.deque "collections.deque") objects[](https://docs.python.org/3/library/collections.html#deque-objects "Permalink to this headline")

_class_ `collections.``deque`([_iterable_[, _maxlen_]])[](https://docs.python.org/3/library/collections.html#collections.deque "Permalink to this definition")

Returns a new deque object initialized left-to-right (using [`append()`](https://docs.python.org/3/library/collections.html#collections.deque.append "collections.deque.append")) with data from _iterable_. If _iterable_ is not specified, the new deque is empty.

Deques are a generalization of stacks and queues (the name is pronounced “deck” and is short for “double-ended queue”). Deques support thread-safe, memory efficient appends and pops from either side of the deque with approximately the same O(1) performance in either direction.

Though [`list`](https://docs.python.org/3/library/stdtypes.html#list "list") objects support similar operations, they are optimized for fast fixed-length operations and incur O(n) memory movement costs for `pop(0)` and `insert(0, v)` operations which change both the size and position of the underlying data representation.

If _maxlen_ is not specified or is `None`, deques may grow to an arbitrary length. Otherwise, the deque is bounded to the specified maximum length. Once a bounded length deque is full, when new items are added, a corresponding number of items are discarded from the opposite end. Bounded length deques provide functionality similar to the `tail` filter in Unix. They are also useful for tracking transactions and other pools of data where only the most recent activity is of interest.

### Deque methods
`append`(_x_)[](https://docs.python.org/3/library/collections.html#collections.deque.append "Permalink to this definition")
Add _x_ to the right side of the deque.

`appendleft`(_x_)[](https://docs.python.org/3/library/collections.html#collections.deque.appendleft "Permalink to this definition")
Add _x_ to the left side of the deque.

`clear`()[](https://docs.python.org/3/library/collections.html#collections.deque.clear "Permalink to this definition")
Remove all elements from the deque leaving it with length 0.

`copy`()[](https://docs.python.org/3/library/collections.html#collections.deque.copy "Permalink to this definition")
Create a shallow copy of the deque.

`count`(_x_)[](https://docs.python.org/3/library/collections.html#collections.deque.count "Permalink to this definition")
Count the number of deque elements equal to _x_.

`extend`(_iterable_)[](https://docs.python.org/3/library/collections.html#collections.deque.extend "Permalink to this definition")
Extend the right side of the deque by appending elements from the iterable argument.

`extendleft`(_iterable_)[](https://docs.python.org/3/library/collections.html#collections.deque.extendleft "Permalink to this definition")
Extend the left side of the deque by appending elements from _iterable_. Note, the series of left appends results in reversing the order of elements in the iterable argument.

`index`(_x_[, _start_[, _stop_]])[](https://docs.python.org/3/library/collections.html#collections.deque.index "Permalink to this definition")

Return the position of _x_ in the deque (at or after index _start_ and before index _stop_). Returns the first match or raises [`ValueError`](https://docs.python.org/3/library/exceptions.html#ValueError "ValueError") if not found.

`insert`(_i_, _x_)[](https://docs.python.org/3/library/collections.html#collections.deque.insert "Permalink to this definition")
Insert _x_ into the deque at position _i_.
If the insertion would cause a bounded deque to grow beyond _maxlen_, an [`IndexError`](https://docs.python.org/3/library/exceptions.html#IndexError "IndexError") is raised.

`pop`()[](https://docs.python.org/3/library/collections.html#collections.deque.pop "Permalink to this definition")
Remove and return an element from the right side of the deque. If no elements are present, raises an [`IndexError`](https://docs.python.org/3/library/exceptions.html#IndexError "IndexError").

`popleft`()[](https://docs.python.org/3/library/collections.html#collections.deque.popleft "Permalink to this definition")
Remove and return an element from the left side of the deque. If no elements are present, raises an [`IndexError`](https://docs.python.org/3/library/exceptions.html#IndexError "IndexError").

`remove`(_value_)[](https://docs.python.org/3/library/collections.html#collections.deque.remove "Permalink to this definition")
Remove the first occurrence of _value_. If not found, raises a [`ValueError`](https://docs.python.org/3/library/exceptions.html#ValueError "ValueError").

`reverse`()[](https://docs.python.org/3/library/collections.html#collections.deque.reverse "Permalink to this definition")
  Reverse the elements of the deque in-place and then return `None`.

`rotate`(_n=1_)[](https://docs.python.org/3/library/collections.html#collections.deque.rotate "Permalink to this definition")
Rotate the deque _n_ steps to the right. If _n_ is negative, rotate to the left.

When the deque is not empty, rotating one step to the right is equivalent to `d.appendleft(d.pop())`, and rotating one step to the left is equivalent to `d.append(d.popleft())`.

Deque objects also provide one read-only attribute:

`maxlen`[](https://docs.python.org/3/library/collections.html#collections.deque.maxlen "Permalink to this definition")
Maximum size of a deque or `None` if unbounded.

In addition to the above, deques support iteration, pickling, `len(d)`, `reversed(d)`, `copy.copy(d)`, `copy.deepcopy(d)`, membership testing with the [`in`](https://docs.python.org/3/reference/expressions.html#in) operator, and subscript references such as `d[0]` to access the first element. Indexed access is O(1) at both ends but slows to O(n) in the middle. For fast random access, use lists instead

## Eliminating loops
- using extraneous loops is inefficient and costly
### Benefits of eliminating loops:
- fewer lines of code
- better code readability
  - "flat is better than nested"
- efficiency gains!!

### Writing better loops (when it's unavoidable)
- understand what is being done with each loop iteration
- move one-time calculations outside (above) the loop
- **use holistic conversions outside (below) the loop** (`map` function)
- anything that is done once should be done outside the loop (duh)
```python
for tupley in zip(names, status, attribute):
  data_tuples.append(tupley)
data = [*map(list, data_tuples)] # Unpack all tuples into lists of lists
```

# Set theory
- branch of mathematics applied to collections of objects (`sets`)
- python has build-in `set` datatype with accompanying methods
    - `intersection()`: all elements in both sets
    - `difference()`: all elements in one set but not the other
    - `symmetric_differcence`: all elements in exactly one set
    - `union`: all elements that are in either set
- **fast membership checking**
    - check if a value exists in a sequence or not using the `in` operator on a set (~6x faster!)
  #### get set of distinct values with `unique_set = set(my_list_w_dupes)`
```python
# Convert both lists to sets
ash_set = set(ash_pokedex)
misty_set = set(misty_pokedex)

# Find the Pokémon that exist in both sets
both = ash_set.intersection(misty_set)
print(both)

# Find the Pokémon that Ash has and Misty does not have
ash_only = ash_set.difference(misty_set)
print(ash_only)

# Find the Pokémon that are in only one set (not both)
unique_to_set = ash_set.symmetric_difference(misty_set)
print(unique_to_set)
```

```python
names_type1 = [*zip(names, primary_types)]

print(*names_type1[:5], sep='\n')
names_types = [*zip(names, primary_types, secondary_types)]


print(*names_types[:5], sep='\n')

names_types = [*zip(names, primary_types, secondary_types)]

print(*names_types[:5], sep='\n')
```

# Intro to pandas DataFrame iteration
- `df.iloc[]` is *inefficient*
#### use `.iterrows()`
```python
for row_tuple in my_df.iterrows():
   print(row_tuple[1]['col']

my_results = []
for i,row in giants_df.iterrows():
    runs_scored = row['RS']
    runs_allowed = row['RA']
    
    # Use the provided function to calculate run_diff for each row
    run_diff = calc_run_diff(runs_scored, runs_allowed)
    
    # Append each run differential to the output list
    run_diffs.append(run_diff)

giants_df['RD'] = run_diffs
```
- returns each df row as a tuple of (index, pandas Series)
```python
for row_tuple in pit_df.iterrows():
    print(row_tuple)
    print(type(row_tuple)) # (index, pd.Series)
-------------------------
    (0, Team         PIT
    League        NL
    Year        2012
    RS           651
    RA           674
    W             79
    G            162
    Playoffs       0
    Name: 0, dtype: object)
    <class 'tuple'> # ...
```

### `itertuples()`
- returns each DF row as a `namedtupe` type (from `collections` module)
  - behaves just like a python tuple, but have fields accessible using attribute lookup
  - e.g.: `Pandas(Index=0, Team='ARI', Year=2012, W=81)`
- can access with "dot" notation `my_named_tuple.my_attribute)`
  - can't use square bracket notation
- **significantly more efficient than `.iterrows`!!**
  - due to the way each method stores its output:
    - `iterrows` returns each row's values as a pandas Series

### avoid looping with pandas with `.apply()`
- acts like a `map` func
- takes a func & applies it to the DataFrame
- must specify the axis to apply(`0` for cols, `1` for rows)
- can be used to apply anonymous functions (`lambda` functions)
```python
run_diffs = baseball_df.apply(
  lambda row: calc_run_diff(row['RS'], row['RA']),
  axis=1
)
baseball_df['RD'] = run_diffs
```

### optimal pandas iterating
- eliminate loops whenever possible
- pandas is built on numpy
  - take advantage of numpy's broadcasting ability to vectorize operations
  - much more efficient than iterating over rows
- using `df['col'].values` returns a `<class 'numpy.ndarray'>`
`baseball_df['RD']  = baseball_df['RS'].values - baseball_df['RA'].values`
  - order of magnitude efficiency improvements
