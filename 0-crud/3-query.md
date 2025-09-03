# MongoDB Query: Array Null & Timeout Example

## Querying for Documents Where Array Field is Null

```js
db.collection.find({ arrayField: null })
```

## Querying for Documents Where Array Field is an Empty Array

```js
db.collection.find({ arrayField: { $size: 0 } })
```

## Setting a Query Timeout

```js
db.collection.find({ /* query */ }).maxTimeMS(5000) // Timeout after 5 seconds
```

## Table: Query Examples

| Scenario                        | Query Example                                      |
|----------------------------------|---------------------------------------------------|
| Array field is null              | `{ arrayField: null }`                            |
| Array field is empty             | `{ arrayField: { $size: 0 } }`                    |
| Set query timeout (5 seconds)    | `.maxTimeMS(5000)`                                |

### Additional Array Query Examples

| Scenario                              | Query Example                                      |
|----------------------------------------|---------------------------------------------------|
| Array contains exactly 3 elements      | `{ arrayField: { $size: 3 } }`                    |
| Array contains element matching value  | `{ arrayField: "someValue" }`                     |
| Array contains element matching object | `{ arrayField: { $elemMatch: { key: "value" } } }`|
| Array contains element greater than 5  | `{ arrayField: { $elemMatch: { $gt: 5 } } }`      |


## Performing Long-Running Snapshot Queries

MongoDB supports snapshot queries to ensure a consistent view of data during long-running operations. Use the `readConcern` option with `"snapshot"` level for transactions or supported queries.

```js
db.collection.find({ /* query */ })
    .readConcern("snapshot")
    .maxTimeMS(10000) // Timeout after 10 seconds
```

> **Note:** Snapshot reads require a replica set and may need transactions for full consistency.

### More Snapshot Query Examples

| Scenario                                   | Query Example                                                      |
|---------------------------------------------|-------------------------------------------------------------------|
| Snapshot read with filter                   | `db.collection.find({ status: "active" }).readConcern("snapshot")`|
| Snapshot read in a transaction              | `session.startTransaction({ readConcern: { level: "snapshot" } })`|
| Snapshot read with sort and limit           | `db.collection.find({}).sort({ date: -1 }).limit(10).readConcern("snapshot")`|
| Snapshot read with aggregation pipeline     | `db.collection.aggregate([ { $match: { status: "active" } } ], { readConcern: { level: "snapshot" } })`|

### More Array Query Examples

| Scenario                                   | Query Example                                         |
|---------------------------------------------|------------------------------------------------------|
| Array contains any of multiple values       | `{ arrayField: { $in: ["value1", "value2"] } }`      |
| Array contains all specified values         | `{ arrayField: { $all: ["value1", "value2"] } }`     |
| Array does not contain a specific value     | `{ arrayField: { $ne: "value" } }`                   |
| Array field exists (not null or missing)    | `{ arrayField: { $exists: true } }`                  |
| Array field does not exist                  | `{ arrayField: { $exists: false } }`                 |
| Array element matches regex                 | `{ arrayField: { $elemMatch: { $regex: /pattern/ } } }` |



