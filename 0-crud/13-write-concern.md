# Write Concern in MongoDB

**Definition:**  
Write concern in MongoDB determines the level of acknowledgment requested from MongoDB for write operations (insert, update, delete). It specifies how many replica set members must confirm the write before it is considered successful.

## Example

```js
// Insert a document with write concern "majority"
db.collection.insertOne(
    { name: "Alice", age: 30 },
    { writeConcern: { w: "majority", wtimeout: 5000 } }
)
```

- `w: "majority"`: The write must be acknowledged by the majority of replica set members.
- `wtimeout: 5000`: The write operation will timeout after 5000 milliseconds if the condition is not met.

## Common Write Concern Levels

- `w: 1` — Acknowledged by the primary only (default).
- `w: "majority"` — Acknowledged by the majority of replica set members.
- `w: 0` — No acknowledgment (unacknowledged write).

**Note:** Higher write concern increases data safety but may impact performance.
