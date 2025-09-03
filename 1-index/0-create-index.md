# Index and Types in Databases

## What is an Index?
An **index** is a data structure that improves the speed of data retrieval operations on a database table at the cost of additional writes and storage space. Indexes are used to quickly locate data without having to search every row in a database table.

## Types of Indexes

### 1. Single Field Index
- Created on a single field/column.
- Speeds up queries filtering or sorting by that field.

### 2. Compound Index
- Created on multiple fields/columns.
- Useful for queries that filter or sort by more than one field.

### 3. Unique Index
- Ensures all values in the indexed field(s) are unique.
- Prevents duplicate values.

### 4. Text Index
- Supports text search queries.
- Used for searching words or phrases in string fields.

### 5. Geospatial Index
- Supports location-based queries.
- Used for storing and querying coordinates.

### 6. Hashed Index
- Indexes the hash of the field value.
- Useful for equality queries.

---

Indexes are essential for optimizing query performance, but they require careful planning to balance speed and resource usage.

# Indexing Reference

## Indexing Methods in `mongosh`

| Name | Description |
|------|-------------|
| [`db.collection.createIndex()`](https://mongodb.com/docs/manual/reference/method/db.collection.createIndex/#mongodb-method-db.collection.createIndex) | Builds an index on a collection. |
| [`db.collection.dropIndex()`](https://mongodb.com/docs/manual/reference/method/db.collection.dropIndex/#mongodb-method-db.collection.dropIndex) | Removes a specified index on a collection. |
| [`db.collection.dropIndexes()`](https://mongodb.com/docs/manual/reference/method/db.collection.dropIndexes/#mongodb-method-db.collection.dropIndexes) | Removes all indexes on a collection. |
| [`db.collection.getIndexes()`](https://mongodb.com/docs/manual/reference/method/db.collection.getIndexes/#mongodb-method-db.collection.getIndexes) | Returns an array of documents that describe the existing indexes on a collection. |
| [`db.collection.reIndex()`](https://mongodb.com/docs/manual/reference/method/db.collection.reIndex/#mongodb-method-db.collection.reIndex) | Rebuilds all existing indexes on a collection. |
| [`db.collection.totalIndexSize()`](https://mongodb.com/docs/manual/reference/method/db.collection.totalIndexSize/#mongodb-method-db.collection.totalIndexSize) | Reports the total size used by the indexes on a collection. |
| [`cursor.explain()`](https://mongodb.com/docs/manual/reference/method/cursor.explain/#mongodb-method-cursor.explain) | Reports on the query execution plan for a cursor. |
| [`cursor.hint()`](https://mongodb.com/docs/manual/reference/method/cursor.hint/#mongodb-method-cursor.hint) | Forces MongoDB to use a specific index for a query. |
| [`cursor.max()`](https://mongodb.com/docs/manual/reference/method/cursor.max/#mongodb-method-cursor.max) | Specifies an exclusive upper index bound for a cursor. For use with `cursor.hint()`. |
| [`cursor.min()`](https://mongodb.com/docs/manual/reference/method/cursor.min/#mongodb-method-cursor.min) | Specifies an inclusive lower index bound for a cursor. For use with `cursor.hint()`. |

---

## Indexing Database Commands

| Name | Description |
|------|-------------|
| [`createIndexes`](https://mongodb.com/docs/manual/reference/command/createIndexes/#mongodb-dbcommand-dbcmd.createIndexes) | Builds one or more indexes for a collection. |
| [`dropIndexes`](https://mongodb.com/docs/manual/reference/command/dropIndexes/#mongodb-dbcommand-dbcmd.dropIndexes) | Removes indexes from a collection. |
| [`compact`](https://mongodb.com/docs/manual/reference/command/compact/#mongodb-dbcommand-dbcmd.compact) | Defragments a collection and rebuilds the indexes. |
| [`reIndex`](https://mongodb.com/docs/manual/reference/command/reIndex/#mongodb-dbcommand-dbcmd.reIndex) | Rebuilds all indexes on a collection. |
| [`validate`](https://mongodb.com/docs/manual/reference/command/validate/#mongodb-dbcommand-dbcmd.validate) | Internal command that scans for a collection's data and indexes for correctness. |
| [`setIndexCommitQuorum`](https://mongodb.com/docs/manual/reference/command/setIndexCommitQuorum/#mongodb-dbcommand-dbcmd.setIndexCommitQuorum) | Changes the minimum number of data-bearing members (commit quorum) that must vote to commit an in-progress index build before the primary marks those indexes as ready. |

---

## Geospatial Query Selectors

| Name | Description |
|------|-------------|
| [`$geoWithin`](https://mongodb.com/docs/manual/reference/operator/query/geoWithin/#mongodb-query-op.-geoWithin) | Selects geometries within a bounding GeoJSON geometry. Supported by 2dsphere and 2d indexes. |
| [`$geoIntersects`](https://mongodb.com/docs/manual/reference/operator/query/geoIntersects/#mongodb-query-op.-geoIntersects) | Selects geometries that intersect with a GeoJSON geometry. Supported by 2dsphere index. |
| [`$near`](https://mongodb.com/docs/manual/reference/operator/query/near/#mongodb-query-op.-near) | Returns geospatial objects in proximity to a point. Requires a geospatial index. Supported by 2dsphere and 2d indexes. |
| [`$nearSphere`](https://mongodb.com/docs/manual/reference/operator/query/nearSphere/#mongodb-query-op.-nearSphere) | Returns geospatial objects in proximity to a point on a sphere. Requires a geospatial index. Supported by 2dsphere and 2d indexes. |


# Create an Index

Indexes in MongoDB make queries faster by allowing the database to quickly locate data. If you frequently query the same fields, creating an index on those fields improves performance. However, indexes can slow down write operations because each insert or update must also update the index.

---

## How to Create an Index

### MongoDB Shell Example

```javascript
db.collection.createIndex({ name: 1 })
```
Creates an ascending index on the `name` field.

**Example query using the index:**
```javascript
db.collection.find({ name: "Alice" })
```

### Node.js Example

```javascript
collection.createIndex({ name: 1 }, function(err, result) {
    console.log(result);
    callback(result);
})
```

## How Index Creation Works

- MongoDB only creates the index if it doesn't already exist with the same specification.
- You can specify options like index type, uniqueness, and name.

---

## View Existing Indexes

To see all indexes on a collection:

```javascript
db.collection.getIndexes()
```

**Sample Output:**
```javascript
[
    { v: 2, key: { _id: 1 }, name: '_id_' },
    { v: 2, key: { name: 1 }, name: 'name_1' }
]
```

---

## Monitor Index Creation

To check if an index is being built, use the `$currentOp` aggregation stage:

```javascript
db.getSiblingDB("admin").aggregate([
    { $currentOp: { idleConnections: true } },
    { $match: { "command.createIndexes": { $exists: true } } }
])
```

This returns information about active index builds.

---

## Performance Note

- **Read-heavy collections:** Indexes improve query speed.
- **Write-heavy collections:** Indexes slow down writes.

---

## Drop Indexes

To remove an index from a collection:

### Drop a Specific Index

```javascript
db.collection.dropIndex("name_1")
```
Drops the index named `name_1` from the collection.

### Drop All Indexes

```javascript
db.collection.dropIndexes()
```
Removes all indexes except for the default `_id` index.

---

## Index Types in MongoDB

MongoDB supports several index types, each optimized for different query patterns:

### 1. Single Field Index

Indexes a single field in a collection.

```javascript
db.collection.createIndex({ field: 1 })
```
- `1` for ascending, `-1` for descending order.

**Example query:**
```javascript
db.collection.find({ field: "value" })
```

### 2. Compound Index

Indexes multiple fields in a single index.

```javascript
db.collection.createIndex({ field1: 1, field2: -1 })
```
- Useful for queries involving multiple fields.

**Example query:**
```javascript
db.collection.find({ field1: "A", field2: "B" })
```

### 3. Multikey Index

Automatically created for fields that hold arrays.

```javascript
db.collection.createIndex({ tags: 1 })
```
- Supports efficient queries on array values.

**Example query:**
```javascript
db.collection.find({ tags: "mongodb" })
```

### 4. Text Index

Supports text search on string content.

```javascript
db.collection.createIndex({ description: "text" })
```
- Enables `$text` queries for searching words and phrases.

**Example query:**
```javascript
db.collection.find({ $text: { $search: "fast queries" } })
```

### 5. Hashed Index

Indexes the hash of a field’s value.

```javascript
db.collection.createIndex({ user_id: "hashed" })
```
- Useful for sharding and equality queries.

**Example query:**
```javascript
db.collection.find({ user_id: "12345" })
```

### 6. Geospatial Index

Supports location-based queries.

- **2dsphere Index:** For spherical geometry (Earth-like coordinates).
     ```javascript
     db.collection.createIndex({ location: "2dsphere" })
     ```
     **Example query:**
     ```javascript
     db.collection.find({
        location: {
          $near: {
             $geometry: { type: "Point", coordinates: [40, -73] },
             $maxDistance: 1000
          }
        }
     })
     ```
- **2d Index:** For flat geometry.
     ```javascript
     db.collection.createIndex({ coordinates: "2d" })
     ```
     **Example query:**
     ```javascript
     db.collection.find({
        coordinates: {
          $near: [40, -73]
        }
     })
     ```

### 7. Unique Index

Ensures all values in the indexed field are unique.

```javascript
db.collection.createIndex({ email: 1 }, { unique: true })
```
- Prevents duplicate values.

**Example query:**
```javascript
db.collection.find({ email: "user@example.com" })
```

---

## Additional Index Types

### Case-Insensitive Indexes

MongoDB supports case-insensitive indexes using collation.

```javascript
db.collection.createIndex(
  { name: 1 },
  { collation: { locale: "en", strength: 2 } }
)
```
- Queries using the same collation will be case-insensitive.

**Example query:**
```javascript
db.collection.find(
  { name: "alice" },
  { collation: { locale: "en", strength: 2 } }
)
```

### Hidden Indexes

Hidden indexes exist on the collection but are not used by the query planner.

```javascript
db.collection.createIndex({ age: 1 }, { hidden: true })
```
- Useful for testing index impact without dropping it.

**Unhide an index:**
```javascript
db.collection.unhideIndex("age_1")
```

### Partial Indexes

Indexes only documents that match a filter expression.

```javascript
db.collection.createIndex(
  { status: 1 },
  { partialFilterExpression: { status: { $eq: "active" } } }
)
```
- Reduces index size and improves write performance.

### Sparse Indexes

Indexes only documents that contain the indexed field.

```javascript
db.collection.createIndex({ phone: 1 }, { sparse: true })
```
- Documents without the field are not indexed.

### TTL (Time-To-Live) Indexes

Automatically removes documents after a specified period.

```javascript
db.collection.createIndex(
  { createdAt: 1 },
  { expireAfterSeconds: 3600 }
)
```
- Useful for expiring session or log data.

### Unique Indexes

Ensures all values in the indexed field are unique.

```javascript
db.collection.createIndex({ username: 1 }, { unique: true })
```
- Prevents duplicate entries.

---

**Choosing the right index type depends on your query patterns and data structure.**

## Index Properties

When creating indexes in MongoDB, you can specify various properties to control their behavior:

### Common Index Properties

- **unique:** Ensures all values in the indexed field are unique.
- **name:** Sets a custom name for the index.
- **sparse:** Only indexes documents that contain the indexed field.
- **expireAfterSeconds:** Sets a TTL (Time-To-Live) for documents in the index.
- **background:** Builds the index in the background without blocking operations.
- **collation:** Enables case-insensitive or locale-aware sorting.
- **hidden:** Hides the index from the query planner.
- **partialFilterExpression:** Indexes only documents matching the filter.

### Example: Creating an Index with Properties

```javascript
db.collection.createIndex(
    { email: 1 },
    {
        unique: true,
        name: "email_unique_idx",
        sparse: true,
        background: true
    }
)
```

This creates a unique, sparse index on the `email` field, names it `email_unique_idx`, and builds it in the background.

**Reference:** [MongoDB createIndex documentation](https://www.mongodb.com/docs/manual/reference/method/db.collection.createIndex/)


## Index Builds on Populated Collections

When creating an index on a collection with existing data, MongoDB builds the index in the background by default (unless specified otherwise). This allows read and write operations to continue during the build, minimizing disruption.

### Considerations

- **Performance Impact:** Building an index on a large collection can temporarily increase CPU and disk usage.
- **Background Builds:** Use the `background: true` option to avoid blocking operations (MongoDB 4.2+ builds all indexes in the background by default).
- **Locking:** On older MongoDB versions, foreground index builds may lock the collection.

### Example: Background Index Build

```javascript
db.collection.createIndex({ status: 1 }, { background: true })
```

### Monitoring Progress

You can monitor index build progress using the `$currentOp` aggregation stage or by checking the logs.

**Tip:** For very large collections, consider building indexes during off-peak hours to reduce impact on application performance.


## Measure Index Use

### Get Index Access Information with `$indexStats`

MongoDB provides the `$indexStats` aggregation stage to report usage statistics for each index on a collection.

```javascript
db.collection.aggregate([ { $indexStats: {} } ])
```

**Sample Output:**
```javascript
[
    {
        name: "name_1",
        accesses: { ops: 120, since: "2024-06-01T10:00:00Z" }
    },
    {
        name: "_id_",
        accesses: { ops: 500, since: "2024-06-01T10:00:00Z" }
    }
]
```
- `ops`: Number of operations using the index.
- `since`: Timestamp when stats started collecting.

Use this information to identify unused indexes and optimize your indexing strategy.

---

## Indexing Strategies

- **Analyze Query Patterns:** Create indexes for fields frequently used in queries, sorting, or filtering.
- **Limit Number of Indexes:** Too many indexes can slow down writes and increase storage requirements.
- **Remove Unused Indexes:** Use `$indexStats` to find and drop indexes that are rarely accessed.
- **Use Compound Indexes:** Combine multiple fields in a single index for queries involving several fields.
- **Leverage Partial and Sparse Indexes:** Optimize for specific subsets of documents or fields.
- **Monitor Performance:** Regularly review query execution plans and index usage to ensure optimal performance.

---

