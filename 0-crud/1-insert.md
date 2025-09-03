| Operation                | Method/Command                                         | Example                                                                                   |
|--------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------|
| Insert One Document      | `insertOne`                                           | `db.collection.insertOne({ name: "Alice", age: 25 })`                                     |
| Insert Many Documents    | `insertMany`                                          | `db.collection.insertMany([{ name: "Bob" }, { name: "Carol" }])`                          |
| Bulk Insert              | `bulkWrite`                                           | `db.collection.bulkWrite([{ insertOne: { document: { name: "Dave", age: 30 } } }])`       |
| Find All Documents       | `find`                                                | `db.collection.find({})`                                                                  |
| Find One Document        | `findOne`                                             | `db.collection.findOne({ name: "Alice" })`                                                |
| Update One Document      | `updateOne`                                           | `db.collection.updateOne({ name: "Alice" }, { $set: { age: 26 } }, { upsert: true })`     |
| Update Many Documents    | `updateMany`                                          | `db.collection.updateMany({ age: { $lt: 30 } }, { $set: { active: true } }, { upsert: true })` |
| Find and Modify          | `findAndModify`                                       | `db.collection.findAndModify({ query: { name: "Bob" }, update: { $set: { age: 28 } }, upsert: true })` |
| Find One and Update      | `findOneAndUpdate`                                    | `db.collection.findOneAndUpdate({ name: "Carol" }, { $set: { age: 29 } }, { upsert: true })` |
| Find One and Replace     | `findOneAndReplace`                                   | `db.collection.findOneAndReplace({ name: "Eve" }, { name: "Eve", age: 23 }, { upsert: true })` |
| Bulk Write (Update/Delete)| `bulkWrite`                                          | `db.collection.bulkWrite([{ updateOne: { filter: { name: "Frank" }, update: { $set: { age: 31 } }, upsert: true } }, { deleteMany: { filter: { age: { $lt: 20 } } } }])` |

**Example Usage**

```js
// Insert one document
db.collection.insertOne({ name: "Alice", age: 25 })

// Insert many documents
db.collection.insertMany([{ name: "Bob" }, { name: "Carol" }])

// Bulk insert operations
db.collection.bulkWrite([
    { insertOne: { document: { name: "Dave", age: 30 } } },
    { insertOne: { document: { name: "Eve", age: 22 } } }
])

// Find all documents
db.collection.find({})

// Find one document
db.collection.findOne({ name: "Alice" })

// Update one document, insert if not exists
db.collection.updateOne(
    { name: "Alice" },
    { $set: { age: 26 } },
    { upsert: true }
)

// Update many documents, insert if not exists
db.collection.updateMany(
    { age: { $lt: 30 } },
    { $set: { active: true } },
    { upsert: true }
)

// Find and modify, insert if not exists
db.collection.findAndModify({
    query: { name: "Bob" },
    update: { $set: { age: 28 } },
    upsert: true
})

// Find one and update, insert if not exists
db.collection.findOneAndUpdate(
    { name: "Carol" },
    { $set: { age: 29 } },
    { upsert: true }
)

// Find one and replace, insert if not exists
db.collection.findOneAndReplace(
    { name: "Eve" },
    { name: "Eve", age: 23 },
    { upsert: true }
)

// Bulk write with upsert and delete
db.collection.bulkWrite([
    { updateOne: {
        filter: { name: "Frank" },
        update: { $set: { age: 31 } },
        upsert: true
    }},
    { deleteMany: { filter: { age: { $lt: 20 } } } }
])
```