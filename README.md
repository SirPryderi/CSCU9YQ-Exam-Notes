# NoSQL Databases

`WARNING: these notes might or might not contain Game of Thrones spoilers`

## Relational Databases (SQL)
In **relational databases** data is structured in _tuples_ (rows), _relations_ (tables), and _schemas_ (fixed table, columns and type structures). Primary keys identify rows within a table, while foreign keys allow to refer rows between tables.

They have been successful because:
- Persistence – allow to store a huge of data in a highly consistent manner.
- Concurrency control – Many users can access it at the same time without errors.
- Integration mechanism – Many applications can use the same database at the same time.
- A standard model – It became the standard approach to databases.

Relational databases are designed to run on a single machine, and they only **scale vertically** (improve machine).

## NoSQL
NoSQL databases are an umbrella term for all the databases that are not based on relational data model (SQL).

They focus on big data and performance, rather than keeping a strict consistent schema (_performance over consistency_).

NoSQL databases are designed to be **clustered** in different machines, so they easily **scale horizontally**, spreading the load on different machines, making it more resilient and cheaper to expand.

Other advantages of NoSQL are:
- Relational databases type do not work well with **object oriented** programming, but NoSQL database data types do
- NoSQL databases are designed as application databases, where one application uses one database, specifically designed for that task.

The table below offers a list of example NoSQL databases based on their **data model**.

| Data Model    | Example Databases                                              |
| ------------- | -------------------------------------------------------------- |
| Key-Value     | BerkleyDB, LevelDB, Memcached, Project Voldermort, Redis, Riak |
| Document      | CouchDB, MongoDB, OrientDB, RavenDB, Terrastore                |
| Column-Family | Amazon SimpleDB, Cassandra, Hbase, Hypertable                  |
| Graph         | FlockDB, HyperGraphDB, Infinite Graph, Neo4j, OrientDB         |

The first four data models (Key-Value, Document, Column) have an **aggregate** orientation, i.e. that a collection of related objects are treated as single unit. The aggregate often becomes the central point of the database, guaranteeing _atomicity_, _consistency_, _isolation_ and _durability_ (ACID).

## Big Data
**Big data** is an extremely large data sets that may be analysed computationally to reveal patterns, trends, and associations, especially relating to human behaviour and interactions. They are come in an unstructured form, so they are not ideal to be stored in relational databases without extensive parsing.

## JSON – JavaScript Object Notation
**JSON** (_JavaScript Object Notation_) is a lightweight data-interchange format. It is easy for humans to read and write. It is easy for machines to parse and generate.

Example JSON object:
```json
{
  "name":"Daenerys",
  "dragons": 
  [
    {"name": "Drogon"},
    {"name": "Rhaegal"},
    {"name": "Viserion", "wight": true}
  ],
  "fucksGiven": 0
 }
```

## MongoDB
MongoDB is a cross-platform **document-oriented** database program. Classified as a NoSQL database program, MongoDB uses JSON-like documents with schemata. MongoDB is developed by MongoDB Inc. and licensed under the Server Side Public License.

Each record in a MongoDB is a **document** an object similar to JSON, organised in a field-and-value structure. Values can be literals, arrays, and other documents. Internally, data documents are stored as **BSON**, which stands for binary JSON, and offers a few additional data types and references to other documents.

All records in a MongoDB database need to have an `_id` field. If not explicitly specified at creation time, it will be created automatically. The autmatically created ids contain the UNIX timestamp in the first 4 bytes.

Similar documents are stored in **collections**, which are comparable to relational database's tables. In turn, collections are stored in **databases**.

The **mongo shell** is an interactive JavaScript interface to MongoDB. You can use the mongo shell to query and update data as well as perform administrative operations.

### Query Language
MongoDB has a JavaScript-based query language, that allows **CRUD** (create, read, update delete) operations.

#### Create operations
Databases are created automatically when the first record is inserted. 

