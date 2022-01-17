[[spark]] [[big data]] [[Data Engineering]]
# What is big data
==a term used to refer to the study and apllications of data sets that are too complex for traditional data-processing software==

## 3 V's of big data
#### Volume
- size of the data
#### Variety
- different sources & formats
#### Velocity
- speed at which the data is generated & available for processing

Big data concepts
#### clustered computing
- collection of resources of multiple machines
#### parallel computing
- simultaneous computation
#### Distributed computing
- collection of nodes (networked computers) that run in parallel
#### batch processing
- breaking the job into small pieces and running them on individual machines
#### real-time processing
-  immediate data processing (as they are created)

# big data processing systems
#### Hadoop/MapReduce
- scalable and fault-tolerant framework written in java
    - open source
    - batch processing
## Apache Spark
- general purpose & lightning fast cluster computing system
    - open source
    - both batch & real time data processing
- distributes data & computation into computing cluster
- efficient in-memory computations for large data sets
    ##### better performance: 100x speed on-memory, 10x faster on disk
- ML, real-time stream processing
![[Pasted image 20211219232903.png]]
- Spark SQL: python, java, SQL
- spark streaming: scalable, high-throughput processing library

### 2 modes
##### Local mode
- single machine such as your laptop
    - convenient for testing, debugging, and demonstration
##### Cluster mode
- set of pre-defined machines
- used for production
during the transition from local testing --> cluster deployment, *no code changes necessary*

# Pyspark
- Spark is written in Scala
- Apache released PySpark to support python with spark
- similar computation speed & power as Scala
- PySpark APIs are similar to pandas & scikit-learn
#### Spark shell
- interactive environment for running Spark jobs
- helpful for fast, interactive prototyping 
- Spark's shell allows interacting with data on disk or in memory
- python, scala, or R

### Pyspark shell
- interface with Spark data structures
- supports connecting to a cluster

### SparkContext
- entry point into spark
- default variable `sc`
**Attributes:**
    - *version* `3.2.10`
    - *python version*
    - *master*: URL of the cluster (or "local" string) 

## Loading data in Pyspark
- SparkContext's `parallelize()` method
`rdd = sc.parallelize([1,2,3,4,5])`
- SparkContext's `textFile()` method
`rdd2 = sc.textFile("test.csv")`

# Functional programming
- lambda functions are anonymous functions in Python
- Lambda functions create functions to be called later, similar to `def`
- returns the functions without any name (i.e. anonymous)
- has no return statement
- can put it anywhere in our code
- efficient when used with `map()` and `filter()`
#### map()
- ==takes a function and a list & returns a new list which contains items returned by that function, for each item==
- `map(function, list)`
- `squared_list_lambda = list(map(lambda x: x ** 2, my_list))`
#### filter()
- ==takes a function & a list and returns a new list for which the function evaluates as true==
`fliter(function, list)`
`filtered_list = list(filter(lambda x: (x%10 == 0), my_list2))`

# RDDs - Resilient Distributed Datasets
- collection of data distributed across the cluster
- fundamental, backbone data type in pyspark
- spark divides the data into partitions & distributes slices across the nodes
### Resilient
- able to withstand failures
- recomputes damaged or missing partitions
### Distributed
- spanning across multiple machines
### Datasets
- collection of partitioned data, e.g. arrays, tables, tuples, etc
![[Pasted image 20211220194315.png]]
##### Create RDDs by parallelizing an existing collection of objects
- external datasets:
    - files in HDFS
    - objects in AWS S3 bucket
    - lines in a text file (in memory processing?)
- from existing RDDs
### parallelized collection (parallelizing)
- Create from python list:
- `numRDD = nc.parallelize([0,1,2])`
- create from external datasets
`fileRDD = sc.textFile("README".md)`

## Partitioning in PySpark
- ==logical division of a large distributed data set==
- by default, **spark creates the partitions **
    - based on:
        - available resources
        - external datasets
        - [ ] etc.
- can be changed with 
`sc.parallelize(numRDD, minPartitions=6)`
- `getNumPartitions()` method retrieves current # of partitions
print("Number of partitions in fileRDD is", fileRDD.getNumPartitions())
```
# Create a fileRDD_part from file_path with 5 partitions
fileRDD_part = sc.textFile(file_path, minPartitions = 5)

# Check the number of partitions in fileRDD_part
print("Number of partitions in fileRDD_part is", fileRDD_part.getNumPartitions())
```
- unlikely to get better performance by tuning in databricks


