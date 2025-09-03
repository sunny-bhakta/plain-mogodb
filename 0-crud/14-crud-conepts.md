# MongoDB CRUD Example

## Sample Collection: `users`

| _id         | name    | email              | age |
|-------------|---------|--------------------|-----|
| 1           | Alice   | alice@email.com    | 25  |
| 2           | Bob     | bob@email.com      | 30  |

## Create

```js
db.users.insertOne({
    name: "Charlie",
    email: "charlie@email.com",
    age: 28
});
```

## Read

```js
db.users.find({ age: { $gt: 25 } });
```

## Update

```js
db.users.updateOne(
    { name: "Alice" },
    { $set: { age: 26 } }
);
```

## Delete

```js
db.users.deleteOne({ name: "Bob" });
```

## `db.collection.insertOne()`

Inserts a single document into a collection.

```js
db.users.insertOne({
    name: "David",
    email: "david@email.com",
    age: 32
});
```

## `db.collection.insertMany()`

Inserts multiple documents into a collection.

```js
db.users.insertMany([
    {
        name: "Eve",
        email: "eve@email.com",
        age: 27
    },
    {
        name: "Frank",
        email: "frank@email.com",
        age: 29
    }
]);
```

# MongoDB CRUD Concepts – Definition & Examples

This section covers additional concepts related to CRUD operations in MongoDB, including atomicity, consistency, distributed queries, query plans, and more.

---

## Atomicity, Consistency, and Distributed Operations

**Atomicity:**  
A single write operation (insert, update, delete) on a single document is atomic in MongoDB.  
**Example:**  
```js
db.users.updateOne({ name: "Alice" }, { $set: { age: 26 } })
```
This update is atomic; either the age is updated or not, never partially.

**Consistency:**  
MongoDB ensures consistency at the document level. For stronger consistency across multiple documents, use transactions.

**Distributed Operations:**  
MongoDB supports sharding and replica sets for distributed data and high availability.

---

## Atomicity and Transactions

**Transactions:**  
Allow multiple operations on one or more documents to be executed atomically.
**Example:**  
```js
const session = db.getMongo().startSession();
session.startTransaction();
try {
  db.users.updateOne({ name: "Alice" }, { $set: { age: 27 } }, { session });
  db.users.deleteOne({ name: "Bob" }, { session });
  session.commitTransaction();
} catch (e) {
  session.abortTransaction();
}
session.endSession();
```

---

## Read Isolation, Consistency, and Recency

**Read Isolation:**  
Controls how "fresh" and consistent the data returned by a query is.  
**Example:**  
```js
db.users.find({}, { readConcern: { level: "majority" } })
```

---

## Distributed Queries

**Definition:**  
Queries that run across sharded clusters or replica sets.  
**Example:**  
```js
db.users.find({ age: { $gt: 25 } })
```
If `users` is sharded, this query runs on all relevant shards.

---

## Query Plan, Performance, Analysis, and Query Shape

**Query Plans:**  
MongoDB analyzes queries and chooses the most efficient plan.
**Example:**  
```js
db.users.find({ age: 25 }).explain("executionStats")
```

**Query Optimization:**  
Use indexes to improve query performance.
**Example:**  
```js
db.users.createIndex({ age: 1 })
```

**Write Operation Performance:**  
Bulk writes and indexed writes are faster and more efficient.

**Query Shapes:**  
The structure of a query (fields, operators) affects performance and index usage.

---

## Miscellaneous

**Tailable Cursors:**  
Used for capped collections to stream new data as it arrives.
**Example:**  
```js
db.logs.find({}, { tailable: true })
```

**Field Names with Periods and Dollar Signs:**  
Avoid using `.` or `$` in field names, as they have special meaning in MongoDB.

---

## Tip: Transactions

Use transactions for multi-document atomic operations, especially in financial or critical applications.

---

**Next:**  
Atomicity &