Selecting to select a database called `myDB` (even if it's non existent):

```javascript
use myDB
```

Then it is possible to insert the data ``{"name": "Daenerys", "dragons": 3}`` in the collection `characters` using:

```javascript
db.characters.insertOne({name: "Daenerys", dragons: 3})
```

This will create the document, the the collection and the database. It is also possible to use `insertMany`, by providing an array of documents, to insert more than one record at a time.

To explicitly create a collection named `kingdoms` use:

```javascript
db.createCollection("kingdoms")
```

This allows to specify various options and constraints to the collection, using an optional parameter.

#### Read operations
It is possible to query the database according to certain criteria using the `db.<collection>.find()` method.

To find all the records in a collection called `characters`:

```javascript
db.characters.find({})
```

To find all the records with the attribute `alive` set to `true`:

```javascript
db.characters.find({alive: true})
```

To find all the records that have the attribute `from` set either to `Winterfell` or `King's Landing`:

```javascript
db.characters.find({
    from: {
        $in: ["Winterfell", "King's Landing"]
    }
})
```

Filters can be combined. To combine the two above:

```javascript
db.characters.find({
    from: {
        $in: ["Winterfell", "King's Landing"]
    },
    alive: true
})
```

#### Update Operations

Using the queries it is possible to update existing documents. This is somewhat similar to a search operation, as the records need to be selected first.

There are three methods to update records: `updateOne`, `updateMany`, and `replaceOne`.

To update all the records with `fromHouse` set to `Lannister`, so that they their `status` attribute is sent to `paid`:

```javascript
db.debts.updateMany(
    {fromHouse: "Lannister"},
    {$set: {status: "paid"}}
)
```

#### Delete Operations

Two methods allow the removal of documents: `deleteMany` and `deleteOne`.

To remove all documents with the `status` set to `paid` from the `debt` collection:

```javascript
db.debt.deleteMany(
    {status: "paid"}
)
```

### Distribution Model
MongoDB uses both **replication** and **sharding** to improve performances and data reliability.

#### Replication
In databases **replication** allows to have the same data available across multiple servers. This guarantees redundancy (**fault tolerance**) (in case of the loss of one of the servers) and better performances (**load balance**) (load balanced between the instances). This requires some sort synchronisation between the servers, which can happen either synchronously or asynchronously.

In **synchronous replication** all replicas are updated when a write operation occurs. This guarantee that all replicas are consistent (**strict consistency**), guaranteeing more resilience, but this affects performance if there are too many writes operations.

In **asynchronous replication** writes propagate as soon as possible, but there's no guarantee that the reads are going to be up to date (**eventual consistency**). This is normally fine because the propagation time is very quick, and usually marginally non-updated records are not a problem. Replicas might have a **master-slave** (only master is written, changes propagate to read-only replicas) configuration or **peer-to-peer** (each node is read/write and changes propagate to the others).

#### Sharding
Differently from replication, sharding spread the data among different nodes in a cluster. This means that only a subset of the data is available to each node.

#### Replication and Sharding in MongoDB
MongoDB is sharded on a collection level. Keys are used to quickly retrieve the document from the right shard. 

Each shard has its own replication model. MongoDB uses master-slave asynchronous replication. The primary replica is guaranteed to up always up to date, while the read-only replicas receive the propagated updates from the master. This means that replicas are not immediately up to date (eventual consistency).


### Data Aggregation
**Data aggregation** is the process of gathering and displaying information in form of a summary, usually for statistical purposes. Aggregation functions are often used such as: average, maximum, minimum, sum, etc.

Data aggregation can be achieved in three ways in MongoDB:
- Aggregation Pipeline 
    - Uses native code
- Map-reduce function
    - Use custom JavaScript functions
    - Harder to program
    - Slower
- Single purpose aggregation methods
    - Limited
 
 #### Aggregation Pipeline
 The **aggregation** pipeline is a multi-stage process that transforms the document into an aggregated result. It provides filters, transformations, grouping and arrays manipulation.
 
 The syntax for the aggregation pipeline is the following:
 
 ```javascript
db.<collection>.aggregate ([
    {<stage1>},
    {<stage2>},
    ...
    {<stageN>},
])
```

Common stages are:
- **$match** – Filters the collection according to the query.
- **$group** – Groups the documents according to an expression.
- **$project** – Reshapes the structure of the collection, adding and removing fields.
- **$sort** – Reorders the documents according to some criterion.
- **$unwind** – Deconstructs an array so that it generates a new document for each element.

##### Match
The `$match` stage filters the document stream according to a certain criterion, similar to `db.<collection>.find()`. This can be used to reduce the size of the initial stream, so it is a good idea to use it first.

Given the following dataset:

```json
{
    "name": "Jon Snow",
    "house": "Stark",
    "from": "Winterfell",
    "walkersKilled": 32
}
{
    "name": "Benjen Stark",
    "house": "Stark",
    "from": "Winterfell",
    "walkersKilled": 89
}
{
    "name": "Jeor Mormont",
    "house": "Mormont",
    "from": "Bear Island",
    "walkersKilled": 15
}
{
    "name": "Samwell Tarly",
    "house": "Tarly",
    "from": "Horn Hill",
    "walkersKilled": 1
}
```

In order to fetch all the records that have `from` set to `Winterfell`:

```javascript
db.nightwatch.aggregate([
    {$match: {from: "Winterfell"}}
])
```

And would result in:

```json
{
    "name": "Jon Snow",
    "from": "Winterfell",
    "walkersKilled": 32
}
{
    "name": "Benjen Stark",
    "from": "Winterfell",
    "walkersKilled": 89
}
```

##### Group
The `$group` stage allows to group multiple documents according to some criterion. This is useful to evaluate averages, sums, max, etc. as aggregation fields.

For instance, in reference to the previous dataset, if you wanted to find the white walkers killed by each house:

```javascript
db.nightwatch.aggregate([
    {$group: {_id: "$house", kills: {$sum: "$walkersKilled"}}}
])
```

And would output:

```json
{
    "_id": "Stark",
    "kills": 121
}
{
    "_id": "Mormont",
    "kills": 15
}
{
    "_id": "Tarly",
    "kills": 1
}
```

Similar methods could be used to extract other statistics.

##### Project
Project allows to reshape each document in the collection. It is possible to compute new fields, hide and create new ones.

For instance, if you wanted to reshape the results of the previous query so that the `_id` field would me named `house`, you would do so: 

```javascript
db.nightwatch.aggregate([
    {$group: {_id: "$house", kills: {$sum: "$walkersKilled"}}},
    {$project: {_id: 0, house: "$_id", kills: 1}}
])
```

Resulting in:

```json
{
    "house": "Stark",
    "kills": 121
}
{
    "house": "Mormont",
    "kills": 15
}
{
    "house": "Tarly",
    "kills": 1
}
```

Note how setting a field to `0` will hide it, while `1` would reveal it.

##### Unwind
The `$unwind` operator allows to deconstruct an array field merging it with the parent document, creating a new document for each element in the array.

Consider the following document:

```json
{
    "name": "Daenerys Targaryen",
    "title": [
        "Queen",
        "Stormborn",
        "of the House Targaryen", 
        "the First of Her Name", 
        "Queen of the Andals, the Rhoynar and the First Men", 
        "Lady of the Seven Kingdoms",
        "Protector of the Realm"
    ]
}
```

Unwinding it on the `title` attribute with:

```javascript
db.nightwatch.aggregate([
   {$unwind: "$title"}
])
```

Would result in:

```json
{
    "name": "Daenerys Targaryen",
    "title": "Queen"
}
{
    "name": "Daenerys Targaryen",
    "title": "Stormborn"
}
{...}
{
    "name": "Daenerys Targaryen",
    "title": "Protector of the Realm"
}
```

##### Sort
The `$sort` operator allows to sort a collection of documents according to their fields. 

For instance, if we wanted to sort the titles used in the example above in ascending order:

```javascript
db.nightwatch.aggregate([
   {$unwind: "$title"},
   {$sort: {title: 1}}
])
```

More than one field can be specified, and `-1` can be used to sort in descending order.

#### Map Reduce
The `mapReduce` operation allows to aggregate data using JavaScript functions, allowing a greater deal of flexibility, compared to the `aggregate` operation, at the cost of slower performances and a less consistent syntax.

The method operates with three parameters `map`, `reduce` and `options` (optional).

The following is a rather useless example:

```javascript
db.<collection>.mapReduce(
	 // map
    function() {
        emit(this.field_to_aggregate_on, this.values);
    },
    // reduce
    function(key, values) {
        return Array.sum(values); // aggregate the values
    },
    // options
    { 
        query: {ciao: "ciao"} // this just filters the collection before mapping
    }
)
```

#### Single purpose aggregation methods
There are a few built-in methods for aggregating with common functions. This allow to quickly create aggregations without writing too much code.

Examples are:

- `db.<collection>.count(<query>, <options>)` – returns the number of documents matching the query in the collection.
- `db.<collection>.estimatedDocumentCount(<options>)` – returns the number of documents in the collection.
- `db.<collection>.distinct(<field>, <query>, <options>)` – returns the distinct values of a specific field given a query in the the collection.

For the Night Watch example used earlier, if you wanted to only select the names of the unique houses you would:

```javascript
db.nightwatch.distinct("house")
```

Resulting in:

```json
[
	"Stark",
	"Mormont",
	"Tarly
]
```

### Consistency
As we discussed earlier, MongoDB has a primary-secondary replication structure (guaranteeing fault-tolerance). Writes operations are only made on the primary node, and are propagated to the secondary over time. If the primary node fails, a new primary node is elected, but what if there was a non-replicated changes when the primary node failed and now it is brought back online? There will be conflicting changes that will require to be resolved.

**Read consistency** – two readers see the same data at the same time after an update.
**Write consistency** – only one writer is allowed to write at a time.

In MongoDB each document is treated atomically, so only one **transaction** at a time can happen, then only providing consistency within one replica.

NoSQL databases usually do not give **ACID** constraints, and it strongly depends on the application. For most of the purposes, **strong consistency** is not needed. Most of of the NoSQL stores rely on **eventual consistency**.

In MongoDB it is possible, at query time, to choose the level of needed consistency, i.e. whether it is okay to retrieve slightly stale data, in favour of better performances.

A distributed database can have only have two of the following:

- **Strong consistency** – all nodes see the same data at the same time.
- **Availability** – every client gets a response, regardless if one of the nodes is down.
- **Partition tolerance** – the system continues to run regardless of the of number of delayed messages.

### Indexes
Indexes are used in databases to improve the efficiency of read operations

In MongoDB indexes are defined at the _collection_ level, and is supported on any field or subfield of the documents. 

B-Trees (which are **not** binary trees) are used to create indexes. Differently from binary trees, they can have more than two children at each node, and they perform all operations in `O(log n)` time.

![b-tree](https://nptel.ac.in/courses/Webcourse-contents/IIT-%20Guwahati/data_str_algo/b%20tree/btreeh.gif)


Indexes can be of three types:

- **default \_id** – the automatically created \_id field is by default indexed.
- **single field** – a user defined index on a single field or subfield.
- **compound index** – sorts according to multiple fields, in the specified order.
- **multikey index** – sorts according arrays content.
- **geospatial index** – efficiently sorts geospatial data (coordinates).
- **text index** – sorts long strings of text ignoring stop words.
- **hashed index** – indexes the hash of a field.

It is possible to create an index on a field with:

```javascript
db.<collection>.createIndex({<field>: <1 or -1>})
```

`1` or `-1` can be used to specify whether the index is going to be ascending or descending.

To add a compound index, simply add more fields to the object:

```javascript
db.<collection>.createIndex({
    <field1>: <1 or -1>,
    <field2>: <1 or -1>,
    ...
    <fieldN>: <1 or -1>
})
```

Indexes can have the following properties:

- **unique** – reject duplicate data for the same indexed field value.
- **sparse** – only index entries that have the index field(s).
- **non-sparse** – index all documents, using `null` for those that don't have the index field.

### Data Models


### Cursor and Projections


## Other NoSQL databases
### Column Family
### Graph Databases