## PySpark operations = transformations + actions
- transformations create new RDDs
- actions perform computation on the RDDs

### Transformations

#### RDD transformations are *lazily evaluated*
- spark creates a graph from all the transformations you perform on the RDD
![[Pasted image 20211220204805.png]]
- lazy evaluation

##### `map()` transformation
==applies a function to all elements in the RDD==
![[Pasted image 20211220210838.png]]
`RDD = sc.parallelize([1,2,3,4])`

##### `filter()` transformation
- Filter transformations returns a new RDD with only the elements that pass the condition
![[Pasted image 20211220220947.png]]
`RDD = sc.parallelize([1,2,3,4])`
`RDD_filter = RDD.filter(lambda x: x > 2)`

##### `flatMap()` transformation
==returns mulitple values for each element in the original RDD==
![[Pasted image 20211220221226.png]]
`RDD = sc.parallelize(["hello world", "how are you"])`
`RDD_flatmap = RDD.flatMap(lambda x: x.split(" "))`

#### `union()` transformation
![[Pasted image 20211220221755.png]]
```
inputRDD = sc.textFile("logs.txt")
errorRDD = inputRDD.filter(lambda x: "error" in x.split())
warningsRDD = inputRDD.filter(lambda x: "warnings" in x.split())
combinedRDD = errorRDD.union(warningsRDD)
``` 

### Actions
- operation return a value after running a computation on the RDD
- basic RDD actions:
    - **`collect`**
    - ==returns all the elements of the dataset as an array==
    -  **`take(n)`**
    -  ==returns an array with the first N elements of the dataset==
    -  **`first()`**
    -  ==prints the first element of the RDD==
    -  **`RDD_map.collect()`**
    -  ==return the number of elements in the RDD== 
    -  **`count()`**
    -  ==returns the number of elements in the RDD==

```
# Create map() transformation to cube numbers
cubedRDD = numbRDD.map(lambda x: x ** 3)

# Collect the results
numbers_all = cubedRDD.collect()

# Print the numbers from numbers_all
for num in numbers_all:
	print(num)
```
- `collect()` shouldn't be used on large datasets, only small datasets

```
# Filter the fileRDD to select lines with Spark keyword
fileRDD_filter = fileRDD.filter(lambda line: 'Spark' in line)

# How many lines are there in fileRDD?
print("The total number of lines with the keyword Spark is", fileRDD_filter.count())

# Print the first four lines of fileRDD
for line in fileRDD_filter.take(4): 
  print(line)
```
## Pair RDDs in Pyspark
- real life datasets are usually key/value pairs
- each row is a key that maps to one or more values
- pair RDD: key is the identifier and value is data
- create pair RDDs:
    - list of key-value tuples
    - from a regular RDD
- get the data into key/value form for paired RDD
```
my_tuple = [('Sam', 23), ('Mary', 34), ('Peter', 25)]
pairRDD_tuple = sc.parallelize(my_tuple)

my_list = ['Sam 23', 'Mary 34', 'Peter 25']
regularRDD = sc.parallelize(my_list)
pairRDD_RDD = regularRDD.map(lambda s: (s.split(' ')[0], s.split(' ')[1]))
```
- all regular transformations on pair RDD
- have to pass functions that operate on key value pairs rather than on individual elements
- examples of paired RDD transformations
    - `reduceByKey(func)`: combines values with the same key
        - runs parallel operations for each key in the dataset
        - **merges the values for each key using an associative reduce function**
```
Rdd = sc.parallelize([(1,2),(3,4),(3,6),(4,5)])

# Apply reduceByKey() operation on Rdd
Rdd_Reduced = Rdd.reduceByKey(lambda x, y: x + y)

# Iterate over the result and print the output
for num in Rdd_Reduced.collect(): 
  print("Key {} has {} Counts".format(num[0], num[1]))
---------------

```

- **`groupByKey()`**: group values with the same key
    - it runs parallel operations for each key in the dataset
    - it is a transformation and not action
```
airports = [("US, "JFK"),("UK", "LHR"),("FR", "CDG"),("US", "SFO")]
regularRDD = sc.parallelize(airports)
pairRDD_group = regularRDD.grouByKey().collect()
for cont, air in pairRDD_group:
    print(cont, list(air))
------  
FR ['CDG']
US ['JFK', 'SFO']
UK ['LHR']
```
- **`sortByKey()`**: Return an RDD sorted by the key
    - returns an RDD sorted by key in ascending or descending order
