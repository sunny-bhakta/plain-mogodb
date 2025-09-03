| Stage                     | Sample Data                                                                 | Sample Query                                                                                                    | Result                                                                                   | Sample Table Data                                   | SQL Query                                                                                 | SQL Result                                                      | Explanation                                                                                       |
| Stage                         | Sample Data                                                                 | Sample Query                                                                                                    | Result                                                                                   | Sample Table Data                                   | SQL Query                                                                                 | SQL Result                                                      | Explanation                                                                                       |
|-------------------------------|-----------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|-----------------------------------------------------|-------------------------------------------------------------------------------------------|------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| `$addFields`                  | `{ "name": "Alice", "score": 85 }`                                         | `{ $addFields: { passed: { $gte: ["$score", 60] } } }`                                                          | `{ "name": "Alice", "score": 85, "passed": true }`                                      | `name | score`<br>`Alice | 85`                    | `SELECT name, score, score >= 60 AS passed FROM students;`                               | `Alice | 85 | true`                                            | Adds a new field `passed` based on a condition.                                                   |
| `$bucket`                     | `{ "score": 72 }, { "score": 85 }, { "score": 90 }`                        | `{ $bucket: { groupBy: "$score", boundaries: [0, 80, 100], default: "Other", output: { count: { $sum: 1 } } }}` | `{ "_id": 0, "count": 1 }, { "_id": 80, "count": 2 }`                                   | `score`<br>`72`<br>`85`<br>`90`                     | `SELECT CASE WHEN score < 80 THEN 0 ELSE 80 END AS bucket, COUNT(*) FROM scores GROUP BY bucket;` | `0 | 1`<br>`80 | 2`                                             | Groups documents into buckets based on score boundaries.                                         |
| `$bucketAuto`                 | `{ "score": 72 }, { "score": 85 }, { "score": 90 }`                        | `{ $bucketAuto: { groupBy: "$score", buckets: 2 } }`                                                            | Buckets with auto boundaries, e.g. `{ "count": 2 }, { "count": 1 }`                     | `score`<br>`72`<br>`85`<br>`90`                     | No direct SQL equivalent; use NTILE: `SELECT score, NTILE(2) OVER (ORDER BY score) AS bucket FROM scores;` | Buckets assigned automatically                              | Automatically divides documents into specified number of buckets.                               |
| `$changeStream`               | N/A                                                                         | `{ $changeStream: {} }`                                                                                          | Change stream cursor (real-time changes)                                                 | N/A                                                | N/A                                                                                       | N/A                                                             | Streams real-time changes to the collection.                                                      |
| `$changeStreamSplitLargeEvent`| N/A                                                                         | `{ $changeStreamSplitLargeEvent: {} }`                                                                          | Split large change stream events                                                         | N/A                                                | N/A                                                                                       | N/A                                                             | Splits large change stream events for easier processing.                                          |
| `$collStats`                  | N/A                                                                         | `{ $collStats: { latencyStats: {} } }`                                                                          | Collection statistics                                                                    | N/A                                                | N/A                                                                                       | N/A                                                             | Returns statistics about the collection.                                                           |
| `$count`                      | `{ "name": "Alice" }, { "name": "Bob" }`                                   | `{ $count: "total" }`                                                                                            | `{ "total": 2 }`                                                                        | `name`<br>`Alice`<br>`Bob`                          | `SELECT COUNT(*) AS total FROM users;`                                                    | `total | 2`                                                     | Counts the number of documents.                                                                   |
| `$densify`                    | `{ "date": "2023-01-01" }, { "date": "2023-01-03" }`                       | `{ $densify: { field: "date", range: { step: 1, unit: "day" } } }`                                              | Adds `{ "date": "2023-01-02" }`                                                          | `date`<br>`2023-01-01`<br>`2023-01-03`              | No direct SQL equivalent; use calendar table and LEFT JOIN.                                 | `2023-01-01`<br>`2023-01-02`<br>`2023-01-03`                  | Fills in missing dates in a sequence.                                                             |
| `$documents`                  | N/A                                                                         | `{ $documents: [ { "x": 1 }, { "x": 2 } ] }`                                                                    | `{ "x": 1 }, { "x": 2 }`                                                                | `x`<br>`1`<br>`2`                                    | `SELECT 1 AS x UNION ALL SELECT 2 AS x;`                                                  | `x`<br>`1`<br>`2`                                              | Returns the provided documents directly.                                                           |
| `$facet`                      | `{ "score": 85 }, { "score": 90 }`                                         | `{ $facet: { high: [ { $match: { score: { $gte: 90 } } } ], low: [ { $match: { score: { $lt: 90 } } } ] } }`    | `{ "high": [ { "score": 90 } ], "low": [ { "score": 85 } ] }`                           | `score`<br>`85`<br>`90`                              | `SELECT score FROM scores WHERE score >= 90;`<br>`SELECT score FROM scores WHERE score < 90;` | `high: 90`<br>`low: 85`                                       | Runs multiple queries in parallel and returns results as facets.                                 |
| `$fill`                       | `{ "date": "2023-01-01", "value": 10 }, { "date": "2023-01-02" }`          | `{ $fill: { output: { value: { method: "linear" } } } }`                                                        | Fills missing `value` for "2023-01-02"                                                   | `date | value`<br>`2023-01-01 | 10`<br>`2023-01-02 | NULL` | No direct SQL equivalent; use window functions or interpolation.                        | `2023-01-01 | 10`<br>`2023-01-02 | 10`                              | Fills missing values using a specified method (e.g., linear interpolation).                     |
| `$geoNear`                    | `{ "loc": [0,0] }, { "loc": [1,1] }`                                       | `{ $geoNear: { near: [0,0], distanceField: "dist" } }`                                                          | Documents sorted by proximity, with `dist` field                                         | `loc`<br>`[0,0]`<br>`[1,1]`                          | No direct SQL equivalent; use spatial extensions.                                         | Sorted by distance from `[0,0]`                                   | Returns documents sorted by proximity to a point.                                                 |
| `$graphLookup`                | `{ "_id": 1, "parent": null }, { "_id": 2, "parent": 1 }`                  | `{ $graphLookup: { from: "coll", startWith: "$_id", connectFromField: "_id", connectToField: "parent", as: "tree" } }` | Adds `tree` array with connected documents                                               | `_id | parent`<br>`1 | NULL`<br>`2 | 1`                  | Recursive CTE: `WITH RECURSIVE tree AS (...)`                                            | Tree structure                                                | Performs recursive search to build a hierarchy/tree.                                              |
| `$group`                      | `{ "cat": "A", "amt": 10 }, { "cat": "A", "amt": 20 }`                     | `{ $group: { _id: "$cat", total: { $sum: "$amt" } } }`                                                          | `{ "_id": "A", "total": 30 }`                                                           | `cat | amt`<br>`A | 10`<br>`A | 20`                        | `SELECT cat, SUM(amt) AS total FROM sales GROUP BY cat;`                                 | `A | 30`                                                        | Groups documents by a field and calculates aggregates.                                            |
| `$indexStats`                 | N/A                                                                         | `{ $indexStats: {} }`                                                                                            | Index usage statistics                                                                  | N/A                                                | N/A                                                                                       | N/A                                                             | Returns statistics about indexes.                                                                  |
| `$limit`                      | `{ "name": "Alice" }, { "name": "Bob" }`                                   | `{ $limit: 1 }`                                                                                                 | `{ "name": "Alice" }`                                                                   | `name`<br>`Alice`<br>`Bob`                              | `SELECT name FROM users LIMIT 1;`                                                        | `Alice`                                                        | Limits the number of documents returned.                                                           |
| `$listClusterCatalog`         | N/A                                                                         | `{ $listClusterCatalog: {} }`                                                                                    | Cluster catalog info                                                                    | N/A                                                | N/A                                                                                       | N/A                                                             | Lists cluster catalog information.                                                                 |
| `$listSampledQueries`         | N/A                                                                         | `{ $listSampledQueries: {} }`                                                                                    | Sampled queries info                                                                    | N/A                                                | N/A                                                                                       | N/A                                                             | Lists sampled queries information.                                                                 |
| `$listSearchIndexes`          | N/A                                                                         | `{ $listSearchIndexes: {} }`                                                                                     | Search indexes info                                                                     | N/A                                                | N/A                                                                                       | N/A                                                             | Lists search indexes information.                                                                  |
| `$listSessions`               | N/A                                                                         | `{ $listSessions: {} }`                                                                                          | Session info                                                                            | N/A                                                | N/A                                                                                       | N/A                                                             | Lists session information.                                                                         |
| `$lookup`                     | `{ "userId": 1 }` in coll1, `{ "userId": 1, "order": "A" }` in coll2       | `{ $lookup: { from: "coll2", localField: "userId", foreignField: "userId", as: "orders" } }`                    | `{ "userId": 1, "orders": [ { "userId": 1, "order": "A" } ] }`                         | `coll1: userId`<br>`1`<br>`coll2: userId | order`<br>`1 | A` | `SELECT coll1.userId, coll2.order FROM coll1 LEFT JOIN coll2 ON coll1.userId = coll2.userId;` | `1 | A`                                                        | Joins documents from another collection based on a field.                                         |
| `$match`                      | `{ "status": "active" }, { "status": "inactive" }`                          | `{ $match: { status: "active" } }`                                                                              | `{ "status": "active" }`                                                                | `status`<br>`active`<br>`inactive`                        | `SELECT * FROM users WHERE status = 'active';`                                            | `active`                                                        | Filters documents by a condition.                                                                  |
| `$merge`                      | `{ "name": "Alice" }`                                                      | `{ $merge: { into: "outputColl" } }`                                                                            | Writes document to `outputColl`                                                         | `name`<br>`Alice`                                        | `INSERT INTO outputColl (name) VALUES ('Alice');`                                         | `Alice`                                                        | Merges documents into another collection/table.                                                    |
| `$out`                        | `{ "name": "Alice" }`                                                      | `{ $out: "outputColl" }`                                                                                         | Writes document to `outputColl`                                                         | `name`<br>`Alice`                                        | `INSERT INTO outputColl (name) VALUES ('Alice');`                                         | `Alice`                                                        | Writes documents to another collection/table.                                                      |
| `$planCacheStats`             | N/A                                                                         | `{ $planCacheStats: {} }`                                                                                        | Plan cache info                                                                         | N/A                                                | N/A                                                                                       | N/A                                                             | Returns query plan cache statistics.                                                               |
| `$project`                    | `{ "name": "Alice", "score": 85 }`                                         | `{ $project: { name: 1 } }`                                                                                      | `{ "name": "Alice" }`                                                                   | `name | score`<br>`Alice | 85`                            | `SELECT name FROM students;`                                                             | `Alice`                                                        | Selects specific fields from documents.                                                            |
| `$querySettings`              | N/A                                                                         | `{ $querySettings: {} }`                                                                                         | Query settings info                                                                     | N/A                                                | N/A                                                                                       | N/A                                                             | Returns query settings information.                                                                |
| `$queryStats`                 | N/A                                                                         | `{ $queryStats: {} }`                                                                                            | Query stats info                                                                        | N/A                                                | N/A                                                                                       | N/A                                                             | Returns query statistics information.                                                              |
| `$redact`                     | `{ "level": "secret", "data": "x" }`                                       | `{ $redact: { $cond: [ { $eq: ["$level", "secret"] }, "$$PRUNE", "$$KEEP" ] } }`                                | Removes documents with `level: "secret"`                                                | `level | data`<br>`secret | x`                                 | `SELECT * FROM docs WHERE level <> 'secret';`                                             | (no rows)                                                       | Removes documents based on a condition.                                                            |
| `$replaceRoot`                | `{ "a": { "b": 1 }, "c": 2 }`                                              | `{ $replaceRoot: { newRoot: "$a" } }`                                                                           | `{ "b": 1 }`                                                                            | `a | c`<br>`{b:1} | 2`                                    | No direct SQL equivalent; use subqueries or CTEs.                                         | `b | 1`                                                        | Replaces the root document with a subdocument.                                                     |
| `$replaceWith`                | `{ "a": { "b": 1 }, "c": 2 }`                                              | `{ $replaceWith: "$a" }`                                                                                         | `{ "b": 1 }`                                                                            | `a | c`<br>`{b:1} | 2`                                    | No direct SQL equivalent; use subqueries or CTEs.                                         | `b | 1`                                                        | Replaces the document with the specified field.                                                     |
| `$sample`                     | `{ "name": "Alice" }, { "name": "Bob" }`                                   | `{ $sample: { size: 1 } }`                                                                                       | Randomly returns one document                                                           | `name`<br>`Alice`<br>`Bob`                              | `SELECT name FROM users ORDER BY RANDOM() LIMIT 1;`                                        | `Alice` or `Bob`                                                | Returns a random sample of documents.                                                               |
| `$search`                     | `{ "text": "MongoDB is great" }`                                           | `{ $search: { text: { query: "MongoDB", path: "text" } } }`                                                      | Documents matching full-text search                                                     | `text`<br>`MongoDB is great`                               | `SELECT * FROM docs WHERE text LIKE '%MongoDB%';`                                          | `MongoDB is great`                                              | Performs full-text search on documents.                                                             |
| `$searchMeta`                 | `{ "text": "MongoDB is great" }`                                           | `{ $searchMeta: { text: { query: "MongoDB", path: "text" } } }`                                                  | Metadata about search                                                                   | `text`<br>`MongoDB is great`                               | No direct SQL equivalent; use EXPLAIN or metadata queries.                                   | Metadata                                                         | Returns metadata about a search query.                                                               |
| `$set`                        | `{ "name": "Alice" }`                                                      | `{ $set: { age: 30 } }`                                                                                          | `{ "name": "Alice", "age": 30 }`                                                        | `name`<br>`Alice`                                            | `UPDATE users SET age = 30 WHERE name = 'Alice';`                                           | `Alice | 30`                                                      | Sets the value of a field in documents.                                                              |
| `$setWindowFields`            | `{ "score": 85 }, { "score": 90 }`                                         | `{ $setWindowFields: { partitionBy: null, sortBy: { score: 1 }, output: { avgScore: { $avg: "$score", window: { documents: [ "unbounded", "current" ] } } } } }` | Adds `avgScore` field                                                                   | `score`<br>`85`<br>`90`                                     | `SELECT score, AVG(score) OVER (ORDER BY score ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS avgScore FROM scores;` | `85 | 85`<br>`90 | 87.5`                                         | Adds windowed aggregate fields to documents.                                                        |
| `$skip`                       | `{ "name": "Alice" }, { "name": "Bob" }`                                   | `{ $skip: 1 }`                                                                                                   | `{ "name": "Bob" }`                                                                    | `name`<br>`Alice`<br>`Bob`                                   | `SELECT name FROM users OFFSET 1;`                                                          | `Bob`                                                           | Skips a number of documents before returning results.                                               |
| `$sort`                       | `{ "score": 85 }, { "score": 90 }`                                         | `{ $sort: { score: -1 } }`                                                                                       | `{ "score": 90 }, { "score": 85 }`                                                      | `score`<br>`85`<br>`90`                                     | `SELECT score FROM scores ORDER BY score DESC;`                                              | `90`<br>`85`                                                     | Sorts documents by a field.                                                                         |
| `$unionWith`              | `{ "name": "Alice" }` in coll1, `{ "name": "Bob" }` in coll2               | `{ $unionWith: { coll: "coll2" } }`                                                                             | `{ "name": "Alice" }, { "name": "Bob" }`                                                | `coll1: name`<br>`Alice`<br>`coll2: name`<br>`Bob`           | `SELECT name FROM coll1 UNION SELECT name FROM coll2;`                                      | `Alice`<br>`Bob`                                                 | Combines results from two collections/tables.                                                       |
| `$unset`                  | `{ "name": "Alice", "age": 30 }`                                           | `{ $unset: "age" }`                                                                                              | `{ "name": "Alice" }`                                                                   | `name | age`<br>`Alice | 30`                                 | No direct SQL equivalent; use SELECT without the column.                                    | `Alice`                                                         | Removes a field from documents.                                                                      |
| `$unwind`                 | `{ "tags": ["a", "b"] }`                                                   | `{ $unwind: "$tags" }`                                                                                           | `{ "tags": "a" }, { "tags": "b" }`                                                      | `tags`<br>`a,b`                                              | No direct SQL equivalent; use normalization or CROSS JOIN.                                   | `a`<br>`b`                                                        | Deconstructs array fields into separate documents/rows.                                             |
| `$vectorSearch`           | `{ "embedding": [0.1, 0.2, 0.3] }`                                         | `{ $vectorSearch: { queryVector: [0.1, 0.2, 0.3], path: "embedding", k: 1 } }`                                  | Most similar document(s)                                                                | `embedding`<br>`[0.1,0.2,0.3]`                                 | No direct SQL equivalent; use vector extensions.                                              | Most similar embedding                                            | Finds documents most similar to a given vector.                                                      |


