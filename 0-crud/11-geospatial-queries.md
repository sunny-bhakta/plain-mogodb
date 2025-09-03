# MongoDB Geospatial Queries – Summary & Key Concepts

## 🔧 Compatibility

Geospatial queries are supported in:
- **MongoDB Atlas** (Cloud)
- **MongoDB Enterprise** (Self-managed, subscription-based)
- **MongoDB Community** (Self-managed, free)

---

## 🗺️ Geospatial Data Types

### 1. **GeoJSON Objects** *(Recommended for spherical calculations)*
- **Format:**
    ```json
    <field>: {
        "type": <GeoJSON type>,
        "coordinates": [ <longitude>, <latitude> ]
    }
    ```
- **Example:**
    ```json
    location: {
        "type": "Point",
        "coordinates": [ -73.856077, 40.848447 ]
    }
    ```
- Follows WGS84 reference system (Earth-like sphere)
- Used with **2dsphere** indexes

### 2. **Legacy Coordinate Pairs**
- Flat surface (Euclidean plane)
- **Format (preferred):**
    ```json
    <field>: [ <longitude>, <latitude> ]
    ```
- **Alternative:**
    ```json
    <field>: { "lng": <longitude>, "lat": <latitude> }
    ```
- Used with **2d** indexes

---

## 📦 Geospatial Indexes

### 🧭 **2dsphere**
- For spherical geometry
- Supports GeoJSON and legacy coordinates
- **Create index:**
    ```js
    db.collection.createIndex({ location: "2dsphere" })
    ```

### 🔲 **2d**
- For flat geometry (legacy)
- Use for legacy coordinate pairs
- **Create index:**
    ```js
    db.collection.createIndex({ location: "2d" })
    ```

---

## 🔍 Geospatial Query Operators

| Operator        | Description                                      | Index Type         |
|-----------------|--------------------------------------------------|--------------------|
| `$geoIntersects`| Finds geometries intersecting with a GeoJSON obj | 2dsphere           |
| `$geoWithin`    | Finds geometries within a specified geometry     | 2dsphere, 2d       |
| `$near`         | Finds documents near a point                     | 2dsphere, 2d       |
| `$nearSphere`   | Like `$near`, but assumes spherical geometry     | 2dsphere, 2d       |

> **Note:** Time series collections support only `$geoNear` (not `$near` or `$nearSphere`).

---

## 🧪 Geospatial Aggregation

### `$geoNear` Stage
- Returns documents sorted by distance
- Requires a geospatial index

**Example:**
```js
db.collection.aggregate([
    {
        $geoNear: {
            near: { type: "Point", coordinates: [ -73.9667, 40.78 ] },
            spherical: true,
            query: { category: "Parks" },
            distanceField: "calcDistance"
        }
    }
])
```

---

## 🧠 Geometry Models

| Operator & Index Type                | Geometry Model | Notes                        |
|--------------------------------------|---------------|------------------------------|
| `$near`, `$nearSphere` + GeoJSON/2dsphere | Spherical      | Preferred for Earth-like accuracy |
| `$near`, `$geoWithin`, `$geoNear` + legacy/2d | Flat           | Suitable for simple 2D use cases  |

---

## 🧪 Example Setup

**Collection:**
```js
db.places.insertMany([
    {
        name: "Central Park",
        location: { type: "Point", coordinates: [ -73.97, 40.77 ] },
        category: "Parks"
    },
    {
        name: "Sara D. Roosevelt Park",
        location: { type: "Point", coordinates: [ -73.9928, 40.7193 ] },
        category: "Parks"
    },
    {
        name: "Polo Grounds",
        location: { type: "Point", coordinates: [ -73.9375, 40.8303 ] },
        category: "Stadiums"
    }
])
```

**Create Index:**
```js
db.places.createIndex({ location: "2dsphere" })
```

### 🔍 Example Geospatial Queries

#### **Query – Find places near a point (within 1000–5000 meters):**
```js
db.places.find({
    location: {
        $near: {
            $geometry: { type: "Point", coordinates: [ -73.9667, 40.78 ] },
            $minDistance: 1000,
            $maxDistance: 5000
        }
    }
})
```

#### **Aggregation – Find nearby parks:**
```js
db.places.aggregate([
    {
        $geoNear: {
            near: { type: "Point", coordinates: [ -73.9667, 40.78 ] },
            spherical: true,
            query: { category: "Parks" },
            distanceField: "calcDistance"
        }
    }
])
```

## Using Geospatial Queries in MongoDB Atlas (UI)

### Query Filter Bar

1. Go to the collection.
2. Create a geospatial index.
3. Use `$geoWithin`, `$near`, etc. in the filter bar.

**Example:**
```json
{
    coordinates: {
        $geoWithin: {
            $geometry: {
                type: "Polygon",
                coordinates: [[[-80,10], [-80,9], [-79,9], [-79,10], [-80,10]]]
            }
        }
    }
}
```

### Aggregation View

1. Add `$geoNear` stage.
2. Fill with location and query parameters.
3. Run and refine stages.

---

## ✅ Quick Recommendations

| Task                       | Recommended Approach           |
|----------------------------|-------------------------------|
| Earth-like location queries| Use GeoJSON + 2dsphere        |
| Flat 2D coordinate queries | Use legacy coords + 2d        |
| Sorting by proximity       | Use `$geoNear` aggregation    |
| Filtering within areas     | Use `$geoWithin`              |
| Finding intersections      | Use `$geoIntersects`          |
