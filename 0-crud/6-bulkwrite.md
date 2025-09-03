# MongoDB Bulk Write Methods

MongoDB bulk write operations allow you to perform multiple write operations (insert, update, delete, replace) in a single command for efficiency.

---

## 1. Insert One

**Definition:**  
Inserts a single document.

**Example:**
```js
db.collection.bulkWrite([
  { insertOne: { document: { name: "Alice", age: 25 } } }
])
```

---

## 2. Update One

**Definition:**  
Updates a single document that matches the filter.

**Example:**
```js
db.collection.bulkWrite([
  { updateOne: { filter: { name: "Alice" }, update: { $set: { age: 26 } } } }
])
```

---

## 3. Update Many

**Definition:**  
Updates all documents that match the filter.

**Example:**
```js
db.collection.bulkWrite([
  { updateMany: { filter: { age: { $lt: 30 } }, update: { $set: { status: "young" } } } }
])
```

---

## 4. Replace One

**Definition:**  
Replaces a single document that matches the filter with a new document.

**Example:**
```js
db.collection.bulkWrite([
  { replaceOne: { filter: { name: "Alice" }, replacement: { name: "Alice", age: 27, status: "active" } } }
])
```

---

## 5. Delete One

**Definition:**  
Deletes a single document that matches the filter.

**Example:**
```js
db.collection.bulkWrite([
  { deleteOne: { filter: { name: "Alice" } } }
])
```

---

## 6. Delete Many

**Definition:**  
Deletes all documents that match the filter.

**Example:**
```js
db.collection.bulkWrite([
  { deleteMany: { filter: { status: "inactive" } } }
])
```

---

**Note:**  
You can combine multiple operations in a single `bulkWrite` array for batch