# MongoDB Read Concern & Write Concern

MongoDB uses **read concern** and **write concern** to control the consistency and durability of data for read and write operations.

---

## Read Concern Levels

### 1. `local`
**Definition:** Returns data from the node, regardless of replication.  
**Example:**
```js
db.collection.find({}, { readConcern: { level: "local" } })
```

### 2. `available`
**Definition:** Returns data from the node even if it is not guaranteed to be written to disk.  
**Example:**
```js
db.collection.find({}, { readConcern: { level: "available" } })
```

### 3. `majority`
**Definition:** Returns data that has been acknowledged by the majority of replica set members.  
**Example:**
```js
db.collection.find({}, { readConcern: { level: "majority" } })
```

### 4. `linearizable`
**Definition:** Guarantees strict consistency; reads reflect the most recent acknowledged writes.  
**Example:**
```js
db.collection.find({}, { readConcern: { level: "linearizable" } })
```

### 5. `snapshot`
**Definition:** Returns data from a snapshot of the database at a specific point in time (used with transactions).  
**Example:**
```js
db.collection.find({}, { readConcern: { level: "snapshot" } })
```

---

## Write Concern Levels

### 1. `w: 1`
**Definition:** Write acknowledged by the primary only.  
**Example:**
```js
db.collection.insertOne({ name: "Alice" }, { writeConcern: { w: 1 } })
```

### 2. `w: "majority"`
**Definition:** Write acknowledged by the majority of replica set members.  
**Example:**
```js
db.collection.insertOne({ name: "Bob" }, { writeConcern: { w: "majority" } })
```

---

## Default Behavior

- **Default Read Concern:** `"local"` (returns data from the node, not guaranteed to be replicated)
- **Default Write Concern:** `w: 1` (write acknowledged by primary only)

---

**Note:**  
- Use `"majority"` for stronger consistency and durability.
- Use `"local"` or `"available"` for faster reads with less consistency.
- Use `"linearizable"` or `"snapshot"` for strict consistency