## Example for Each Aggregation Stage

Below are examples for each MongoDB aggregation stage using a sample `sales` collection:

### Sample Collection

```json
{ "item": "apple", "price": 5 }
{ "item": "banana", "price": 15 }
{ "item": "orange", "price": 25 }
{ "item": "pear", "price": 35 }
```

---

### `$addFields`

Add a field `expensive` if price > 20:

```js
db.sales.aggregate([
    { $addFields: { expensive: { $gt: ["$price", 20] } } }
])
```

Result:

```json
{ "item": "apple", "price": 5, "expensive": false }
{ "item": "banana", "price": 15, "expensive": false }
{ "item": "orange", "price": 25, "expensive": true }
{ "item": "pear", "price": 35, "expensive": true }
```

---

### `$bucket`

Group items by price ranges:

```js
db.sales.aggregate([
    {
        $bucket: {
            groupBy: "$price",
            boundaries: [0, 10, 20, 30, 40],
            default: "Other",
            output: {
                count: { $sum: 1 },
                items: { $push: "$item" }
            }
        }
    }
])
```

Result:

```json
[
    { "_id": 0, "count": 1, "items": ["apple"] },
    { "_id": 10, "count": 1, "items": ["banana"] },
    { "_id": 20, "count": 1, "items": ["orange"] },
    { "_id": 30, "count": 1, "items": ["pear"] }
]
```

