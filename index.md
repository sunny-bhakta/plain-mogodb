# Documents in MongoDB

MongoDB stores data as **documents** within collections. Each document is a BSON (Binary JSON) object, which is similar to JSON but supports more data types like binary and date.

## Example Document

```json
{
    "_id": "507f1f77bcf86cd799439011",
    "name": "John Doe",
    "email": "john.doe@example.com",
    "age": 30,
    "isActive": true
}
```

### Key Points

- Documents are flexible and can have varying fields.
- Every document has a unique `_id` field.
- Documents are organized in **collections**.

### Resources

- [MongoDB Documents Documentation](https://www.mongodb.com/docs/manual/core/document/)

## JSON vs BSON

**JSON** is a text-based format; **BSON** is a binary format used internally by MongoDB.

#### JSON Example

```json
{
    "name": "Alice",
    "age": 25,
    "isMember": false
}
```

#### BSON Example (conceptual)

| Key      | Type   | Value   |
|----------|--------|---------|
| name     | String | Alice   |
| age      | Int32  | 25      |
| isMember | Bool   | false   |

> **Note:** BSON supports additional types (e.g., `Date`, `Binary`) not available in JSON.

- [BSON Specification](https://bsonspec.org/)

---

# MongoDB CRUD Example

## Sample Collection: `users`

| _id | name  | email            | age |
|-----|-------|------------------|-----|
| 1   | Alice | alice@email.com  | 25  |
| 2   | Bob   | bob@email.com    | 30  |

### Create

```js
db.users.insertOne({
    name: "Charlie",
    email: "charlie@email.com",
    age: 28
});
```

### Read

```js
db.users.find({ age: { $gt: 25 } });
```

### Update

```js
db.users.updateOne(
    { name: "Alice" },
    { $set: { age: 26 } }
);
```

### Delete

```js
db.users.deleteOne({ name: "Bob" });
```

#### `db.collection.insertOne()`

Insert a single document:

```js
db.users.insertOne({
    name: "David",
    email: "david@email.com",
    age: 32
});
```

#### `db.collection.insertMany()`

Insert multiple documents:

```js
db.users.insertMany([
    { name: "Eve", email: "eve@email.com", age: 27 },
    { name: "Frank", email: "frank@email.com", age: 29 }
]);
```

---

# Aggregation in MongoDB

Aggregation transforms and combines data from multiple documents to produce summarized results. It's used for grouping, filtering, and calculating aggregate values.

## Aggregation Pipeline

An aggregation pipeline is a sequence of stages that process documents. Each stage transforms documents as they pass through.

### Example Pipeline

```json
[
    { "$match": { "status": "active" } },
    { "$group": { "_id": "$category", "total": { "$sum": "$amount" } } },
    { "$sort": { "total": -1 } }
]
```

**Stages:**
- `$match`: Filter documents.
- `$group`: Group and aggregate.
- `$sort`: Sort results.

See [MongoDB Aggregation Documentation](https://www.mongodb.com/docs/manual/aggregation/) for details.

### Shell Example

```js
db.collection.aggregate([
    { $match: { status: "active" } },
    { $group: { _id: "$category", total: { $sum: "$amount" } } },
    { $sort: { total: -1 } }
])
```

## Common Aggregation Stages

- `$match`: Filter documents.
- `$group`: Group and aggregate.
- `$sort`: Sort documents.
- `$project`: Reshape documents.
- `$limit`: Limit output.
- `$skip`: Skip documents.
- `$unwind`: Expand array fields.

## Stage Priority

Stages are executed in order. Typical optimization:

1. `$match` (filter early)
2. `$group` (aggregate)
3. `$sort` (order results)
4. `$project` (reshape)
5. `$limit` (restrict output)
6. `$skip` (skip documents)
7. `$unwind` (expand arrays)

Placing `$match` and `$project` early can improve performance.

## Sample Data

```json
[
    { "category": "A", "status": "active", "amount": 100 },
    { "category": "B", "status": "inactive", "amount": 50 },
    { "category": "A", "status": "active", "amount": 200 },
    { "category": "C", "status": "active", "amount": 75 }
]
```

## Example Results by Stage

### `$match`

```json
[
    { "category": "A", "status": "active", "amount": 100 },
    { "category": "A", "status": "active", "amount": 200 },
    { "category": "C", "status": "active", "amount": 75 }
]
```

### `$group`

```json
[
    { "_id": "A", "total": 300 },
    { "_id": "C", "total": 75 }
]
```

### `$sort`

```json
[
    { "_id": "A", "total": 300 },
    { "_id": "C", "total": 75 }
]
```

### `$project`

```json
[
    { "category": "A", "amount": 100 },
    { "category": "A", "amount": 200 },
    { "category": "C", "amount": 75 }
]
```

### `$limit`

```json
[
    { "category": "A", "amount": 100 }
]
```

### `$skip`

```json
[
    { "category": "A", "amount": 200 },
    { "category": "C", "amount": 75 }
]
```

### `$unwind`

If documents have array fields, `$unwind` creates a document for each array element.

See [MongoDB Aggregation Pipeline Stages](https://www.mongodb.com/docs/manual/reference/operator/aggregation-pipeline/) for more examples.
