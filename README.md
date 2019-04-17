# NoSQL Databases

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
  "name":"John",
  "age":30,
  "cars": {
    "car1":"Ford",
    "car2":"BMW",
    "car3":"Fiat"
  }
 }
```

## MongoDB
### Distribution Model
### Aggregation Pipeline
### Consistency
### Map Reduce
### Indexes
### Data Models
### Cursor and Projections

## Other NoSQL databases
### Column Family
### Graph Databases
