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

### Aggregation Pipeline
### Consistency
### Map Reduce
### Indexes
### Data Models
### Cursor and Projections

## Other NoSQL databases
### Column Family
### Graph Databases
