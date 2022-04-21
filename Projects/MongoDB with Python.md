[[Python]] [[Data Engineering]] [[NoSQL]]
- command-line shell for MongoDB uses Javascript
# JSON
- basis of mongoDBs data format
## **can *only* contain:**
### Objects
- string keys & values
`{'key1': value1, 'key2':value2,...}`
- order of values not important
### Arrays
- series of values
 `[value1, value2,...]
- order of values *is* important

### Values
- **strings**
 `'name':'John Doe'`
- **Numbers**
 `'id': 1234`
- **`true`/`false`**
- **`null`**
- **nested array **
`'tags': ['Python', 'MongoDB']`
- **another object**
`[{'id': 12345, ...}...]`

## JSON <> Python
| JSON         | Python       |
| ------------ | ------------ |
| object       | dict         |
| array        | list         |
| strings      | str          |
| _numbers_    | int, float   |
| true / false | True / False |
| null         | None         |

![[Pasted image 20220218211303.png]]

# MongoDB
- store & explore data without a known structure
- enables you to handle diverse data together & unify analytics
- improve & fix issues on-the-fly as requirements evolve
- most APIs on the web are exposed in json format - if you can express your data in json, you can use mongoDB

| MongoDB         | JSON        | Python                        |
| --------------- | ----------- | ----------------------------- |
| Databases       | Objects     | dict                          |
| >collections    | arrays      | list                          |
| >>documents     | objects     | dict                          |
| >>>subdocuments | objects     | dict                          |
| >>>values       | value types | value types & datetime, regex |

- MongoDB also supports some types native to python but not to JSON
  - e.g. date & regex

## Example database + collections
```
import requests
free pymongo import MongoClient
# Client connects to "localhost"by default
client = MongoClient()
# Create local "nobel" database on the fly
db = client["nobel"]

