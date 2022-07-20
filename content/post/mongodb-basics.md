---
title: "Mongodb Basics"
date: 2022-05-09T20:41:49+06:30
draft: false
---

# MongoDB Basics

Created: July 21, 2021 4:41 PM

## What is Database

- A database is a structured way to store and access data.

## What is NoSQL Database

- A database that stores data in an organizing way without using table.

## What is MongoDB Database

- MongoDB is a NoSQL document database.
- Data are stored as documents.
- Documents are turn stored in what we called collections.
- Collection is an organized store of documents in MongoDB usually with common fields between documents.
- MongoDB database would contain multiple collections where they would contains multiple documents.

## What is document in MongoDB

- A way to organize and store data as a set of field-value pairs.
- Field is an unique identifier for a datapoint.
- Value is a data related to a given field ( identifier ).

```sql
{
  <field> : <value>,
  "name"  : "Alice",
  "age"   : 19
}
```

## Replica Set

a few connected machines that store the same data to ensure that if something happens to one of the machines the data will remain intact. Comes from the word replicate - to copy something.

## Instance

a single machine locally or in the cloud, running a certain software, in our case it is the MongoDB database.

## Cluster

group of servers that store your data.

---

## Atlas UI

### Two ways to interact with clusters

- Data explorer which can be navigated via collections button on cluster page.
- via shell, by clicking connect button on cluster page.

---

## How does MongoDB store data

- JSON store data in key-value pair.
- Keys are also known as fields in MongoDB
- JSON are turn into BSON(Binary JSON).
- MongoDB stores data in BSON format both internally, and over the network.
- Data can also be retrieved from MongoDB as JSON.
- MongoDB stores data in BSON, view in JSON.

---

## What is BSON

- BSON bridges the gap between binary representation and JSON.
- BSON is lighter, faster than JSON.

```bash

{"hello": "world"} →

\x16\x00\x00\x00           //total document size
\x02                       // 0x02 = type String
hello\x00                  // field name 
\x06\x00\x00\x00world\x00  // field value
\x00                       // 0x00 = type EOO ('end of object')  
```

## Pros and Cons of JSON

### Pros of JSON

- User friendly
- Readable
- Familiar for programmers

### Cons of JSON

- Text-based format
- Text parsing is very slow
- Limited number of basic data types
- Space consuming, another database concern

---

## JSON vs BSON

### Encoding

- JSON use UTF-8.
- BSON use binary.

### Data type support

- JSON : String, Boolean, Number, Array
- String, Boolean, Number (Integer, Float, Long, Decimal128...), Array, Date, Raw Binary

### Readability

- JSON : Human and machine
- BSON : Machine only

---

## Importing and Exporting Data

We can import/export data in BSON and JSON format. 

To view and read data, should choose JSON format.

To transfer data, should choose BSON format. 

### Import/Export in JSON Format

To import data in JSON format, use `mongoimport` command.

Example ,

`mongoimport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --drop sales.json`

To export data in JSON format, use 

`mongoexport` command. 

Example, 

`mongoexport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --collection=sales --out=sales.json`

## Import/Export in BSON Format

To import data in BSON format, use `mongorestore` command. 

Example,

`mongorestore --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies"  --drop dump`

To export data in BSON format, use `mongodump` command. 

Example,

`mongodump --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies"`

 

*srv* in URI means it established secure connection. 

---

## Querying Collections and Database

### Via data explorer

*Namespace* - The concatenation of the database name and collection name is
called a namespace.

Example : `sample_training.grades`, where *sample_training* is database name while *grades* is collection name.

We looked at the sample_training.zips collection and issued the following
queries:

- {"state": "NY"}
- {"state": "NY", "city": "ALBANY"}

Remember queries are case-sensitive.

## Via MongoDB shell

Syntax of connection string :

`mongo "mongodb+srv://<username>:<password>@<cluster>.mongodb.net/admin"`

`admin` is default database that does a number of administrative things.

Once we are connected, first make sure all the databases are there and we are connected to the right cluster.

 `show dbs` command shows the list of databases in the cluster.

To use one of the databases ,use `use` command.

Syntax    : `use <database name>`

