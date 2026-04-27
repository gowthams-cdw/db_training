1. Create a database called shopDB and switch to it.

```
use shopDB
```

---

1. Insert a single product document into a products collection with the following fields:

- name : "Wireless Mouse"
- brand: "Logitech"
- price: 29.99
- category: "Electronics"
- stock: 150
- rating: 4.5

```
db.products.insertOne({
	name : "Wireless Mouse",
	brand: "Logitech",
	price: 29.99,
	category: "Electronics",
	stock: 150,
	rating: 4.5,
})
```

---

1. Insert the following three products in one command:

- { name: "Mechanical Keyboard", brand: "Corsair", price: 89.99, category: "Electronics", stock: 60, rating: 4.7 }
- { name: "USB-C Hub", brand: "Anker", price: 35.00, category: "Electronics", stock: 200, rating: 4.3 }
- { name: "Desk Lamp", brand: "BenQ", price: 109.99, category: "Office", stock: 45, rating: 4.6 }

```
db.products.insertMany(
    [
        {
            name: "Mechanical Keyboard",
            brand: "Corsair",
            price: 89.99,
            category: "Electronics",
            stock: 60,
            rating: 4.7,
        },
        {
            name: "USB-C Hub",
            brand: "Anker",
            price: 35.0,
            category: "Electronics",
            stock: 200,
            rating: 4.3,
        },
        {
            name: "Desk Lamp",
            brand: "BenQ",
            price: 109.99,
            category: "Office",
            stock: 45,
            rating: 4.6,
        },
    ]
)
```

---

4. Retrieve all documents from the products collection.

```
db.products.find({})
```

---

5. Find only the product named "Wireless Mouse"

```
db.products.find({ name: "Wireless Mouse" })
```

---

6. Update the stock of "USB-C Hub" to 180 .

```
db.products.updateMany({ name: "USB-C Hub" }, { $set: { stock: 180 } })
```

---

7. Add a new field "onsale": true to all products in the "Electronics" category

```
db.products.updateMany({ category: "Electronics" }, { $set: { onsale: true } })
```

---

8. Delete the document where the name is "Desk Lamp" .

```
db.products.deleteMany({ name: "Desk Lamp" })
```

---

9. Find all products with a price greater than 30•

```
db.products.find({ price: { $gt: 30 } })
```

---

10. Find all products with a rating between 4.4 and 4.8 (inclusive).

```
db.products.find({ rating: { $gte: 4.4, $lte: 4.8 } })
```

---

11. Find all products that belong to either the "Electronics" or "Office" category.

```
db.products.find({ category: { $in: ["Electronics", "Office"] } })
```

---

12. Find all products where the field "onSale" exists.

```
db.products.find({ onSale: { $exists: true } })
```

---

13. Find all products with price less than stock fields 100 AND stock greater than 100. Return only the name, price (exclude \_id)

```
db.products.find(
    {
      "$and": [
        { price: { "$lt": 100 } },
        { stock: { "$gt": 100 } }
      ]
    },
    {
        name: 1,
        price: 1,
    }
)
```

---

14. Retrieve all products sorted by price in ascending order.

```
db.products.find({}).sort({ price: 1 })
```

---

15. Retrieve all products sorted by rating in descending order, but show only the top 2.

```
db.products.find({}).sort({ rating: -1 }).limit(2)
```

---

16. Skip the first result and return the next 2 products sorted by price ascending. (Simulate page 2 of a results list.)

```
db.products.find({}).sort({ price: 1 }).skip(1).limit(2)
```

---

17. Create an index on the name field of the products collection.

```
db.products.createIndex({ name: 1 })
```

---

18. Create a unique index on the name field. What happens if you try to insert a duplicate product name after creating it?

```
# MongoServerError: E11000 duplicate key error collection
```

---

19. Count the total number of employees grouped by their department.

```
db.products.aggregate(
    [
        {
            $group: {
               _id: "$department",
               count: { $sum: 1 }
            }
        }
    ]
)
```

---

20. Find the average salary for each department, and sort the results from highest to lowest average.

```
db.products.aggregate(
    [
        {
            $group: {
                _id: "$department",
                avgSalary: { $avg: "$salary" }
            }
        }
    ]
).sort({ avgSalary: -1 })
```

---

21. Filter only "active" employees, then group by department to find the total salary budget per department.

```
db.products.aggregate(
    [
        {
            $match: {
                status: "active"
            }
        },
        {
            $group: {
                _id: "$department",
                totalSalary: { $sum: "$salary" }
            }
        }
    ]
)
```

---

22. Find the top 3 highest-paid employees. Return only their name, role, department, and salary.

```
db.products.aggregate(
    [
        {
            $sort: { salary: -1 }
        },
        {
            $limit: 3
        },
        {
            $project: {
                name: 1,
                role: 1,
                department: 1,
                salary: 1,
                _id: 0
            }
        }
    ]
)
```

---

23. Group employees by department and count how many are in each. Then sort by count descending, and only return departments with more than 2 employees.

```
db.products.aggregate(
    [
        {
            $group: {
                _id: "$department",
                count: { $sum: 1 }
            }
        },
        {
            $sort: { count: -1 }
        },
        {
            $match: { count: { $gt: 2 } }
        },
        {
            $project: {
                department: "$_id",
                _id: 0
            }
        }
    ]
)
```

---

24. For each department, find the maximum salary and minimum salary.

```
db.products.aggregate(
    [
        {
            $group: {
                _id: "$department",
                minSalary: { $min: "$salary" },
                maxSalary: { $max: "$salary" },
            }
        },
        {
            $project: {
                department: "$_id",
                minSalary: 1,
                maxSalary: 1,
                _id: 0
            }
        }
    ]
)
```

---

25. Explore commands lookup, unwind & watch.

```
#lookup -> performs outter join (from, localField, foreignField, as)
#unwind -> give individual docs for each item in array
#watch -> watches for specific event listener and do action (db.collection.watch())
```

***
