# MongoDB Retryable Write Operations

**Definition:**  
Retryable writes are write operations (insert, update, delete, replace) that MongoDB can automatically retry once in case of transient network errors or primary failover. This helps ensure reliability and consistency for applications.

---

## Example: Retryable Insert

```js
db.collection.insertOne(
  { name: "Bob", age: 30 },
  { writeConcern: { w: "majority" }, retryWrites: true }
)
```

---

## Example: Retryable Update

```js
db.collection.updateOne(
  { name: "Bob" },
  { $set: { age: 31 } },
  { writeConcern: { w: "majority" }, retryWrites: true }
)
```

---

## Example: Retryable Delete

```js
db.collection.deleteOne(
  { name: "Bob" },
  { writeConcern: { w: "majority" }, retryWrites: true }
)
```

---

**Note:**  
- Retryable writes are enabled by default in most MongoDB drivers.
- The server must be a replica set or sharded cluster for retryable writes
---
### What Does `w: "majority"` Mean?

The `w: "majority"` option in MongoDB's write concern ensures that a write operation is only considered successful when the data has been written to most members of the replica set.

**Why use it?**
- Increases data durability and consistency.
- Reduces risk of data loss if some nodes fail.

**Example:**  
In a replica set with 5 nodes, `"majority"` means at least 3 nodes must acknowledge the write.

**Caution:**  
Using `"majority"` may slow down writes if some nodes are lagging or unavailable, but it significantly improves reliability for important data.