Example : `use sample_training`

To view the collections of an active database, use `show collections` command.

To find with a given criteria.

`findOne`  :  If there are multiple documents that match a given criteria, only one of of them will be showed.

`find` : This will give all results those match a given criteria.

Syntax    : `db.collection_name.find(query)`
Example : `db.zips.find({"state": "NY"})`

We don't have to specify database name, since we navigated to the needed database earlier.

`db` object is pointing to sample_training which is a database.

Query results are show in random, not in some sorting.

To view more result of query, use `it`command which iterates through the cursor.

To get the total number of query results, use `count` operation at the end of find command.

Syntax    : `db.collection_name.find(query).count()`

Example : `db.zips.find({"state": "NY"}).count()`

To read the results easier, use `pretty` operation at the end of find command.

Syntax    : `db.collection_name.find(query).pretty()`

Example : `db.zips.find({"state": "NY", "city": "ALBANY"}).pretty()`

---

## Inserting new documents - ObjectId

### Via data explorer

- `_id` is a unique identifier for a document in a collection.
- `_id` is required in every MongoDB  document.
- `ObjectId` is the default value for the `_id` field unless otherwise specified.

### Via Mongo Shell

Navigate to the needed database.

Syntax    : `db.<collection_name>insert([ <query> ])`

Example : `db.inspections.insert([ { "test": 1 }, { "test": 2 }, { "test": 3 } ])`

Above example will not give you a duplicate error since `_id` are automatically generated before insertion.

Also we can give an unique id `_id` to a document ourselves.

When the default ordered insert happened, the moment there is a duplicate key error, the insert operation halts.

Even if the rest of the documents have unique IDs, they won't get a chance to be inserted.

add `{ "ordered": false }` option at the end of the insert command to get inserted even the other data encountered duplicate key error.

Example : `db.inspections.insert([ { "_id": 1, "test": 1 }, { "_id": 1, "test": 2 }, { "_id": 3, "test": 3 } ] , { "ordered": false })`

Above example will insert `"_id": 3, "test": 3` to the collection although duplicate key error encountered on the first two items.

Always take care of collection name in `db.<collection_name>insert([ <query> ])` 

---

## Updating Documents

### via data explorer

In collections, navigate to the needed document and click `Edit` button on the right side of the frame.

### Via Mongo Shell

`updateOne` : If there are multiple documents that match a given criteria, only one of of them will be updated.

`updateMany` : This will update all documents that match a given criteria.

Syntax : `db.collection_name.update(query, update, options)`

Example : 

```markdown
db.iot.updateOne({ "sensor": r.sensor, "date": r.date,
                   "valcount": { "$lt": 48 } },
                         { "$push": { "readings": { "v": r.value, "t": r.time } },
                           "$inc": { "valcount": 1, "total": r.value } },
                 { "upsert": true })
```

Here the yellow part is query, green part is update and the white one is the option.

### Some update Operators

`$inc`     : increases field values by a specified amount.

Syntax    : `{ "$inc" : { "field1" : "value1" , "field2" : "value2" } }`

Example : `db.<collection_name>.updateMany( { "city" : "Mandalay" } , { "$inc" : { "population" : "10" } } )`

`$set`     :  set field values to a new specified value.

Syntax    : `{ "$set" : { "field1" : "value1" , "field2" : "value2" } }` 

Example : `db.<collection_name>.updateMany( { "city" : "Mandalay" } , { "$set" : { "population" : "133337" } } )`

*Remember if the field that you specified does not exist in the document , then it will create a new field-value in the document.*

`$push`   : adds an element to an array field.

`$push`   :  turns a field into an array field if it was previously a different type. 

If the field is absent in the document to update, `$push` adds the array field with the value as its element.

Syntax    : `{ "$push" : { "array-field-name" : { "new-sub-field1" : "new-sub-value1" } } }` 

Example : `db.<collection_name>.updateMany( { "student_id" : 123 ,"class_id" : 2 } ,  { "$push" : { "scores" :  { " type " : "extra score" , "value" : 100 } } }  )`

`upsert option`

upsert is a combination of update and insert (update + insert = upsert).

If the value of this option is set to true and the document or documents found that match the specified query, then the update operation will update the matched document or documents.

