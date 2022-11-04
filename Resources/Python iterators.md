[[Python]]
# Iterating with a for loop
- Lists
- strings
- ranges
- dictionaries
- file connections 
are all:
## iterables
==object that has an associated `iter()` method==
-   applying `iter()` to an object creates an iterator
vs:
## iterator
- produces next value with `next()`
- helpful to conserve memory - *use this as often as possible!*
- e.g. 10 ** 100 would normally cause stack overflow:
```python
# Create an iterator for range(10 ** 100): googol
googol = iter(range(10 ** 100))

# Print the first 5 values from googol
print(next(googol))
print(next(googol))
print(next(googol))
print(next(googol))
print(next(googol))
```

# enumerate()
==takes any iterable as argument & returns pairs of elements of original iterable & the index in the iterable==
- use `start` argument to start index at an int > 0

# zip()
==iterator of tuples==
- turn zip into list & print it
```python
# Create a list of tuples: mutant_data
mutant_data = list(zip(mutants, aliases, powers))

# Print the list of tuples
print(mutant_data)

# Create a zip object using the three lists: mutant_zip
mutant_zip = zip(mutants, aliases, powers)

# Print the zip object
print(mutant_zip)

# Unpack the zip object and print the tuple values
for value1, value2, value3 in mutant_zip:
    print(value1, value2, value3)
```
- **unzip with "splat" operator** `*`
- `*` unpacks an iterable into *positional arguments* in a function call
- using splat operator goes through the iterator object & so *can only be used once* before the iterable needs to be re-created as new object (reset iter() to index 0)
```python
# Create a zip object from mutants and powers: z1
z1 = zip(mutants, powers)

# Print the tuples in z1 by unpacking with *
print(*z1)

# Re-create a zip object from mutants and powers: z1
z1 = zip(mutants,powers)

# 'Unzip' the tuples in z1 by unpacking with * and zip(): result1, result2
result1, result2 = zip(*z1)

# Check if unpacked tuples are equivalent to original tuples
print(result1 == mutants)
print(result2 == powers)
------------------------
('charles xavier', 'telepathy') ('bobby drake', 'thermokinesis') ('kurt wagner',    'teleportation') ('max eisenhardt', 'magnetokinesis') ('kitty pryde', 'intangibility')
True
True
```

## Large amount of data, can't hold in memory
- load data in chunks using pandas.read_csv('file', chunksize=10)
```python
# Define count_entries()
def count_entries(csv_file, c_size, colname):
    """Return a dictionary with counts of
    occurrences as value for each key."""
    
    # Initialize an empty dictionary: counts_dict
    counts_dict = {}

    # Iterate over the file chunk by chunk
    for chunk in pd.read_csv(csv_file, chunksize=c_size):

        # Iterate over the column in DataFrame
        for entry in chunk[colname]:
            if entry in counts_dict.keys():
                counts_dict[entry] += 1
            else:
                counts_dict[entry] = 1

    # Return counts_dict
    return counts_dict

# Call count_entries(): result_counts
result_counts = count_entries(csv_file='tweets.csv', c_size=10, colname='lang')

# Print result_counts
print(result_counts)
```