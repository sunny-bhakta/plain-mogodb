## MongoDB Aggregation Overview

Aggregation operations combine or summarize data, such as sum, average, minimum, maximum, or custom logic. They are commonly used in data processing, analytics, and reporting scenarios to group and summarize multiple records.

### Examples
- Summing values in a column
- Calculating averages
- Finding minimum or maximum values in a group

### Usage
Specify the aggregation method when querying or processing data.

---

### Aggregation Pipeline Stages

Each stage processes documents and passes results to the next stage.

| Stage         | Description                                               |
|---------------|----------------------------------------------------------|
| `$match`      | Filters documents (like a query)                         |
| `$project`    | Reshapes documents (include, exclude, add fields)        |
| `$group`      | Groups documents by key and applies accumulators         |
| `$sort`       | Sorts documents                                          |
| `$limit`      | Limits number of documents                               |
| `$skip`       | Skips a number of documents                              |
| `$unwind`     | Deconstructs arrays into multiple documents              |
| `$lookup`     | Left outer join with another collection                  |
| `$facet`      | Processes multiple pipelines in one stage                |
| `$addFields`  | Adds or replaces fields                                  |
| `$replaceRoot`| Replaces root document                                   |
| `$count`      | Counts documents                                         |
| `$sortByCount`| Groups, counts, and sorts by count                       |
| `$merge`      | Writes results to a collection                           |
| `$out`        | Writes results to a collection (replaces it)             |
| `$geoNear`    | Geospatial queries and sorts by proximity                |
| `$set`        | Alias for `$addFields`                                   |
| `$sample`     | Randomly selects documents                               |
| `$redact`     | Filters document content by condition                    |
| `$indexStats` | Returns index statistics                                 |
| `$currentOp`  | Info on current operations                               |
| `$unionWith`  | Combines pipeline with another collection                |

---

### Aggregation Operators

Operators are used within stages to manipulate or compute values.

#### Expression Operators

- **Arithmetic:** `$add`, `$subtract`, `$multiply`, `$divide`, `$mod`
- **Array:** `$arrayElemAt`, `$concatArrays`, `$filter`, `$size`, `$slice`
- **Boolean:** `$and`, `$or`, `$not`, `$cond`
- **Comparison:** `$eq`, `$ne`, `$gt`, `$gte`, `$lt`, `$lte`
- **Conditional:** `$ifNull`, `$switch`
- **Date:** `$dateToString`, `$year`, `$month`, `$dayOfMonth`, `$hour`, `$minute`, `$second`, `$millisecond`
- **String:** `$concat`, `$substr`, `$toLower`, `$toUpper`, `$trim`, `$split`, `$indexOfBytes`
- **Set:** `$setUnion`, `$setIntersection`, `$setDifference`, `$setIsSubset`
- **Type:** `$type`
- **Variable:** `$let`

#### Accumulator Operators (mainly in `$group`)

- `$sum`, `$avg`, `$min`, `$max`
- `$push` (append value to array)
- `$addToSet` (append unique values)
- `$first`, `$last`
- `$stdDevPop`, `$stdDevSamp`

---

### Summary

- **Stages** control the flow and transformation of documents.
- **Operators** perform calculations or logic inside stages.