---

### `$bucketAuto`

Automatically group into 2 buckets:

```js
db.sales.aggregate([
    { $bucketAuto: { groupBy: "$price", buckets: 2 } }
])
```

Result:

```json
[
    { "count": 2, "items": ["apple", "banana"] },
    { "count": 2, "items": ["orange", "pear"] }
]
```

---

### `$count`

Count total documents:

```js
db.sales.aggregate([
    { $count: "total" }
])
```

Result:

```json
{ "total": 4 }
```

---

### `$group`

Sum prices by item type:

```js
db.sales.aggregate([
    { $group: { _id: null, totalPrice: { $sum: "$price" } } }
])
```

Result:

```json
{ "_id": null, "totalPrice": 80 }
```

---

### `$match`

Filter items with price > 20:

```js
db.sales.aggregate([
    { $match: { price: { $gt: 20 } } }
])
```

Result:

```json
{ "item": "orange", "price": 25 }
{ "item": "pear", "price": 35 }
```

---

### `$project`

Show only item names:

```js
db.sales.aggregate([
    { $project: { item: 1, _id: 0 } }
])
```

Result:

```json
{ "item": "apple" }
{ "item": "banana" }
{ "item": "orange" }
{ "item": "pear" }
```

---

### `$sort`

Sort by price descending:

