```js
// Switch to the 'aggregate' database
use("aggregate")

// Insert sample orders
db.orders.insertMany([
  {
    "_id": 1,
    "customerId": 101,
    "items": [
      { "productId": 201, "qty": 2 },
      { "productId": 202, "qty": 1 }
    ],
    "orderDate": ISODate("2023-08-01T10:00:00Z"),
    "status": "shipped"
  },
  {
    "_id": 2,
    "customerId": 102,
    "items": [
      { "productId": 201, "qty": 1 }
    ],
    "orderDate": ISODate("2023-08-03T12:30:00Z"),
    "status": "processing"
  },
  {
    "_id": 3,
    "customerId": 101,
    "items": [
      { "productId": 203, "qty": 4 }
    ],
    "orderDate": ISODate("2023-08-02T09:00:00Z"),
    "status": "delivered"
  }
])

// Insert sample products
db.products.insertMany([
  { "_id": 201, "name": "Laptop", "price": 1000, "category": "Electronics" },
  { "_id": 202, "name": "Mouse", "price": 50, "category": "Electronics" },
  { "_id": 203, "name": "Chair", "price": 150, "category": "Furniture" }
])

// Aggregation pipeline to analyze customer orders and spending
db.orders.aggregate([
    // 1. Unwind items array to process each item separately
    { $unwind: "$items" },
    // Output example:
    // {
    //   "_id": 1,
    //   "customerId": 101,
    //   "items": { "productId": 201, "qty": 2 },
    //   "orderDate": ISODate("2023-08-01T10:00:00Z"),
    //   "status": "shipped"
    // }

    // 2. Lookup product details for each item
    {
        $lookup: {
            from: "products",
            localField: "items.productId",
            foreignField: "_id",
            as: "productDetails"
        }
    },
    // Output example:
    // {
    //   "_id": 1,
    //   "customerId": 101,
    //   "items": { "productId": 201, "qty": 2 },
    //   "productDetails": [{ "_id": 201, "name": "Laptop", ... }],
    //   ...
    // }

    // 3. Unwind productDetails array
    { $unwind: "$productDetails" },
    // Output example:
    // {
    //   "_id": 1,
    //   "customerId": 101,
    //   "items": { "productId": 201, "qty": 2 },
    //   "productDetails": { "_id": 201, "name": "Laptop", ... },
    //   ...
    // }

    // 4. Add product name, category, and total price to each item
    {
        $addFields: {
            "items.productName": "$productDetails.name",
            "items.category": "$productDetails.category",
            "items.totalPrice": { $multiply: ["$items.qty", "$productDetails.price"] }
        }
    },
    // Output example:
    // {
    //   "_id": 1,
    //   "customerId": 101,
    //   "items": {
    //     "productId": 201,
    //     "qty": 2,
    //     "productName": "Laptop",
    //     "category": "Electronics",
    //     "totalPrice": 2000
    //   },
    //   ...
    // }

    // 5. Group back to order level, collecting items and calculating order total
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
    // Output example:
    // {
    //   "_id": 1,
    //   "customerId": 101,
    //   "orderDate": ISODate("2023-08-01T10:00:00Z"),
    //   "status": "shipped",
    //   "items": [ ... ],
    //   "orderTotal": 2050
    // }

    // 6. Group by customer, summing total spent and collecting orders
    {
        $group: {
            _id: "$customerId",
            totalSpent: { $sum: "$orderTotal" },
            orders: {
                $push: {
                    orderId: "$_id",
                    orderDate: "$orderDate",
                    status: "$status",
                    items: "$items",
                    orderTotal: "$orderTotal"
                }
            }
        }
    },
    // Output example:
    // {
    //   "_id": 101,
    //   "totalSpent": 2050,
    //   "orders": [ ... ]
    // }

    // 7. Filter customers who spent more than $1000
    {
        $match: {
            totalSpent: { $gt: 1000 }
        }
    },
    // Output example:
    // {
    //   "_id": 101,
    //   "totalSpent": 2050,
    //   "orders": [ ... ]
    // }

    // 8. Sort customers by total spent descending
    {
        $sort: { totalSpent: -1 }
    },
    // Output example:
    // [
    //   { "_id": 101, "totalSpent": 2050, ... }
    // ]

    // 9. Project desired fields and calculate order count and first order date
    {
        $project: {
            _id: 0,
            customerId: "$_id",
            totalSpent: 1,
            orders: 1,
            orderCount: { $size: "$orders" },
            firstOrderDate: {
                $dateToString: {
                    format: "%Y-%m-%d",
                    date: { $min: "$orders.orderDate" }
                }
            }
        }
    },
    // Output example:
    // {
    //   "customerId": 101,
    //   "totalSpent": 2050,
    //   "orders": [ ... ],
    //   "orderCount": 2,
    //   "firstOrderDate": "2023-08-01"
    // }

    // 10. Pagination: skip 0, limit 5
    { $skip: 0 },
    { $limit: 5 },
    // Output example:
    // [
    //   { "customerId": 101, ... }
    // ]

    // 11. Add customer type based on total spent
    {
        $addFields: {
            customerType: {
                $cond: {
                    if: { $gte: ["$totalSpent", 2000] },
                    then: "Platinum",
                    else: "Gold"
                }
            }
        }
    },
    // Output example:
    // {
    //   "customerId": 101,
    //   "totalSpent": 2050,
    //   "customerType": "Platinum",
    //   ...
    // }

    // 12. Facet to get customers and total count
    {
        $facet: {
            customers: [{ $match: {} }],
            totalCount: [{ $count: "count" }]
        }
    }
    // Output example:
    // {
    //   "customers": [ { ... } ],
    //   "totalCount": [ { "count": 1 } ]
    // }
])
```