for collection_name in ["prizes", "laureates"]:
  # cellect the data from the API
  response = requests.get(
    http://api.nobelprize.org/v1/{}.json".format(collection_name[:-']))
  # convert to json
  documents = response.json()[collection_name]
  # Create collections on the fly
  db[collection_name].insert_many(documents)
```
![[Pasted image 20220219172921.png]]

## Accessing databases & collections
#### 2 ways to access dbs & collections from a client:
##### use `[]` 
- interact with client as `dict` of databases, db names as `keys`
- db, in turn, is like a `dict` of collections, with collection names as `keys`
```
# client is a dictionary of databases
db = client["nobel"]

# database is a dictionary of collections
pirzes_callection = db["prizes"]
```
##### use `.`
```
# databases are attributes of a client
db = client.nobel

# collections are attributes of a database
prize_collection = db["prizes"]
```
 ## Count documents in a collection
```
# use empty document {} as a filter for total count!
filter = {}

# Count documents in a collection
n_prizes = db.prizes.count_documents(filter)
n_laureates = db.laureates.count_documents(filter)

# fecth a document and infer the schema of the raw json data given by the API
doc = db.prizes.find_one(filter)
```
![[Pasted image 20220219175314.png]]

## Find documents with `find_one()` method
- used to retrieve a single document
-  accepts an optional filter argument that specifies the pattern that the document must match
-  useful to learn the structure of documents in the collection
-  *documents returned as dictionaries in Python*
```
# Connect to the "nobel" database
db = client.nobel

# Retrieve sample prize and laureate documents
prize = db.prizes.find_one()
laureate = db.laureates.find_one()

# Print the sample prize and laureate documents
print(prize)
print(laureate)
print(type(laureate))

# Get the fields present in each type of document
prize_fields = list(prize.keys())
laureate_fields = list(laureate.keys())

print(prize_fields)
print(laureate_fields)
------------------------
{'category': 'physics', '_id': ObjectId('5bc56145f35b634065ba1996'), 'overallMotivation': '“for groundbreaking inventions in the field of laser physics”', 'laureates': [{'id': '960', 'surname': 'Ashkin', 'motivation': '"for the optical tweezers and their application to biological systems"', 'firstname': 'Arthur', 'share': '2'}, {'id': '961', 'surname': 'Mourou', 'motivation': '"for their method of generating high-intensity, ultra-short optical pulses"', 'firstname': 'Gérard', 'share': '4'}, {'id': '962', 'surname': 'Strickland', 'motivation': '"for their method of generating high-intensity, ultra-short optical pulses"', 'firstname': 'Donna', 'share': '4'}], 'year': '2018'} 

{'bornCity': 'Lennep (now Remscheid)', '_id': ObjectId('5bc56154f35b634065ba1be4'), 'diedCountryCode': 'DE', 'bornCountryCode': 'DE', 'gender': 'male', 'surname': 'Röntgen', 'bornCountry': 'Prussia (now Germany)', 'born': '1845-03-27', 'died': '1923-02-10', 'diedCountry': 'Germany', 'id': '1', 'prizes': [{'category': 'physics', 'affiliations': [{'country': 'Germany', 'name': 'Munich University', 'city': 'Munich'}], 'share': '1', 'motivation': '"in recognition of the extraordinary services he has rendered by the discovery of the remarkable rays subsequently named after him"', 'year': '1901'}], 'firstname': 'Wilhelm Conrad', 'diedCity': 'Munich'} 

<class 'dict'> 

['category', '_id', 'overallMotivation', 'laureates', 'year']\

['bornCity', '_id', 'diedCountryCode', 'bornCountryCode', 'gender', 'surname', 'bornCountry', 'born', 'died', 'diedCountry', 'id', 'prizes', 'firstname', 'diedCity']
```

# Filters as (sub)documents
- count documents by providing a *filter document* to match
  - mirrors the structure of documents to match in the collection
```
filter_doc = {
  'born': '1845-03-27',
  'diedCountry': 'Germany',
  'gender': 'male',
  'surname': 'Rontgen'
}
db.laureates.count_documents(filter_doc)
```

## Simple filters
 - `db.laureates.count_documents({'gender': 'female'})`
 - merge criteria into a single filter document by:
## Composing filters
```
filter_doc = {'gender': 'female',
              'diedCountry': 'France',
              'bornCity': 'Warsaw'}
db.leareates.count_documents(filter_doc)
```
## Query operators
- use like a filter pane on a webpage, with sliders & drop down menus
- place an operator in a filter document to wrap around a field and its acceptable values
- operators in MongoDB have a `$` prefix
  - use the `'$in':[value1,value2]` to wrap around acceptable values (inverse: `$nin`
  - use `'$ne': unwanted_value` for 'not equal'
  - use  `$gt` for >, `$gte` for >=
  - use `$lt` for <, `$lte` for <=
```
{
  'field_name1': value1,
  'field_name2': {
    $operator1: value1,
    $operator2: vaule2,
    # ... more operators
  },
  # ... more fields
}
```
- comparison operators order values in lexicographic order, meaning alphabetical order for non-numeric values
  - keep this in mind when working with raw (unprocessed) data - check data types for numeric fields!

# Dot notation: reach into substructure
- dot notation is ==how MongoDB allows us to query document substructure==
- MongoDB allows you to specify and enforce a schema for a collection, (but this is not required)
  -  fields do not need to have the same type of value across documents in a collection
- another accommodation in MongoDB is that of field presence
  -  even root-level fields don't need to be present
  -  use the `"$exists": True||False"` operator to query for the existence, or non-existence, of fields
-  reference an array element by its numerical index using dot notation
  - check for empty arrays with:  `db.collection.count_documents({"subdocument.0": {"$exists": True}})`
```
# Filter for laureates with at least three prizes
criteria = {"prizes.2": {"$exists": True}}

# Find one laureate with at least three prizes
doc = db.collection.find_one(criteria)
```

# using `collection.distinct()`
`db.collection.distinct('gender')`
- 'convenience' method for common aggregation (like `count_documents`)
- `.distinct()` aggregation is efficient **if there is a collection index** on the field
### dot notation with `.distinct()`
- specify fields deeper than the root level of a document
`db.collection.find_one({"field.2": {"$exists": True}})
##### Validate categorical data equality across collections
`assert set(db.collection1.distict("field")) == set(db.collection2.distinct("field.subfield"))`

## pre-filtering distinct values
- distinct method takes an optional filter argument
  - essentially, a two-stage pipeline: filter--> distinct
`db.collection.distinct("collection.field", {"field.1": {"$exists":True}})`
- first, filters for documents with more than one element
- then returns distinct values of field from that filtered set
`  db.collection.distinct('prizes.affiliations.country', {"bornCountry":'USA'})`
```
# Save a filter for prize documents with three or more laureates
criteria = {'laureates.2': {"$exists":True}}

# Save the set of distinct prize categories in documents satisfying the criteria
triple_play_categories = set(db.prizes.distinct('category', criteria))

# Confirm literature as the only category not satisfying the criteria.
assert set(db.prizes.distinct('category')) - triple_play_categories == {"literature"}
```

## matching array fields
`db.collection.count_documents({'field.category': 'physics'})`
- note that the documents returned may still contain a field subdocument with a category of physics - they need only contain *another* category that is not physics
`{'nicknames':['Johnny', 'JSwitch', 'JB'. 'Tc Johnny', 'Bardy']}`
`db.collection.find({'nicknames':'JB'})`
- above filter matches all documents that have at least one value in the 'nicknames' array field equal to 'JB'
`db.collection.count_documents({'field.category':{'$ne': 'physics'}})`

##### in / not in
`db.collection.count_documents({'field.category':{'$in': ['physics', 'chemistry', 'medicine']}})`
`db.collection.count_documents({'field.category':{'$nin': ['physics', 'chemistry', 'medicine']}})`

if we want to match 2 

- filter on more than one field within a subdocument --> must use:
### `$elemMatch`
`db.collection.count_documents({'prizes': {'$elemMatch': {'category': 'physics', 'share': '1'}}})`
- we can continue to drill down within `$elemMatch` operation
  - nest operations to make finer-grained queries:
```
db.collection.count_documents({
  'prizes': {
    '$elemMatch': {
      'category': 'physics'
      , 'share': '1'
      , 'year': {'$lt': '1945'},
    }
  }
})
```

```
# Save a filter for laureates with unshared prizes
unshared = {
    "prizes": {'$elemMatch': {
        'category': {'$nin': ["physics", "chemistry", "medicine"]},
        "share": "1",
        "year": {'$gte': "1945"},
    }}}

# Save a filter for laureates with shared prizes
shared = {
    "prizes": {'$elemMatch': {
        'category': {'$nin': ["physics", "chemistry", "medicine"]},
        "share": {'$ne': "1"},
        "year": {'$gte': "1945"},
    }}}

ratio = db.laureates.count_documents(unshared) / db.laureates.count_documents(shared)
print(ratio)
```

```
# Save a filter for organization laureates with prizes won before 1945
before = {
    'gender': 'org',
    'prizes.year': {'$lt': "1945"},
    }

# Save a filter for organization laureates with prizes won in or after 1945
in_or_after = {
    'gender': 'org',
    'prizes.year': {'$gte': "1945"},
    }

n_before = db.laureates.count_documents(before)
n_in_or_after = db.laureates.count_documents(in_or_after)
ratio = n_in_or_after / (n_in_or_after + n_before)
print(ratio)
```

## Filtering with RegEx
search for `'Poland'` substring
`db.collection.distinct("bornCountry", {'bornCountry': {'$regex': 'Poland'}})`

use `$options` operator for case insensitive version:
`db.collection.distinct("bornCountry", {'bornCountry': {'$regex': 'poland', '$options': 'i'}})`
 
 - pymongo driver includes a bson package with a Regex class:
 `from bson.regex import Regex`
`...Regex('poland', 'i')

### match beginning of field value: `^`
`db.collection.distinct('bornCountry', {'bornCountry': Regex('^Poland')})`
![[Pasted image 20220320163153.png]]
### escaping special characters: `\`
`db.collection.distinct('bornCountry', {'bornCountry': Regex('^Poland \(now')})`
![[Pasted image 20220320163159.png]]
### match end of field value: `$`
`db.collection.distinct('bornCountry', {'bornCountry': Regex('now Poland\)$')})`
![[Pasted image 20220320163206.png]]

```
from bson.regex import Regex

# Save a filter for laureates with prize motivation values containing "transistor" as a substring
criteria = {'prizes.motivation': Regex('transistor')}

# Save the field names corresponding to a laureate's first name and last name
first, last = 'firstname', 'surname'
print([(laureate[first], laureate[last]) for laureate in db.laureates.find(criteria)])
```

# Projection: getting only what you need
==reducing data to fewer dimensions==
- pass a dictionary as a second argument to the `find()` method of a collection;
```
docs = db.collection.find(
  filter={}
  , projection={'prizes.affiliations':1, '_id': 0)
```
- include fields with a `1`
- fields not included in the dict are not included in the projection
  - `'_id'` field is included by default - need an explicit `0` to leave it out
- returns `Cursor` iterable
  - collect from the iterable into a list

##### Only projected fields that exist are returned (no error is thrown)

```
# Use projection to select only firstname and surname
docs = db.laureates.find(
       filter= {"firstname" : {"$regex" : "^G"},
                "surname" : {"$regex" : "^S"}  },
   projection= ["firstname", "surname"]  )

# Iterate over docs and concatenate first name and surname
full_names = [doc["firstname"] + " " + doc['surname']  for doc in docs]

# Print the full names
print(full_names)
```

# Sorting
## post-query
- ok for small datasets
```
from operator import itemgetter

docs = sorted(docs, key=itemgetter('year'), reverse=False)

print([doc['year'] for doc in docs][:5])
```

## In-query
```
cursor = db.prizes.find({'category':'physics'}, ['year'], sort=[('year',1)])
print([doc['year'] for doc in cursor][:5])
``` 
- `1`: ascending
- `-1`: descending 
- `sort` argument is a list, so we can sort by multiple fields
- you can sort by fields that you do not project

```
from operator import itemgetter

def all_laureates(prize):  
  # sort the laureates by surname
  sorted_laureates = sorted(prize["laureates"], key=itemgetter("surname"))
  
  # extract surnames
  surnames = [laureate["surname"] for laureate in sorted_laureates]
  
  # concatenate surnames separated with " and " 
  all_names = " and ".join(surnames)
  
  return all_names

# find physics prizes, project year and name, and sort by year
docs = db.prizes.find(
           filter= {"category": "physics"}, 
           projection= ["year", "laureates.firstname", "laureates.surname"], 
           sort= [("year", 1)])

# print the year and laureate names (from all_laureates)
for doc in docs:
  print("{year}: {names}".format(year=doc['year'], names=all_laureates(doc)))
```

# Indices
- like a book index
  - each collection = book
  - each document = page
  - each field = type of content

### When to use:
- queries with **high specificity**: only a small subset of documents returned
  - otherwise, you might as well scan the whole collection
- large documents
- large collections
  - rather than load these into memory from disk, Mongo can use the much-smaller indexes

## adding single-field index
- index model: list of `(field, direction)` tuples
- directions: `1` ascending, `-1` descending
`db.collection.create_index(['year',1)])
- Mongo can read a single-field index in reverse
  - for multi-field indexes however, indexes matter
  
## adding compound (multi-field) index
`db.collection.create_index([('category', 1), ('year',1)])`
- index 'covering' a query with projection:
`list(db.prizes.find({'category': 'economics'}, {'year': 1, '_id':0}))`

### troubleshoot query performance
`db.collection.index_information()`
`db.collection.find({'firstname':'Marie'}, {'bornCountry':1, '_id':0}).explain()`: cursor method
![[Pasted image 20220320205037.png]]
- `COLLSCAN` = tablescan (bad!)
- fix:
`db.collection.create_index([('firstname', 1), ('bornCountry', 1)])`
![[Pasted image 20220320205258.png]]
- `IXSCAN`: much better!

```
# Specify an index model for compound sorting
index_model = [('category', 1), ('year', -1)]
db.prizes.create_index(index_model)

# Collect the last single-laureate year for each category
report = ""
for category in sorted(db.prizes.distinct("category")):
    doc = db.prizes.find_one(
        {'category': category, "laureates.share": "1"},
        sort=[('year', -1)]
    )
    report += "{category}: {year}\n".format(**doc)

print(report)
----------------------------------
chemistry: 2011
economics: 2017
... etc ...
```

### complex query with indexing, filtering, dict comprehension
```
from collections import Counter

# Ensure an index on country of birth
db.laureates.create_index([('bornCountry', 1)])

# Collect a count of laureates for each country of birth
n_born_and_affiliated = {
    country: db.laureates.count_documents({
        'bornCountry': country,
        "prizes.affiliations.country": country
    })
    for country in db.laureates.distinct("bornCountry")
}

five_most_common = Counter(n_born_and_affiliated).most_common(5)
print(five_most_common)
```
# Limits & skips
- in concert with sorting, this will help you find documents with extreme values

```
# get prize category & year information for a few prizes split 3 ways
for doc in db.subdoc.find({}, [laureates.share"]): # empty filter first
  share_is_three = [laureate["share" == "3" for laureate in doc["laureates"]]
  # either all or none of the prizes returned have a .share == '3'
  assert all(share_is_three) or not any(share_is_three)

for doc in db.subdoc.find({'laureates.share": "3"}, skip=3, limit=3):
  print("{year} {category}".format(**doc))
```
- combine skip & limit to get **pagination**
### Use cursor methods for `{sort, skip, limit}`
- alternative to passing extra params to the `.find()` method
```
for doc in (db.prizes.find({"laureates.share": "3"})
  .sort([("year", 1)]) # with single field, can also use .sort("year")
  .skip(3)
  .limit(3)):
    ...
```

### func to iteratively retrieve page numbers from complex query:
```
# Write a function to retrieve a page of data
def get_particle_laureates(page_number=1, page_size=3):
    if page_number < 1 or not isinstance(page_number, int):
        raise ValueError("Pages are natural numbers (starting from 1).")
    particle_laureates = list(
        db.laureates.find(
            {'prizes.motivation': {'$regex': "particle"}},
            ["firstname", "surname", "prizes"])
        .sort([('prizes.year', 1), ('surname', 1)])
        .skip(page_size * (page_number - 1))
        .limit(page_size))
    return particle_laureates

# Collect and save the first nine pages
pages = [get_particle_laureates(page_number=page) for page in range(1,9)]
pprint(pages[0])
```
# Aggregation
- total number of prize elements
- use projection to only fetch the data we need
```
docs = db.laureates.find({}, ['field']{

sum([len(doc['prizes'] for doc in docs])
```
- iterating over the `Cursor` in this way we avoid having to download the other data in each laureate document
- implicit stages of a query map to explicit stages of an aggregation pipeline
- aggregation pipeline is a list, pertaining to a sequence of stages
```
cursor = db.collection.aggregate([
  '{$match': {'bornCountry': 'USA'}}
  ,{'$project': {'field.subfield':1, "_id": 0}} # must be a dict in agg pipeline
  ,{'$sort': OrderedDict([('prizes.year',1)])} # this ensures compound indices are read in the order specified
  ,{'skip': 1}
  ,{'$limit':3}
])
for doc in cursor:
  print(doc['prizes'])
```

```
list(db.laureates.aggregate([
  {'$match':{'bornCountry':'USA'}},
  {'$count':'n_USA-born-laureates}
]))
```

- note that you may need to put the `$sort` stage before the `$project` stage if you are sorting on a field left out in that projection

### Field paths
- *expression object*: `{field1: <expession1>,...}`
- what you pass to an aggregation stage 
```
db.laureates.aggregate([
  {'$project':{'n_prizes':{'$size': '$prizes'}}}
]).next()
```
- operator expression: `{'$size': '$prizes'}`
- field path: `$prizes`
  - takes the value of the prizes field for each document processed at that stage of the pipeline
- note that you can create new fields, or overwrite old ones, during aggregation

### operator expressions treat the operator as a function
- expression applies the operator to one or more arguments and returns a value
- `$group` must map `_id`, which is unique
  - no `$match` before `$group`
    - all distinct 'bornCountry' values captured
    - including 'no value' (`None`)
```
list(db.laureates.aggregate([
  {'$project': {'n_prizes': {'$size':'$prizes'}}},
  {'$group': {'_id':None, 'n_prizes_total':{'$sum':'n_prizes'}}}
]))
```
- `{'_id':None}` - one document out
- `$sum` operator acts as accumulator in `$group` stage
## Access array elements during aggregation
```
list(db.field.aggregate([
  {'$project': {'n_records': {'$size: '$field_to_count}
                              , 'year': 1, 'category': 1}}
  ,{'$group': {'_id': '$category', 'n_records': 
    {'$sum': '$n_records'}}}
  ,('$sort': {'n_records': -1}},
]))


pipeline = [
    {'$match': {'gender': "org"}},
    {"$project": {"n_prizes": {"$size": '$prizes'}}},
    {"$group": {"_id": None, "n_prizes_total": {"$sum": 'n_prizes'}}}
]

print(list(db.laureates.aggregate(pipeline)))

from collections import OrderedDict

original_categories = sorted(set(db.prizes.distinct("category", {"year": "1901"})))
pipeline = [
    {"$match": {"category": {"$in": original_categories}}},
    {"$project": {"category": 1, "year": 1}},
    
    # Collect the set of category values for each prize year.
    {"$group": {"_id": "$year", "categories": {"$addToSet": "$category"}}},
    
    # Project categories *not* awarded (i.e., that are missing this year).
    {"$project": {"missing": {"$setDifference": [original_categories, "$categories"]}}},
    
    # Only include years with at least one missing category
    {"$match": {"missing.0": {"$exists": True}}},
    
    # Sort in reverse chronological order. Note that "_id" is a distinct year at this stage.
    {"$sort": OrderedDict([("_id", -1)])},
]
for doc in db.prizes.aggregate(pipeline):
    print("{year}: {missing}".format(year=doc["_id"],missing=", ".join(sorted(doc["missing"]))))
```

## Access array elements during aggregation
### Size -> Sum
```
list(db.prizes.aggregate([
  {'$project': 
    {'n_laureates':{'$size': '$laureates'}
    , 'year': 1
    , 'category': 1
    , '_id': 0}
  }
]))
- `$unwind` stage outputs one pipeline document per array element:
```
list(db.field.aggregate([
  {'$unwind': '$laureates'}
  ,{'$project': {
    '_id': 0, 'year': 1, 'category': 1, 'collection.field': 1, 'collection.other_field': 1}},
  {'$limit': 3}
]))
```
- don't use if you need to preserve null values

### Renormalization
- use stages after an unwind to recompress data
```
list(db.collection.aggregate([
  {'$unwind': '$laureates'}
  ,{'$project': {'year': 1, 'category': 1, 'collection.id': 1}}
  ,{'$group': {'_id': {'$_id': {'$concat': ['category', ':', '$year']}
    {'collection_ids': {'$addToSet': '$laureates.id'}}},
  {'$limit': 5}
]))
```