```js
db.sales.aggregate([
    { $sort: { price: -1 } }
])
```

Result:

```json
{ "item": "pear", "price": 35 }
{ "item": "orange", "price": 25 }
{ "item": "banana", "price": 15 }
{ "item": "apple", "price": 5 }
```

---

### `$limit`

Limit to 2 documents:

```js
db.sales.aggregate([
    { $limit: 2 }
])
```

Result:

```json
{ "item": "apple", "price": 5 }
{ "item": "banana", "price": 15 }
```

---

### `$skip`

Skip first document:

```js
db.sales.aggregate([
    { $skip: 1 }
])
```

Result:

```json
{ "item": "banana", "price": 15 }
{ "item": "orange", "price": 25 }
{ "item": "pear", "price": 35 }
```

---

### `$unwind`

Suppose each document has an array field `tags`:

```json
{ "item": "apple", "tags": ["fruit", "red"] }
```

Unwind tags:

```js
db.sales.aggregate([
    { $unwind: "$tags" }
])
```

Result:

```json
{ "item": "apple", "tags": "fruit" }
{ "item": "apple", "tags": "red" }
```

---

### `$set`

Set a new field `discounted`:

```js
db.sales.aggregate([
    { $set: { discounted: true } }
])
```

Result:

```json
{ "item": "apple", "price": 5, "discounted": true }
...
```