Or if the value of this option is set to true and no document or documents matches the specified document, then this option inserts a new document in the collection and this new document have the fields that indicate in the operation.

Syntax : `upsert: <boolean>`

Example : `db.employee.update({name:"Priya"}, {$set: {department: "HR"}}, {upsert:true})`

When upsert is set to `true` it can perform an insert if the query predicate doesn't return a matching document.

When upsert is set to `false` an update will happen only when the query predicate is matched with a document from the collection.

---

## Deleting documents and collections

### Via data explorer

On hover, a trash-icon appears.Click that icon to delete the database or collection or document.

### Via Mongo Shell

`deleteOne`   :  If there are multiple documents that match a given criteria, only one of of them will be deleted.

`deleteMany` : This will delete all documents that match a given criteria.

To delete a collection, use `drop` operation.

Syntax : `db.<collection_name>.drop()`

> *Note  : Removing all collections in a database also remove the database.*
> 

---

## Query Operators

Provide additional ways to locate within the database.

### Comparison Operators

`$eq`  : Equal To

`$nq`  : Not Equal To

`$gt`  : Greater Than

`$lt`  : Less Than

`$gte` : Greater Than or Equal To

`$lte` : Less Than or Equal To

Syntax    : `{ <field> : { <operator> : <value> } }`

Example : `db.trips.find( { "tripduration" : { "$lte" : 70 } , "usertype" : { "$ne" : "subscriber" } } ).pretty()`

Meaning : Find all documents where the *tripduration* was *less than or equal to* 70 seconds and the *usertype* was *not* Subscriber.

---

### Logic Operators

Logic operators will allow us to be more granular in our search of data.

`$and` : Match all of the specified query clauses

Syntax    : `{ <operator> : [ {statement1} , {statement2} , ... ] }`

Example : `{"$and" : [{"tripduration" : {"$gt":350 ,"$lt":380 }}]}`

Above example can also be written as : `{"tripduration" : {"$gt":350,"$lt":380}}`

*Note : `$and` is used as default operator when an operator is not specified.*

`*{ "sector" : "Mobile Food Vendor - 991 ",  "result " : "Warning"  }*`

Explicitly use `$and` when you need to include the same operator more than once in a query.

Example : 

```
db.routes.find({ "$and": [ { "$or" :[ { "dst_airport": "KZN" },
                                    { "src_airport": "KZN" }
                                  ] },
                          { "$or" :[ { "airplane": "CR2" },
                                     { "airplane": "A81" } ] }
                         ]}).pretty()
```

`$or`   : At least one of the query clauses is matched

Syntax    : `{ <operator> : [ {statement1} , {statement2} , ... ] }`

Example : `{ "$or" : [ { "dst_airport" : "KZN" } , { "src_airport" : "KZN" } ] }`

`$nor` : Fail to match both given clauses

Syntax    : `{ <operator> : [ {statement1} , {statement2} , ... ] }`

Example : `{ $nor : [ { "result" : "No Violation Issued" } , { "result" : "Violation Issued" } ] }`

`$not` : Negates the query requirement

Syntax    : `{ field: { $not: { <operator-expression> } } }`

Example : `{ price: { $not: { $gt: 1.99 } } }`

Note : Since `$not` is a logical operator, this can be used for only value.

### `$ne` VS `$not` Operators

`$ne` is a comparison operator.

`$not` is a logical operator.

`$ne` selects the documents where the value of the field is not equal to the specified value. This includes documents that do not contain the field.

`$not` performs a logical NOT operation on the specified `<operator-expression>` and selects the documents that do not match the `<operator-expression>`. This includes documents that do not contain the field.

`{ "price": { "$not": { "$gt": 1.99 } } }` : price not greater than 1.99 

`{ "qty" : { "$ne": 20 } }` : number of qty is not equal to 20

`{ "qty" : {"$not" : {"$ne" : 20} } }` : reject all qty that is not equal to 20, this means it accepts only 20.

---

## Expressive Query Operator

`$expr` allows the use of aggregation expressions within the query language.

`$expr` allows us to use variables and conditional statements.

