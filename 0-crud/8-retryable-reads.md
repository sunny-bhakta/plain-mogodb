# MongoDB Retryable Read Operations

**Definition:**  
Retryable reads are read operations (find, aggregate, count, etc.) that MongoDB drivers can automatically retry once in case of transient network errors or replica set failover. This helps ensure reliability and availability for applications.

---

## Example: Retryable Find

```js
db.collection.find(
  { status: "active" },
  { retryReads: true }
)
```

---

## Example: Retryable Aggregate

```js
db.collection.aggregate(
  [{ $match: { status: "active" } }],
  { retryReads: true }
)
```

---

**Note:**  
- Retryable reads are enabled by default in most MongoDB drivers.
- The server must be a replica set or sharded cluster for retryable reads to work.