---

### `$unset`

Remove the `price` field:

```js
db.sales.aggregate([
    { $unset: "price" }
])
```

Result:

```json
{ "item": "apple" }
...
```

---

### `$sample`

Get a random document:

```js
db.sales.aggregate([
    { $sample: { size: 1 } }
])
```

Result:

```json
{ "item": "banana", "price": 15 }
```

---

### `$lookup`

Join with another collection `inventory`:

```js
db.sales.aggregate([
    {
        $lookup: {
            from: "inventory",
            localField: "item",
            foreignField: "name",
            as: "inventoryDetails"
        }
    }
])
```

Result:

```json
{ "item": "apple", "price": 5, "inventoryDetails": [ ... ] }
```

---

### `$facet`

Run multiple pipelines:

```js
db.sales.aggregate([
    {
        $facet: {
            cheap: [ { $match: { price: { $lt: 20 } } } ],
            expensive: [ { $match: { price: { $gte: 20 } } } ]
        }
    }
])
```

Result:

```json
{
    "cheap": [ { "item": "apple", "price": 5 }, { "item": "banana", "price": 15 } ],
    "expensive": [ { "item": "orange", "price": 25 }, { "item": "pear", "price": 35 } ]
}
```

---

For other stages, see MongoDB documentation for advanced usage.