```
pairRDD_reducebykey_rev = pairRDD_reducebykey.map(lambda x: (x[1], x[0]))
pairRDD_reducebykey_rev.sortByK.ey(ascending=False).collect()
```
- join(): join two pair RDDs based on their key
```
regularRDD = sc.parallelize([("Messi", 23), ("Ronaldo", 34),
                              ("Neymar", 22), ("Messi", 23)])
regularRDD2 = sc.parallelize([("Messi", 23), ("Ronaldo", 34)])

reglarRDD.join(regularRDD2).collect()
```

# More actions
**`reduce()`**
    - ==used for aggregating the elements of a regular RDD==
    - the function should be commutative (changing the order of the operands does not change the result) and associative so that it can be computed correctly in parallel
        - e.g. addition & simple multiplication/division
- in many cases it is not smart to run `collect()` action because the data is huge
- `saveAsTextFile()`
    - saves RDD into a text file inside a directory with each partition as a separate file
- `coalesce()` method can be used to save RDD as a single text file
`RDD.coalesce(1).saveAsTextFile("tempFile")`

- action available on pair RDDs
- pair RDD actions leverage the key-value data
- few examples of pair RDD actions include:
    - `countByKey()`
        - only available for key-value data
        - counts the number of elements for each key
        - returns a dictionary
        - should only be used for datasets whose size is small enough to fit in memory
```
# Count the unique keys
total = Rdd.countByKey()

# What is the type of total?
print("The type of total is", type(total))

# Iterate over the total and print the output
for k, v in total.items(): 
  print("key", k, "has", v, "counts")
```
- `collectAsMap()`
    - returns the key-value pairs in the RDD as a dictionary
```
# Create a baseRDD from the file path
baseRDD = sc.textFile(file_path)

# Split the lines of baseRDD into words
splitRDD = baseRDD.flatMap(lambda x: x.split())

# Count the total number of words
print("Total number of words in splitRDD:", splitRDD.count())

# Convert the words in lower case and remove stop words from the stop_words curated list
splitRDD_no_stop = splitRDD.filter(lambda x: x.lower() not in stop_words)

# Create a tuple of the word and 1 
splitRDD_no_stop_words = splitRDD_no_stop.map(lambda w: (w, 1))

# Count of the number of occurences of each word
resultRDD = splitRDD_no_stop_words.reduceByKey(lambda x, y: x + y)

# Display the first 10 words and their frequencies from the input RDD
for word in resultRDD.take(10):
	print(word)

# Swap the keys and values from the input RDD
resultRDD_swap = resultRDD.map(lambda x: (x[1], x[0]))

# Sort the keys in descending order
resultRDD_swap_sort = resultRDD_swap.sortByKey(ascending=False)

# Show the top 10 most frequent words and their frequencies from the sorted RDD
for word in resultRDD_swap_sort.take(10):
	print("{},{}". format(word[1], word[0]))
----------------------------------------------
('Quince', 1)
('Corin,', 2)
('circle', 10)
('enrooted', 1)
('divers', 20)
('Doubtless', 2)
('undistinguishable,', 1)
('widowhood,', 1)
('incorporate.', 1)
('rare,', 10)
thou,4247
thy,3630
shall,3018
good,2046
would,1974
Enter,1926
thee,1780
I'll,1737
hath,1614
like,1452
```


## PySpark MLflow
##### Machine Learning library
**machine learning**: ==scientific discipline that explores the construction & study of algorithms that can learn from data==

- **ML algorithms**: collaborative filtering, classification, and clustering
- **Featurization:** feature extraction, transformation, dimensionality reduction, and selection
- **pipelines:** tools for constructing, evaluating, and tuning ML pipelines

### Why PySpark MLib?
- vs. scikit-learn:a popular Python library for data mining & machine learning
- scikit-learn algorithms only work for small datasets on a single machine
- Spark's MLib algorithms are designed for parallel processing on a cluster
- Supports languages such as Scala, Java and R
- Provides a high-level API to build machine learning pipelines

### Algorithms:
- **classification (binary & multiclass) and regression:**
    - Linear SVMs
    - logistic regression,
    - decision trees
    - random forests,
    - gradient-boosted trees
    - naive Bayes
    - Linear least squares
    - Lasso
    - ridge regression
    - isotonic regression
- **Collaborative filtering**
    - alternating least squares (ALS)
- **Clustering**:
    - K-means, Gaussian mixture, Bisecting K-means and Streaming K-Means
    - 


#### Spark is *synonymous* with big data processing
#### ML