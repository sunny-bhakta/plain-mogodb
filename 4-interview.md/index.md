# MongoDB Interview Questions and Answers

## Beginner Level

### 1. What is MongoDB?
MongoDB is a NoSQL, document-oriented database that stores data in JSON-like BSON format.

### 2. How do you insert a document in MongoDB?
```js
db.users.insertOne({ name: "Alice", age: 25 })
```

### 3. How do you find documents in a collection?
```js
db.users.find({ age: { $gt: 20 } })
```

### 4. What is a collection in MongoDB?
A collection is a group of MongoDB documents, similar to a table in relational databases.

---

## Intermediate Level

### 5. How do you update a document?
```js
db.users.updateOne(
    { name: "Alice" },
    { $set: { age: 26 } }
)
```

### 6. What is an index? How do you create one?
An index improves query performance.
```js
db.users.createIndex({ age: 1 })
```

### 7. How do you delete documents?
```js
db.users.deleteMany({ age: { $lt: 18 } })
```

### 8. What is aggregation in MongoDB?
Aggregation processes data records and returns computed results.
```js
db.orders.aggregate([
    { $group: { _id: "$customerId", total: { $sum: "$amount" } } }
])
```

---

## Advanced Level

### 9. Explain replica sets in MongoDB.
Replica sets provide redundancy and high availability by replicating data across multiple servers.

### 10. How do you perform transactions in MongoDB?
```js
const session = db.getMongo().startSession()
session.startTransaction()
try {
    db.users.insertOne({ name: "Bob" }, { session })
    db.accounts.updateOne({ user: "Bob" }, { $inc: { balance: 100 } }, { session })
    session.commitTransaction()
} catch (e) {
    session.abortTransaction()
}
session.endSession()
```

### 11. What is sharding?
Sharding is a method for distributing data across multiple machines to support large datasets.

### 12. How do you handle schema validation?
```js
db.createCollection("products", {
    validator: {
        $jsonSchema: {
            bsonType: "object",
            required: ["name", "price"],
            properties: {
                name: { bsonType: "string" },
                price: { bsonType: "number" }
            }
        }
    }
})
```

---

## Expert Level

### 13. How do you optimize query performance?
- Use indexes
- Avoid large documents
- Use projection to limit returned fields

### 14. How do you monitor MongoDB performance?
Use `mongostat`, `mongotop`, and MongoDB Atlas monitoring tools.

### 15. How do you handle data migration in MongoDB?
Use `mongoexport` and `mongoimport` for data transfer, or use aggregation pipelines for transformation.

---

## Example: Complex Aggregation Pipeline

```js
db.sales.aggregate([
    { $match: { status: "completed" } },
    { $group: { _id: "$productId", totalSales: { $sum: "$amount" } } },
    { $sort: { totalSales: -1 } }
])
```

### 16. What is the difference between `find()` and `findOne()`?
`find()` returns a cursor to all matching documents, while `findOne()` returns the first matching document.

### 17. How do you perform bulk write operations?
Use `bulkWrite()` to execute multiple write operations in a single call.
```js
db.users.bulkWrite([
    { insertOne: { document: { name: "Charlie", age: 30 } } },
    { updateOne: { filter: { name: "Alice" }, update: { $set: { age: 27 } } } }
])
```

### 18. How do you ensure data consistency in MongoDB?
Use transactions for multi-document operations and replica sets for high availability.

### 19. What is the purpose of the `_id` field?
The `_id` field uniquely identifies each document in a collection and is automatically indexed.

### 20. How do you perform text search in MongoDB?
Create a text index and use the `$text` operator.
```js
db.articles.createIndex({ content: "text" })
db.articles.find({ $text: { $search: "MongoDB" } })
```

### 21. How do you handle relationships between documents in MongoDB?
Use references (storing ObjectId of related documents) or embedding documents, depending on the use case.

### 22. What is the difference between capped and uncapped collections?
Capped collections have a fixed size and maintain insertion order; old documents are overwritten when the size limit is reached. Uncapped collections have no such restrictions.

### 23. How do you perform geospatial queries in MongoDB?
Create a geospatial index and use operators like `$near` and `$geoWithin`.
```js
db.places.createIndex({ location: "2dsphere" })
db.places.find({ location: { $near: { $geometry: { type: "Point", coordinates: [40, 5] } } } })
```

### 24. What is the WiredTiger storage engine?
WiredTiger is the default storage engine for MongoDB, offering compression and improved concurrency.

### 25. How do you backup and restore MongoDB data?
Use `mongodump` to create backups and `mongorestore` to restore data.


### 26. How do you enforce unique values in a field?
Create a unique index on the field.
```js
db.users.createIndex({ email: 1 }, { unique: true })
```

### 27. What is the purpose of the `$lookup` stage in aggregation?
`$lookup` performs left outer joins to combine documents from different collections.
```js
db.orders.aggregate([
    {
        $lookup: {
            from: "users",
            localField: "userId",
            foreignField: "_id",
            as: "userDetails"
        }
    }
])
```

### 28. How do you handle large file storage in MongoDB?
Use GridFS to store and retrieve large files such as images, audio, and video.

### 29. What is TTL index and how is it used?
TTL (Time-To-Live) index automatically removes documents after a specified period.
```js
db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })
```

### 30. How do you secure MongoDB deployments?
- Enable authentication and authorization
- Use TLS/SSL for encrypted connections
- Restrict network access with firewalls
- Regularly update MongoDB to the latest version