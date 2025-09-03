## MongoDB Document Deletion Methods

### `findOneAndDelete()`
- Deletes a single document and returns it.
- Supports a `sort` option to specify the order in which documents are considered for deletion.
- Example:
    ```js
    db.collection.findOneAndDelete(
        { status: "inactive" },
        { sort: { createdAt: 1 } }
    );
    ```

### `findAndModify()`
- Can be used to delete a document, update it, or return it.
- Also supports a `sort` option for determining which document to modify or delete.
- Example:
    ```js
    db.collection.findAndModify({
        query: { status: "inactive" },
        sort: { createdAt: 1 },
        remove: true
    });
    ```

### `bulkWrite()`
- Allows multiple write operations (insert, update, delete) in a single call.
- Useful for batch deletions or modifications.
- Example:
    ```js
    db.collection.bulkWrite([
        { deleteOne: { filter: { status: "inactive" } } },
        { deleteMany: { filter: { expired: true } } }
    ]);
    ```
