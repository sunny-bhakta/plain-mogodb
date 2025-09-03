# MongoDB Text Search & Atlas Vector Search

---

## 1. Text Search Definition

**Text search** in MongoDB allows you to search for string values within documents using text indexes. It supports searching for words, phrases, and can rank results by relevance.

**Example:**
```js
db.collection.createIndex({ description: "text" })
db.collection.find({ $text: { $search: "quick brown fox" } })
```

---

## 2. Atlas Vector Search Quick Start

**Atlas Vector Search** enables searching for documents based on semantic meaning using vector embeddings (from ML models). This is useful for semantic search, recommendations, and AI-powered queries.

**Quick Start Example:**
```js
// Create a vector search index in Atlas UI or API

// Insert a document with a vector field
db.collection.insertOne({
  text: "MongoDB Atlas is a developer data platform.",
  embedding: [0.12, 0.98, ...] // Example vector
})
```

---

## 3. Atlas Semantic Text Search

**Semantic text search** uses vector embeddings to find documents with similar meaning, not just keyword matches.

**Example:**
```js
db.collection.aggregate([
  {
    $vectorSearch: {
      index: "vector_index",
      queryVector: [0.11, 0.97, ...], // Query embedding
      path: "embedding",
      numCandidates: 100,
      limit: 5
    }
  }
])
```

---

## 4. Run Vector Search Queries Example

**Example:**
```js
db.collection.aggregate([
  {
    $vectorSearch: {
      index: "vector_index",
      queryVector: [0.15, 0.88, ...],
      path: "embedding",
      numCandidates: 50,
      limit: 10
    }
  }
])
```

---

**Note:**  
- Text search uses `$text` and text indexes.
- Vector search uses `$vectorSearch` and vector indexes (Atlas only).
- For semantic search, you need to generate vector embeddings using ML models (like OpenAI, HuggingFace,

- You can use the `$meta` operator to project the relevance score of text search results.

**Example:**
```js
db.collection.find(
    { $text: { $search: "quick brown fox" } },
    { score: { $meta: "textScore" } }
).sort({ score: { $meta: "textScore" } })
```