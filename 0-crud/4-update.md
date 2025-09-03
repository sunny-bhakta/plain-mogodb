# MongoDB Update Methods with Examples

## MongoDB Update Methods Overview

| Method                              | Description                                      |
|--------------------------------------|--------------------------------------------------|
| `db.collection.updateOne()`          | Updates a single document matching the filter.   |
| `db.collection.updateMany()`         | Updates all documents matching the filter.        |
| `db.collection.findOneAndUpdate()`   | Finds and updates a document, returns doc.        |
| `db.collection.findAndModify()`      | Finds and modifies a document (legacy).           |
| `Bulk.find.update()`                 | Bulk updates documents found by the operation.    |
| `Bulk.find.updateOne()`              | Bulk updates a single document found.             |
| `Bulk.find.upsert()`                 | Updates if found, inserts if not.                 |

---

## Examples

### 1. `db.collection.updateOne()`
```js
db.collection.updateOne(
    { name: "Alice" },
    { $set: { age: 30 } }
)
```

### 2. `db.collection.updateMany()`
```js
db.collection.updateMany(
    { status: "active" },
    { $set: { verified: true } }
)
```

### 3. `db.collection.findOneAndUpdate()`
```js
db.collection.findOneAndUpdate(
    { name: "Charlie" },
    { $inc: { score: 5 } },
    { returnDocument: "after" }
)
```

### 4. `db.collection.findAndModify()`
```js
db.collection.findAndModify({
    query: { name: "Eve" },
    update: { $set: { age: 35 } },
    new: true
})
```

### 5. `Bulk.find.update()`
```js
bulk.find({ status: "pending" }).update({ $set: { status: "complete" } })
```

### 6. `Bulk.find.updateOne()`
```js
bulk.find({ name: "Frank" }).updateOne({ $set: { age: 28 } })
```

### 7. `Bulk.find.upsert()`
```js
bulk.find({ name: "Grace" }).upsert().update({ $set: { age: 32 } })
```
 
### 8. Update a Specific Array Element Using `$` and `$elemMatch`

```js
db.collection.updateOne(
    { scores: { $elemMatch: { type: "math", value: { $lt: 50 } } } },
    { $set: { "scores.$.value": 75 } }
)
```

**Explanation:**  
This updates the `value` field of the first `scores` array element where `type` is `"math"` and `value` is less than `50`.