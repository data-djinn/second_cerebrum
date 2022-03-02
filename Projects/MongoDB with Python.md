[[Python]] [[Data Engineering]] [[NoSQL]]
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
  db[collectien_name].insert_many(documents)
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
 - merrge critere into a single filter document by:
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
  - use the `'$in':[value1,value2]` to wrap around acceptable values
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
doc = db.laureates.find_one(criteria)
```