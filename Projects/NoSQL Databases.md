[[Database Design]] [[Data Engineering]][[MongoDB with Python]][[redis]]

| relational databases                     | NoSQL databases                           |
| ---------------------------------------- | ----------------------------------------- |
| use tables/rows/columns                  | don't use tables/columns/rows             |
| need a predefined schema, hard to change | schema-less, easy changes                 |
| vertically scalable: add more CPU/RAM    | horizontally scalable - add more machines |
| more expensive                           | cheaper                                   |
| slower when joining multiple tables      | faster with good design                   |
| ACID transactions                        | not acid compliant                        |

- they can coexist, even coplement each other!
# Key-value databases
- simplest NoSQL DB
- Get/set values with associated key
key1: 'hello'
key2: 'goodbye'
#### Keys
- can be any binary sequence
- must be unique
- can be generated by algorithms
- avoid long keys
- `:` is conventionally used to name keys, e.g. `user:id:preferences`
#### Values
- associated with a key
- retrieve, set, delete a value by key
- numbers, strings, JSON, images
- often have size restrictions

example: using Redis for storing user preferences in one object

## Advantages
- very simple! just need key:value tuple
- no defined schema/types
- few operations
- very fast operations
- flexible: can change data types, add attributes
- information is stored in memory - fast read/writes but can lose data
- horizontal scalable via sharding (distributing different parts of the data across servers)
#### basic operations:
  - **put**
      - inserts a new k:v tuple
      - updates a value if the key already exists
  - **get**
      - returns the value of a given key
  - **delete**
      - removes a key & its value (if it exists)  

## Limitations
- can only search by key - problem if we don't know the key
  - some if we don't know the key
  - some k:v dbs have added functionality to:
      - search by value
      - add secondary indexes
      - search by several keys simultaneously
  - no complex queries  

## when to use
#### Suitable cases
- user sessions
  - key: session id
  - value: info about session
- user profiles & user preferences
  - key: user id
  - value: user preferences/settings applied
- shopping carts
  - key: user id
  - value: info about items added to cart
- real-time recommendations
- advertising
all these examples store *all* information in a single object
information is saved & retrieved with a single operation

- **Single view applications**
    - financial services
    - government
    - high tech
    - retail
    - gaming
    -  catalogs/online retailing
    -  real-time analytics
    -  content management
    -  internet of things

## When *not* to use
- searching data by its value
- related data
## mongoDB
[[MongoDB with Python]]
- stores data in BSON (binary representation of json)
- uses MQL: `db.users.find({"address.zipcode" : "10245" })
- native drivers for C#, Java, Python, Scala, & more
- allows **indexes on any field**
#### **ACID transactions!**
#### **Joins!**
#### scale horizontally
  - native sharding
  - add/move shortcuts
#### replication
- 50 copies of our data
### MongoDB Products:
##### mongoDB compass:
 - free GUI
 - explore schema, create queries visually
##### MongoDB Atlas
- managed cloud service
- AWS, Azure, Google Cloud
##### MongoDB Enterprise advanced
- run MongoDB on our infrastructure
##### Realm Mobile Database
# Document databases
- store data in documents, which are grouped into **collections**
- documents = rows, collections = tables
## Documents
- set of key:value pairs
  - keys: strings
  - values: numbers, strings, booleans, arrays, or objects
- schemaless: no need to specify structure
- Formats: JSON, BSON, YAML, or XML
- unlike k:v stores, you can elaborate more complex queries
## Collections
- set of documents
- typically store the **same type of entities**
- organize documents & collections by thinking about the queries

## Advantages
#### Flexibility
- no pre-defined schema
  - documents can vary over time
  - **avoid schema migrations**
- embedded documents **avoid costly joins**
  - better performance
#### intuitive for devs
- json is human-readable
- documents map objects in code
  - less coding
  - simpler and faster development
  - start coding and storing objects as documents are created
- easier for new developers
#### horizontal scalability
- sharding
## Limitations - more responsibility!
- care more about data in the application code
  - e.g. check required email in the application code if it's required
- care about redundant data
  - may introduce redundancy for optimizing application code
  - **remember to update *all* documents containing redundant data**
  
## when to use
 - catalogs: ecommerc/product info
   - different attributes between the products
 - event logging
   - user log in
   - product purchase
     - shard by time or type of event
 - user profile
   - info may vary
 - content management systems
   - comments
   - images
   - videos
 - **real-time analytics**
   - page views, unique visitors, etc.
   - easy to add new metrics over time
## when not to use
- very structured data
- need to always have consistent data 

# Column family databases
- derived from Google BigTable
- store data in column families
  - group related data
  - frequently acessed together
- also called **wide column** databases
- great when dealing with large volumes of data
- **column family = table equivalent**
- **row key**: unique ID
  - primary key equivalent
  - each row contains columns
  - rows can have **different number of columns than other rows**
  - columns can be added to a row when needed
#### Parts of the column:
- name: 
- value: type (depending on the db)
- timestamp: records when the data was inserted
  - allows us to store multiple values of a column (just use latest timestamp)
![[Pasted image 20220317215026.png]]
### Design:
- think about the queries we will be running, and include that information in column families
- **no joins** - add all the columns you need in the row key

#### Graph databases