### Additional Aggregation Operators Examples

#### `$multiply`

Multiply price by 2:

```js
db.sales.aggregate([
    { $project: { item: 1, doublePrice: { $multiply: ["$price", 2] } } }
])
```

Result:

```json
{ "item": "apple", "doublePrice": 10 }
{ "item": "banana", "doublePrice": 30 }
{ "item": "orange", "doublePrice": 50 }
{ "item": "pear", "doublePrice": 70 }
```

---

#### `$sum`

Sum prices by item type (already shown in `$group`):

```js
db.sales.aggregate([
    { $group: { _id: null, totalPrice: { $sum: "$price" } } }
])
```

Result:

```json
{ "_id": null, "totalPrice": 80 }
```

---

#### `$first`

Get the first item in each price group:

```js
db.sales.aggregate([
    { $sort: { price: 1 } },
    { $group: { _id: null, firstItem: { $first: "$item" } } }
])
```

Result:

```json
{ "_id": null, "firstItem": "apple" }
```

---

#### `$push`

Push all item names into an array:

```js
db.sales.aggregate([
    { $group: { _id: null, items: { $push: "$item" } } }
])
```

Result:

```json
{ "_id": null, "items": ["apple", "banana", "orange", "pear"] }
```

---

#### `$gt`

Check if price > 20 (already shown in `$addFields`):

```js
db.sales.aggregate([
    { $addFields: { expensive: { $gt: ["$price", 20] } } }
])
```

Result:

```json
{ "item": "orange", "price": 25, "expensive": true }
```

---

#### `$size`

Get the number of tags per item:

```js
db.sales.aggregate([
    { $project: { item: 1, numTags: { $size: "$tags" } } }
])
```

Result:

```json
{ "item": "apple", "numTags": 2 }
```

---

#### `$dateToString`

Format a date field:

```js
db.sales.aggregate([
    { $project: { item: 1, dateStr: { $dateToString: { format: "%Y-%m-%d", date: "$date" } } } }
])
```

Result:

```json
{ "item": "apple", "dateStr": "2023-01-01" }
```

---

#### `$min`

Get the minimum price:

```js
db.sales.aggregate([
    { $group: { _id: null, minPrice: { $min: "$price" } } }
])
```