Syntax    : `{ $expr : { <expression> } }`

Example : `{"$expr" : { "$eq" : ["$start station id","$end station id"] }}`

`$` denotes the use of an operator.

`$` addresses the field-value.

If we don't use `$` in this example, we have to look for a specific field value in all documents rather than compare a value that varies from document to document to another value that varies from document to document.

While using expressive query operator, we must use `$` to describe a field.

default MQL syntax : `{ <field> : {<operator> : <value>} }`

Aggregation syntax : `{ <operator> : {<field> , <value>} }`

*A closer look*

```sql
{ "$expr" : {

             "$and" : [ 

                       {"$gt" : 1200 } ,

	               {"$eq" : ["$start station id" , "$end station id"]  } 

] } }
```

---

## Array Operators

Syntax    : `{ <array field> : [ item1, item2 ] }`

Example : `{"beg" : ["pen","book"]}`

In array field, index is important.

`["car","house"]` and `["house","car"]` will give you different results. 

`$all` will ignore ordering.

Example : `{"beg" : {"$all" : [ "pen","book" ] } }` 

 `$size` will return the exact length of given size. 

Example : `{"beg" : {"$size" : 2, [ "pen","book" ] } }`

---

## Projection

Projection is used to look only at fields that we are interested.

Example : `{ "amenities": "WiFi" },{ "price": 1, "address": 1, "_id": 0 }`

The first part (*highlight in yellow*) is what we want to find, while the second part (*highlight in green*) is what we want to display.

So, in 2nd part, number `1` means we want to include that field in the result, number `0` to exclude from the result.

Example : 

Q : Find one document in the collection and only include the address field in the resulting cursor.

Ans :  `db.listingsAndReviews.findOne({ },{ "address": 1, "_id": 0 })`

### $elemMatch

`$elemMatch` is used to match documents that contain an array field with at least one element that matches the specified query criteria.

Syntax    : `{ <field> : { "$elemMatch" : { <field> : <value> }}}`

Example : `{ "scores": { "$elemMatch": { "type": "exam" } }}`

---

## Array Operators and Sub-Documents

MQL uses dot-notion (`.`) to specify the address of nested elements in a document.

To use dot-notion in arrays, specify the position of the element in the array.

Example : `{"start station location.coordinates.0":-73.97966069}`

---

## Aggregation Framework 101

To use aggregation framework, we use `aggregate` instead of `find`.

With the aggregation framework, we can calculate, reshape, reorganize data.

*Syntax Comparison*

```markdown
db.listingsAndReviews.find(
				{ "amenities" : "WiFi" },
				{ "price" : 1 , "address" : 1 , " _id " : 0 }).pretty()
```

```
db.listingsAndReviews.aggregate([
				{ "$match" : { "amenities" : "WiFi" }},
				{ "$project" : { "price" : 1 , "address" : 1 , " _id " : 0 }}
])
```

The aggregation framework works as a pipeline, where the order of the actions in the pipeline matters.

![MongoDB%20Basics%2067c759e006ba4516a6326ff2caeff2f8/Untitled.png](MongoDB%20Basics%2067c759e006ba4516a6326ff2caeff2f8/Untitled.png)

### Introducing the $group stage

`$group` : An operator that takes the incoming stream of data, and siphons it into multiple distinct reservoirs.

![MongoDB%20Basics%2067c759e006ba4516a6326ff2caeff2f8/Untitled%201.png](MongoDB%20Basics%2067c759e006ba4516a6326ff2caeff2f8/Untitled%201.png)

Syntax : 

```markdown
{"$group":
	{
		_id : <expression>, //group by expression
		<field1> : { <accumulator1>,<expression1> },
		... 
	}
}
```

We can give custom field name in `$group`'s second part (*highlight in yellow*) .

Examples : 

*Q1 : Project only the address field value for each document, then group all documents into one document per address.country value.*

```markdown
db.listingsAndReviews.aggregate([ { "$project": { "address": 1, "_id": 0 }},
                                  { "$group": { "_id": "$address.country" }}])
```

