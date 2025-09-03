# SQL to MongoDB Mapping Cheat Sheet

This guide provides a quick reference for mapping common SQL concepts, commands, and queries to their MongoDB equivalents.

---

## Terminology Mapping

| SQL Term/Concept      | MongoDB Term/Concept |
|-----------------------|---------------------|
| database              | [database](https://mongodb.com/docs/manual/reference/glossary/#std-term-database) |
| table                 | [collection](https://mongodb.com/docs/manual/reference/glossary/#std-term-collection) |
| row                   | [document](https://mongodb.com/docs/manual/reference/glossary/#std-term-document) or [BSON](https://mongodb.com/docs/manual/reference/glossary/#std-term-BSON) document |
| column                | [field](https://mongodb.com/docs/manual/reference/glossary/#std-term-field) |
| index                 | [index](https://mongodb.com/docs/manual/reference/glossary/#std-term-index) |
| table joins           | [`$lookup`](https://mongodb.com/docs/manual/reference/operator/aggregation/lookup/#mongodb-pipeline-pipe.-lookup), embedded documents |
| primary key           | [primary key](https://mongodb.com/docs/manual/reference/glossary/#std-term-primary-key) (`_id` field in MongoDB) |
| aggregation (group by)| aggregation pipeline |
| SELECT INTO NEW_TABLE | [`$out`](https://mongodb.com/docs/manual/reference/operator/aggregation/out/#mongodb-pipeline-pipe.-out) |
| MERGE INTO TABLE      | [`$merge`](https://mongodb.com/docs/manual/reference/operator/aggregation/merge/#mongodb-pipeline-pipe.-merge) |
| UNION ALL             | [`$unionWith`](https://mongodb.com/docs/manual/reference/operator/aggregation/unionWith/#mongodb-pipeline-pipe.-unionWith) |
| transactions          | [transactions](https://mongodb.com/docs/manual/core/transactions/#std-label-transactions) |

---

## Executables Mapping

| Role            | MongoDB         | MySQL     | Oracle   | Informix   | DB2         |
|-----------------|-----------------|-----------|----------|------------|-------------|
| Database Server | `mongod`        | `mysqld`  | `oracle` | `IDS`      | `DB2 Server`|
| Database Client | `mongosh`       | `mysql`   | `sqlplus`| `DB-Access`| `DB2 Client`|

---

## CRUD Examples

### Create/Alter Table

**SQL**
```sql
CREATE TABLE people (
    id MEDIUMINT NOT NULL AUTO_INCREMENT,
    user_id Varchar(30),
    age Number,
    status char(1),
    PRIMARY KEY (id)
)
```
**MongoDB**
```javascript
db.people.insertOne({ user_id: "abc123", age: 55, status: "A" })
```
Or explicitly create:
```javascript
db.createCollection("people")
```

**Add Column**
```sql
ALTER TABLE people ADD join_date DATETIME
```
**MongoDB**
```javascript
db.people.updateMany({}, { $set: { join_date: new Date() } })
```

**Drop Column**
```sql
ALTER TABLE people DROP COLUMN join_date
```
**MongoDB**
```javascript
db.people.updateMany({}, { $unset: { "join_date": "" } })
```

**Create Index**
```sql
CREATE INDEX idx_user_id_asc ON people(user_id)
```
**MongoDB**
```javascript
db.people.createIndex({ user_id: 1 })
```

**Drop Table**
```sql
DROP TABLE people
```
**MongoDB**
```javascript
db.people.drop()
```

---

### Insert

**SQL**
```sql
INSERT INTO people(user_id, age, status) VALUES ("bcd001", 45, "A")
```
**MongoDB**
```javascript
db.people.insertOne({ user_id: "bcd001", age: 45, status: "A" })
```

---

### Select

**Select All**
```sql
SELECT * FROM people
```
```javascript
db.people.find()
```

**Select Specific Fields**
```sql
SELECT user_id, status FROM people
```
```javascript
db.people.find({}, { user_id: 1, status: 1, _id: 0 })
```

**Where Clause**
```sql
SELECT * FROM people WHERE status = "A"
```
```javascript
db.people.find({ status: "A" })
```

**Count**
```sql
SELECT COUNT(*) FROM people
```
```javascript
db.people.count()
```

**Distinct**
```sql
SELECT DISTINCT(status) FROM people
```
```javascript
db.people.distinct("status")
```

**Limit & Skip**
```sql
SELECT * FROM people LIMIT 5 SKIP 10
```
```javascript
db.people.find().limit(5).skip(10)
```

**Regex/LIKE**
```sql
SELECT * FROM people WHERE user_id LIKE "%bc%"
```
```javascript
db.people.find({ user_id: /bc/ })
```

---

### Update

**SQL**
```sql
UPDATE people SET status = "C" WHERE age > 25
```
**MongoDB**
```javascript
db.people.updateMany({ age: { $gt: 25 } }, { $set: { status: "C" } })
```

**Increment**
```sql
UPDATE people SET age = age + 3 WHERE status = "A"
```
**MongoDB**
```javascript
db.people.updateMany({ status: "A" }, { $inc: { age: 3 } })
```

---

### Delete

**SQL**
```sql
DELETE FROM people WHERE status = "D"
```
**MongoDB**
```javascript
db.people.deleteMany({ status: "D" })
```

**Delete All**
```sql
DELETE FROM people
```
**MongoDB**
```javascript
db.people.deleteMany({})
```

---

## Useful MongoDB References

- [Insert Documents](https://mongodb.com/docs/manual/tutorial/insert-documents/)
- [Query Documents](https://mongodb.com/docs/manual/tutorial/query-documents/)
- [Update Documents](https://mongodb.com/docs/manual/tutorial/update-documents/)
- [Delete Documents](https://mongodb.com/docs/manual/tutorial/remove-documents/)
- [Aggregation](https://mongodb.com/docs/manual/aggregation/)
- [Data Modeling Concepts](https://mongodb.com/docs/manual/data-modeling/concepts/)

---

**Tip:** MongoDB collections are schema-less; structure is enforced at the application level, not the database.

