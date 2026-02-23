# 🗄️ 05. Database Design & Optimization

![Difficulty: Advanced](https://img.shields.io/badge/Difficulty-Advanced-orange?style=for-the-badge)
![Focus: Data Architecture](https://img.shields.io/badge/Focus-Data_Architecture-blue?style=for-the-badge)
![Status: Essential](https://img.shields.io/badge/Status-Essential-success?style=for-the-badge)

A beautifully architected Node.js backend means absolutely nothing if your database takes 5 seconds to execute a query. Database design is where you win or lose the scalability battle. 

---

## ⚖️ Relational (SQL) vs. NoSQL: The Real Trade-Off

Junior developers often pick MongoDB because "it's easy to set up." Senior engineers choose their database based on the data shape and access patterns.

* **Relational (PostgreSQL, MySQL):** Use when data is highly structured and relational (e.g., A User has many Orders, an Order has many Products). Enforces strict ACID compliance. Best for marketplaces, financial systems, and most general SaaS.
* **NoSQL (MongoDB, DynamoDB):** Use for unstructured data, rapid schema iteration, or massive horizontal write-scaling (e.g., IoT sensor data, event logging, document stores).

---

## 🗺️ Real-World Schema Design: Multi-Vendor Marketplace

Consider a university marketplace where students can both buy and sell. We need a robust relational schema to handle Users, Vendors (a specialized subset of Users), Products, and Orders.

### ASCII ER Diagram

```text
+----------------+       +------------------+       +----------------+
|     USERS      |       |     VENDORS      |       |    PRODUCTS    |
+----------------+       +------------------+       +----------------+
| id (PK)        |<------| user_id (PK/FK)  |       | id (PK)        |
| email          |       | store_name       |<------| vendor_id (FK) |
| password_hash  |       | verify_status    |       | title          |
| created_at     |       +------------------+       | price          |
+----------------+                                  +----------------+
        ^
        |                +------------------+
        |                |      ORDERS      |
        +----------------| id (PK)          |
                         | buyer_id (FK)    |
                         | product_id (FK)  |
                         | status           |
                         +------------------+
```

*Architectural Note:* Notice how `VENDORS` shares a primary key with `USERS` (`user_id`). This is a 1-to-1 relationship. Not all users are vendors, so separating this prevents our `USERS` table from being bloated with NULL columns.

---

## 🚀 Indexing: From $O(n)$ to $O(\log n)$

This is the most critical concept in database performance.

Imagine your marketplace grows to 5 million products. A user searches for a product by its exact title:
`SELECT * FROM products WHERE title = 'Engineering Calculus 101';`

### The Brute Force Reality (Sequential Scan)
Without an index, the database must check every single row. This is an $O(n)$ operation. As your table grows, your server dies.

### The Optimized Reality (B-Tree Index)
When you add an index to the `title` column, the database builds a separate data structure (usually a B-Tree). 


Searching a B-Tree operates in $O(\log n)$ time. Finding 1 specific row out of 5 million drops from 5,000,000 operations to roughly ~23 operations.

```sql
-- ✅ Creating an index to optimize search performance
CREATE INDEX idx_products_title ON products(title);
```

**⚠️ The Trade-Off:** Why not index every column? Because every time you `INSERT`, `UPDATE`, or `DELETE` a row, the database must recalculate and rebalance the B-Tree index. 
* **Rule of Thumb:** Index your Primary Keys (done automatically), Foreign Keys, and columns frequently used in `WHERE`, `ORDER BY`, or `JOIN` clauses. Do not over-index write-heavy tables.

---

## 💥 The N+1 Query Problem

A common mistake when using ORMs (like Prisma or TypeORM) is accidentally executing queries inside a loop.

**❌ The Anti-Pattern:**
```javascript
// 1 query to get 100 products
const products = await db.query('SELECT * FROM products LIMIT 100');

for (let product of products) {
  // 100 separate queries to get the vendor for each product!
  // Total queries: 101 (The N+1 problem)
  product.vendor = await db.query(`SELECT * FROM vendors WHERE id = ${product.vendorId}`);
}
```

**✅ The Professional Fix (SQL JOIN):**
Let the database do the relational heavy lifting in a single, optimized pass.
```sql
SELECT p.*, v.store_name 
FROM products p
JOIN vendors v ON p.vendor_id = v.user_id
LIMIT 100;
```

---

## 🛡️ ACID Transactions

When money or inventory is involved, operations must be **Atomic** (all succeed, or all fail). If a buyer purchases a product, you must:
1. Deduct the buyer's balance.
2. Decrease the product inventory.
3. Create an order record.

If step 3 fails but steps 1 and 2 succeeded, you have corrupted data. Wrap them in a transaction:

```sql
BEGIN;
  UPDATE users SET balance = balance - 50 WHERE id = 1;
  UPDATE products SET stock = stock - 1 WHERE id = 99;
  INSERT INTO orders (buyer_id, product_id) VALUES (1, 99);
COMMIT; -- If anything fails before this, run ROLLBACK;
```

---

## 🎯 Practical Exercises

1. **Query Analysis:** Open your database CLI and use `EXPLAIN ANALYZE` (PostgreSQL) or `EXPLAIN` (MySQL) on a complex query. Read the execution plan to see if it's doing a "Seq Scan" (Sequential Scan) or an "Index Scan".
2. **Schema Design Challenge:** Design the ER diagram for a "Chat/Messaging" feature between a Buyer and a Vendor. How do you handle read receipts efficiently?
3. **Migration Practice:** Never use `ALTER TABLE` manually in production. Setup a migration tool (like Knex or Prisma Migrate) and write a migration script to add a `discount_price` column to a products table safely.

## 🎤 Interview Questions
* *"Explain the difference between First Normal Form (1NF), 2NF, and 3NF. When would you intentionally denormalize a database?"*
* *"What is a Composite Index, and how does the order of columns in a composite index affect its usage?"*
* *"You have a query that is joining 4 large tables and taking 10 seconds to run. Walk me through your exact steps to optimize it."*
