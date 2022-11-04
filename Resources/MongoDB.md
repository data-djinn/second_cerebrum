[[MongoDB with Python]] [[NoSQL Databases]] [[Data Engineering]]
# What is MongoDB
- NoSQL document database
  - data is stored as **documents**
    - a way to organize & store data as a set of **field-value pairs**
  - documents are stored in **collections**
    - organized store of documents, usually with common fields between documents

#### MongoDB URI
==Uniform Resource Identifier==
- looks like a url
- used to tell the driver the hostname+port+username+pw
	- everything about the connection can be specified by the URI
- if the srv 

## MongoDB Atlas
- database as a service
  - deploys **clusters**: group of servers that store your data
  - configured in **replica sets**: a few connected MongoDB **instances** that store the same data
    - **instance**: single machine locally or in the cloud, running a certain software
    - this setup ensures that if one instance in your replica set goes down, your data will remain intact & available for use by the application
### Pricing
##### Free tier
- 1 free cluster
- 3 server replica set
- 512 MB storage
- never expires
- charts
- realm
- good for small-scale app

# Importing, exporting, querying data
### Document Representation & Syntax
**JSON**: Javascript Standard Object Notation
- starts and ends with `{}`
```json
{
  'key':value,
  'key2':value
}
```
  - in MongoDB, `'keys'` are called **fields**
  - **Sub-documents** contain other fields as value (nested)

| Pros of JSON          | Cons of JSON                       |
| --------------------- | ---------------------------------- |
| User-friendly         | Text-based (slow to parse)         |
| Human-Readable        | Space-inefficient                  |
| Familiar to many devs | Limited number of basic data types |

So, mongo uses **BSON** (binary JSON)
- bridges the gap between binary representation & JSON
- Optimized for:
  - speed
  - space
  - Flexibility
- High-performance
- general-purpose

| JSON                           | BSON                                                        |
| ------------------------------ | ----------------------------------------------------------- |
| UTF-8 encoding                 | binary encoding                                             |
| String, Boolean, Number, Array | String, Bool, Integer, Long, Float, Array, Date, Raw Binary |
| Human & machine readable       | Machine-readable only                                       |

### importing & exporting
##### BSON:
*use when migrating data to new Mongo cluster*
- export to a different system
```shell
mongorestore 
  --uri '<atlas cluster URI>'
  --drop dump
```
- backup cloud data locally
`mongodump --uri '<atlas cluster URI>'`  
##### JSON
*use when extracting the raw data for other purposes*
- export to a local machine
```shell
mongoexport 
  --uri '<atlas cluster URI>'
  --collection <collection name>
  --out <filename>.json`
```
- import  (can also import csv!)
```shell
mongoimport 
  --uri '<atlas cluster URI>'
  --drop <filename>.json
  --collection <collection name>
```

### Data Explorer
