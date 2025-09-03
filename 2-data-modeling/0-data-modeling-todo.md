# TODO MongoDB Data Modeling
# https://www.mongodb.com/docs/manual/data-modeling/

**Definition:**  
Data modeling in MongoDB is the process of designing the structure of documents and collections to efficiently store, retrieve, and manage data. It involves choosing between embedding and referencing, handling relationships, optimizing for queries, and considering scalability.

---

## Key Concepts

### 1. Documents and Collections
- **Document:** A JSON-like object (BSON) that stores data.
- **Collection:** A group of documents, similar to a table in relational databases.

**Example:**
```json
{
  "_id": ObjectId("..."),
  "name": "Alice",
  "age": 30,
  "address": {
    "street": "123 Main St",
    "city": "Metropolis"
  }
}
```

---

### 2. Embedding vs. Referencing

- **Embedding:** Store related data in the same document.
- **Referencing:** Store related data in separate documents and link using references.

**Embedding Example:**
```json
{
  "name": "Alice",
  "orders": [
    { "item": "Book", "qty": 2 },
    { "item": "Pen", "qty": 5 }
  ]
}
```

**Referencing Example:**
```json
// users collection
{ "_id": ObjectId("..."), "name": "Alice" }

// orders collection
{ "userId": ObjectId("..."), "item": "Book", "qty": 2 }
```

---

### 3. One-to-One, One-to-Many, Many-to-Many Relationships

- **One-to-One:** Embed or reference.
- **One-to-Many:** Embed if "many" is small and static; reference if large or growing.
- **Many-to-Many:** Use referencing and linking collections.

**Example:**
```json
// Many-to-Many: students and courses
{ "_id": ObjectId("..."), "studentName": "Alice", "courseIds": [ObjectId("..."), ObjectId("...")] }
{ "_id": ObjectId("..."), "courseName": "Math", "studentIds": [ObjectId("..."), ObjectId("...")] }
```

---

### 4. Schema Design

- **Flexible Schema:** Documents in a collection can have different fields.
- **Validation:** Use schema validation to enforce rules.

**Example:**
```js
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "age"],
      properties: {
        name: { bsonType: "string" },
        age: { bsonType: "int", minimum: 0 }
      }
    }
  }
})
```

---

### 5. Indexing

- Create indexes on fields that are frequently queried to improve performance.

**Example:**
```js
db.users.createIndex({ name: 1 })
```

---

### 6. Data Aggregation

- Use aggregation pipelines to transform and analyze data.

**Example:**
```js
db.orders.aggregate([
  { $match: { status: "delivered" } },
  { $group: { _id: "$customerId", total: { $sum: "$amount" } } }
])
```

---

### 7. Denormalization vs. Normalization

- **Denormalization:** Duplicate data for faster reads (common in MongoDB).
- **Normalization:** Minimize redundancy, use references (common in relational DBs).

---

### 8. Polymorphic Schemas

- Store different types of related data in the same collection.

**Example:**
```json
{ "_id": 1, "type": "car", "wheels": 4 }
{ "_id": 2, "type": "bike", "wheels": 2 }
```

---

### 9. Data Integrity and Atomic Operations

- Single document writes are atomic.
- Use transactions for multi-document atomicity.

---

## Summary

- Choose embedding for related, small, static data.
- Use referencing for large, growing, or loosely related data.
- Design schemas based on query patterns and scalability needs.
- Use indexes and aggregation for performance and analytics.
- Consider denormalization for read