[[MongoDB]]
# BLUF
- even though Mongo doesn't enforce it, it's **vital** to design a schema
- Indexes have to be designed in conjunction with your schema and access patterns
- **avoid large objects, and especially large arrays**
- be careful with MongoDB's settings, especially when int concerns security & durability
- MongoDB **does not have a query optimizer**, so you have to be very careful how you order the query operations

# Common Mistakes
**1. Creating Mongo Server without authentication**
  - MongoDB installs without authentication by default
  - this is fine on a workstation, acessed only locally
  - Mongo is much better on a server however - it uses as much memory as it can, and eats up all the RAM
  - **Installing on a server on the default port without authentication is dangerous**, especially when users can execute arbitrary Javascript within a query (e.g. `$where` as a vector for injection attacks
    - use a different port than default
    - check the logs for unauthorized access

**2. Forgetting to tie down MongoDB's attack surface**
- check [MongoDB's security checklist](https://docs.mongodb.com/manual/administration/security-checklist/)
- disable the use of arbitrary JS execution by setting `javascriptEnabled:false` in the config file (unless there is a very good reason to use `mapReduce`, `group`, or `$where`
- standard MongoDB data files are not encrypted
  - [Run MongoDB with a Dedicated User](https://docs.mongodb.com/manual/administration/security-checklist/#run-mongodb-with-a-dedicated-user) with full access to the data files restricted to that user, iot use the OS's own file-access controls

3. **Failing to design a schema**
- this will make import/storage ridiculously easy, but retrieval [will be very difficult](https://www.compose.com/articles/mongodb-with-and-without-schemas/)
- [6 Rules of Thumb for MongoDB Schema Design](https://www.mongodb.com/blog/post/6-rules-of-thumb-for-mongodb-schema-design-part-1)
- [Schema Explorer](https://studio3t.com/knowledge-base/articles/schema-explorer/)

4. **Forgetting about collations (sort order)**
- [MongoDB defaults to using binary collation](https://jira.mongodb.org/browse/SERVER-1920)
- [instead, use accent-insensitive, case-insensitive collation](https://weblogs.sqlteam.com/dang/archive/2009/07/26/Collation-Hell-Part-1.aspx)
  - this makes searching through string data much easier

5. **creating collections with large documents**
 - MongoDB can store large documents (up to 16MB)
   - this is a bad idea - [will cause several performance problems](https://www.reddit.com/r/mongodb/comments/573fqr/question_mongodb_terrible_performance_for_a/)
   - **keep individual documents down to a few KBs in size**
     - treat them more like rows in a wide SQL table

6. **Creating documents with large arrays**
- best to keep number of array elements well below 1000
- if the array is added to frequently, it will outgrow the containing document so that it's [its location on disk has to be moved](http://docs.mongodb.org/manual/core/data-model-operations/#document-growth)
  - this in turn means [every index must be updated](http://docs.mongodb.org/manual/core/write-performance/#document-growth)
  - because there is a separate [index entry for every array element](http://docs.mongodb.org/manual/core/index-multikey/), a lot of index re-writing will take place

7. **Not paying attention to the order of stages in aggregation pipelines**
[14 Things I Wish I’d Known When Starting with MongoDB (infoq.com)](https://www.infoq.com/articles/Starting-With-MongoDB/?msclkid=1c4dee66ad1f11eca40faa87b710fa93)