```markdown
{ "_id" : "Canada" }
{ "_id" : "Hong Kong" }
{ "_id" : "Spain" }
{ "_id" : "Turkey" }
{ "_id" : "China" }
{ "_id" : "Brazil" }
{ "_id" : "United States" }
{ "_id" : "Australia" }
{ "_id" : "Portugal" }
```

*Q2 : Project only the address field value for each document, then group all documents into one document per address.country value, and count one for each document in each group.*

```markdown
db.listingsAndReviews.aggregate([
                                  { "$project": { "address": 1, "_id": 0 }},
                                  { "$group": { "_id": "$address.country",
                                                "count": { "$sum": 1 } } }//here we give 1 to allow $sum.
                                ])
```

```markdown
{ "_id" : "Portugal", "count" : 555 }
{ "_id" : "Spain", "count" : 633 }
{ "_id" : "Hong Kong", "count" : 600 }
{ "_id" : "China", "count" : 19 }
{ "_id" : "Turkey", "count" : 661 }
{ "_id" : "United States", "count" : 1222 }
{ "_id" : "Brazil", "count" : 606 }
{ "_id" : "Canada", "count" : 649 }
{ "_id" : "Australia", "count" : 610 }

//this means there're 555 documents that have "Portugal" in address.
```

This image will clarify what we did.

![MongoDB%20Basics%2067c759e006ba4516a6326ff2caeff2f8/group_sum.jpeg](MongoDB%20Basics%2067c759e006ba4516a6326ff2caeff2f8/group_sum.jpeg)

*Note : Non-filtering stages do not modify the original data when they do the summaries,calculation and grouping of data.Instead they work with the data that they get from the previous stage in the pipeline, which is in its own cursor.*

---

## Sorting and Limit

### Sorting

In MongoDB, sorting is done by the `sort()` method.

The `sort()` method consists of two basic building blocks, *1. fields to be sorted 2. the sort order*

The sorting order in MongoDB is defined by either a one (`1`) or minus one (`-1`).

*+1 : Ascending order*

*-1 : Descending order*

Syntax    : `db.collection_name.find().sort( { field_name : sort_order } )`

Example : `db.zips.find().sort({ "pop": 1 })`

### Sorting using multiple fields

The query will be sorted according to the declaration position of the fields, where sort order is evaluated from left to right.

Syntax    : `db.collection_name.find().sort( { field_name1 : sort_order , field_name2 : sort order } )`

Example : `db.zips.find().sort({ "pop": 1 , "zip":1 })`

### Sorting with the limit() method

`limit()` method limits the number of results in the search query.

Just pass an integer into `limit()` method.

Syntax    : `db.collection_name.find().sort( { field_name : sort_order } ).limit(n)`

Example : `db.zips.find().sort({ "pop": 1 }).limit(2)`

*Note : While the limit() and sort() methods are not listed in the correct order, MongoDB flips their order when executing the query, delivering the results that the question prompt is looking for.*

*That is `find().sort().limit()` is equal to `find().limit().sort()`*

---

## Indexing and Aggregation Pipeline

### Introduction to Indexes

In database, special data structure that stores a small portion of the collection's data set in an easy to traverse form.

Indexes support the efficient resolution of queries. Without indexes, MongoDB must scan every document of a collection to select those documents that match the query statement. This scan is highly inefficient and require MongoDB to process a large volume of data.

To create an index, you need to use `createIndex()` method of MongoDB.

Syntax : `db.collection_name.createIndex({KEY:1})`

`KEY` is the name of the field on which you want to create index.

*+1 : Ascending order*

*-1 : Descending order*

Example : `db.trips.createIndex({ "birth year": 1 })`

            `db.trips.createIndex({ "start station id": 1, "birth year": 1 })`

*When it is a simple single-field index, It doesn't really matter whether the index was created in increasing or decreasing order.*

---

### Introduction to Data Modeling

***Data modeling*** is a way to organize fields in a document to support your application performance and querying capabilities.

*Data Modeling with MongoDB*

- Data that is used together should be store together.
- Evolving application implies an evolving data model.

[MongoDB Basics 6f502173b5e9439a86cb42022c059dfd](https://www.notion.so/MongoDB-Basics-6f502173b5e9439a86cb42022c059dfd-92775f3203e84f19a5eb320c6b394526)