Result:

```json
{ "_id": null, "minPrice": 5 }
```

---

#### `$cond`

Conditional field based on price:

```js
db.sales.aggregate([
    { $project: { item: 1, priceCategory: { $cond: [ { $gte: ["$price", 20] }, "expensive", "cheap" ] } } }
])
```

Result:

```json
{ "item": "apple", "priceCategory": "cheap" }
{ "item": "orange", "priceCategory": "expensive" }
```

---

#### `$gte`

Check if price >= 15:

```js
db.sales.aggregate([
    { $addFields: { midOrHigh: { $gte: ["$price", 15] } } }
])
```

Result:

```json
{ "item": "apple", "midOrHigh": false }
{ "item": "banana", "midOrHigh": true }
```




### Sample Table Data

| item   | price | tags         | date       |
|--------|-------|--------------|------------|
| apple  | 5     | fruit, red   | 2023-01-01 |
| banana | 15    | fruit, yellow| 2023-01-02 |
| orange | 25    | fruit, orange| 2023-01-03 |
| pear   | 35    | fruit, green | 2023-01-04 |

---

### Aggregation Stages and Operators Example

Below is a comprehensive aggregation pipeline using multiple stages and operators:

```js
db.sales.aggregate([
    // 1. Filter items with price >= 10
    { $match: { price: { $gte: 10 } } },

    // 2. Add a field 'expensive' if price > 20
    { $addFields: { expensive: { $gt: ["$price", 20] } } },

    // 3. Project only item, price, expensive, and tags count
    { $project: {
        item: 1,
        price: 1,
        expensive: 1,
        numTags: { $size: "$tags" }
    }},

    // 4. Sort by price descending
    { $sort: { price: -1 } },

    // 5. Group by 'expensive' and sum prices
    { $group: {
        _id: "$expensive",
        totalPrice: { $sum: "$price" },
        items: { $push: "$item" }
    }},

    // 6. Add a conditional field for price category
    { $addFields: {
        priceCategory: {
            $cond: [ { $eq: ["$_id", true] }, "expensive", "cheap" ]
        }
    }},

    // 7. Limit to 2 groups
    { $limit: 2 }
])
```

**Result:**

```json
[
    {
        "_id": true,
        "totalPrice": 60,
        "items": ["orange", "pear"],
        "priceCategory": "expensive"
    },
    {
        "_id": false,
        "totalPrice": 15,
        "items": ["banana"],
        "priceCategory": "cheap"
    }
]
```

**Explanation:**

- `$match` filters items with price >= 10.
- `$addFields` creates an `expensive` flag.
- `$project` selects fields and counts tags.
- `$sort` orders by price.
- `$group` aggregates by expensive/cheap.
- `$addFields` uses `$cond` for category.
- `$limit` restricts output.

This example demonstrates how to use multiple aggregation stages and operators together on sample table data.



### `$lookup` Example: Join Orders with Product Details

Suppose you have two collections:

**orders**
```json
[
  { "_id": 1, "customerId": 101, "items": [ { "productId": 201, "qty": 2 }, { "productId": 202, "qty": 1 } ], "orderDate": ISODate("2023-08-01T10:00:00Z"), "status": "shipped" },
  { "_id": 2, "customerId": 102, "items": [ { "productId": 201, "qty": 1 } ], "orderDate": ISODate("2023-08-03T12:30:00Z"), "status": "processing" },
  { "_id": 3, "customerId": 101, "items": [ { "productId": 203, "qty": 4 } ], "orderDate": ISODate("2023-08-02T09:00:00Z"), "status": "delivered" }
]

```

**products**
```json
[
  { "_id": 201, "name": "Laptop", "price": 1000, "category": "Electronics" },
  { "_id": 202, "name": "Mouse", "price": 50, "category": "Electronics" },
  { "_id": 203, "name": "Chair", "price": 150, "category": "Furniture" }
]

```

To join each order item with its product details:

```js
db.orders.aggregate([
    { $unwind: "$items" },
    {
        $lookup: {
            from: "products",
            localField: "items.productId",
            foreignField: "_id",
            as: "productDetails"
        }
    },
    { $unwind: "$productDetails" },
    {
        $addFields: {
            "items.productName": "$productDetails.name",
            "items.price": "$productDetails.price",
            "items.category": "$productDetails.category",
            "items.totalPrice": { $multiply: [ "$items.qty", "$productDetails.price" ] }
        }
    },
    {
        $group: {
            _id: "$_id",
            customerId: { $first: "$customerId" },
            orderDate: { $first: "$orderDate" },
            status: { $first: "$status" },
            items: { $push: "$items" },
            orderTotal: { $sum: "$items.totalPrice" }
        }
    }
])
```

**Result:**
```json
{
    "_id": 1,
    "customerId": 101,
    "orderDate": "2023-08-01T10:00:00Z",
    "status": "shipped",
    "items": [
        {
            "productId": 201,
            "qty": 2,
            "productName": "Laptop",
            "price": 1000,
            "category": "Electronics",
            "totalPrice": 2000
        },
        {
            "productId": 202,
            "qty": 1,
            "productName": "Mouse",
            "price": 50,
            "category": "Electronics",
            "totalPrice": 50
        }
    ],
    "orderTotal": 2050
}
```

### Example 2: Advanced Aggregation Pipeline for Customer Spending

This example demonstrates a multi-stage aggregation pipeline that joins orders with product details, calculates totals, groups by customer, filters, sorts, and classifies customers.

```js
db.orders.aggregate([
    // 1. Join orders with product details on items.productId
    { $unwind: "$items" },  // Deconstruct array of items
    {
        $lookup: {
            from: "products",
            localField: "items.productId",
            foreignField: "_id",
            as: "productDetails"
        }
    },
    { $unwind: "$productDetails" },  // Deconstruct lookup result

    // 2. Add total price per item (price * qty)
    {
        $addFields: {
            "items.totalPrice": { $multiply: [ "$items.qty", "$productDetails.price" ] },
            "items.productName": "$productDetails.name",
            "items.category": "$productDetails.category"
        }
    },

    // 3. Group by order id to reconstruct orders with total price per order
    {
        $group: {
            _id: "$_id",
            customerId: { $first: "$customerId" },
            orderDate: { $first: "$orderDate" },
            status: { $first: "$status" },
            items: { $push: "$items" },
            orderTotal: { $sum: "$items.totalPrice" }
        }
    },

    // 4. Group by customer to sum total spent across orders
    {
        $group: {
            _id: "$customerId",
            totalSpent: { $sum: "$orderTotal" },
            orders: {
                $push: {
                    orderId: "$_id",
                    orderDate: "$orderDate",
                    status: "$status",
                    total: "$orderTotal",
                    items: "$items"
                }
            }
        }
    },

    // 5. Filter customers who spent more than 1000
    { $match: { totalSpent: { $gt: 1000 } } },

    // 6. Sort customers by total spent descending
    { $sort: { totalSpent: -1 } },

    // 7. Project only needed fields + formatted date string for first order's date
    {
        $project: {
            _id: 0,
            customerId: "$_id",
            totalSpent: 1,
            orderCount: { $size: "$orders" },
            firstOrderDate: {
                $dateToString: {
                    format: "%Y-%m-%d",
                    date: { $min: "$orders.orderDate" }
                }
            },
            orders: 1
        }
    },

    // 8. Skip the first customer, limit to 5 customers
    { $skip: 1 },
    { $limit: 5 },

    // 9. Add a field to classify customers
    {
        $addFields: {
            customerType: {
                $cond: {
                    if: { $gte: ["$totalSpent", 5000] },
                    then: "VIP",
                    else: "Regular"
                }
            }
        }
    },

    // 10. Optionally count total customers after filter, in facet
    {
        $facet: {
            customers: [ { $match: {} } ],  // Pass-through to keep customers
            totalCount: [ { $count: "count" } ]
        }
    }
])
```

**Pipeline Highlights:**
- Joins each order item with product details.
- Calculates total price per item and per order.
- Groups orders by customer and sums total spent.
- Filters for customers who spent more than 1000.
- Sorts, paginates, and classifies customers as "VIP" or "Regular".
- Uses `$facet` to return both customer data and total count.


```