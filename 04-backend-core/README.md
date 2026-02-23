# ⚙️ 04. Backend Core & Clean Architecture

![Difficulty: Advanced](https://img.shields.io/badge/Difficulty-Advanced-orange?style=for-the-badge)
![Focus: Architecture & Optimization](https://img.shields.io/badge/Focus-Architecture_%26_Optimization-blue?style=for-the-badge)
![Language: Node.js/Agnostic](https://img.shields.io/badge/Language-Node.js%2FAgnostic-brightgreen?style=for-the-badge)

A backend is more than just a server responding to HTTP requests; it is the central nervous system of your application. It enforces business logic, ensures data integrity, and must scale to meet user demand. If your backend architecture is tightly coupled and poorly optimized, no amount of frontend caching will save your application from crashing under load.

---

## 🏛️ Architectural Trade-Offs: MVC vs. Clean Architecture

When designing a backend (e.g., in Node.js, Go, or Java), your primary structural decision dictates the future maintainability of your codebase.

### 1. MVC (Model-View-Controller)
* **How it works:** The Controller receives the request, processes the business logic, talks directly to the Model (database), and returns the View/Response.
* **The Trade-Off:** * *Pros:* High development speed. Easy to conceptualize. Great for fast MVPs.
  * *Cons:* Business logic bleeds into controllers. The Controller becomes a "God File" that is incredibly difficult to unit test because it is tightly coupled to the HTTP framework (like Express) and the Database (like Prisma or Mongoose).

### 2. Clean Architecture (Domain-Driven Design)
* **How it works:** We separate concerns into distinct layers. Dependencies point *inward* toward the core business logic.

* **The Trade-Off:**
  * *Pros:* Long-term scalability. Framework-agnostic. Highly testable. You can swap Express for Fastify, or PostgreSQL for MongoDB, without touching your core business rules.
  * *Cons:* Slower initial development speed due to boilerplate (Interfaces, DTOs, dependency injection).

---

## 🔍 Code Review: Refactoring a "Junior" Controller

Let's look at a typical route for a platform. We need an endpoint to process a student purchasing an item.

### ❌ The "Brute Force" Approach (Anti-Pattern)
```javascript
// routes/checkout.js
app.post('/buy', async (req, res) => {
  // 1. Poor naming & no validation
  let p = req.body.pId; 
  let u = req.body.uId;

  try {
    // 2. Business logic & DB queries mixed directly into the HTTP router
    let product = await db.query(`SELECT * FROM products WHERE id = ${p}`);
    
    if(product.qty > 0) {
      // 3. No transaction! If the insert fails, the stock is still decremented.
      await db.query(`UPDATE products SET qty = qty - 1 WHERE id = ${p}`);
      await db.query(`INSERT INTO orders (uId, pId) VALUES (${u}, ${p})`);
      res.send("Success");
    } else {
      res.status(400).send("Out of stock"); 
    }
  } catch (e) {
    // 4. Leaking raw DB errors to the client
    res.status(500).send(e.message);
  }
});
```

### ✅ The "Senior" Approach (Optimized & Resilient)
Here is how we architect this professionally using layered separation.

**1. The Controller (Handles HTTP only):**
```javascript
// controllers/OrderController.js
class OrderController {
  static async purchaseItem(req, res, next) {
    try {
      const { productId, buyerId } = req.body;
      
      // Basic validation
      if (!productId || !buyerId) {
        throw new AppError('Missing required fields: productId, buyerId', 400); 
      }

      // Delegate all business logic to the Service Layer
      const order = await OrderService.processPurchase(productId, buyerId);

      return res.status(201).json({ status: 'success', data: { order } });
    } catch (error) {
      next(error); // Pass to centralized error handler
    }
  }
}
```

**2. The Service (Handles Business Logic only):**
```javascript
// services/OrderService.js
class OrderService {
  static async processPurchase(productId, buyerId) {
    // Service doesn't know about HTTP or raw SQL. It uses Repositories.
    const product = await ProductRepository.findById(productId);
    
    if (!product || product.qty <= 0) {
      throw new AppError('Product is out of stock or unavailable', 409);
    }

    // Wrap in a transaction to ensure atomicity
    return await Database.transaction(async (trx) => {
      await ProductRepository.decrementStock(productId, 1, trx);
      return await OrderRepository.create({ buyerId, productId }, trx);
    });
  }
}
```

---

## 🚀 Algorithmic Optimization in the Backend

Do not just rely on your database to do the heavy lifting. As an engineer, you must process data efficiently in memory. 

Imagine you pull an array of 10,000 product objects and 5,000 vendor objects, and you need to merge them to send a complete feed to the frontend.

**The $O(n^2)$ Trap (Quadratic Time):**
```javascript
// ❌ Using find inside a map means for every product, we loop through vendors.
// 10,000 products * 5,000 vendors = 50,000,000 operations. Your server will stall.
const mergedFeed = products.map(product => {
  const vendor = vendors.find(v => v.id === product.vendorId);
  return { ...product, vendor };
});
```

**The $O(n)$ Solution (Linear Time):**
Challenge yourself to optimize this just like a technical interview. We can use a Hash Map to bring the complexity down to $O(n)$.
```javascript
// ✅ Build a Hash Map first: O(v) time
const vendorMap = new Map();
vendors.forEach(v => vendorMap.set(v.id, v));

// Map the products: O(p) time. Total time: O(v + p) -> O(n)
const mergedFeed = products.map(product => ({
  ...product,
  vendor: vendorMap.get(product.vendorId) || null
}));
```

---

## 🛡️ Centralized Error Handling

Never sprinkle `res.status(500).send(err)` throughout your codebase. 

1. Create a custom `AppError` class that extends the native `Error` object. Include an `isOperational` flag.
2. If an error is operational (e.g., "User not found"), send a clean message to the client.
3. If an error is a programmer bug (e.g., "Cannot read properties of undefined"), log the stack trace to your server logs, and send a generic "Internal Server Error" to the client so you do not leak sensitive infrastructure details.

---

## 🎯 Practical Exercises

1. **Refactor a Monolith:** Take an old Express or Flask route you wrote that touches the database directly. Refactor it into a Controller, a Service, and a Repository.
2. **Optimize a Route:** Find an endpoint that returns a list of items and their associated owners. Ensure you are not running an $N+1$ query problem (querying the DB inside a loop). Fix it using a SQL `JOIN` or an in-memory Hash Map.
3. **Mini Project:** Build an API with two endpoints: `POST /records` and `GET /records/search`. Implement pagination and filtering on the search route. Use Clean Architecture.

## 🎤 Interview Questions
* *"Explain the difference between a PUT and a PATCH request."*
* *"What is Idempotency in the context of REST APIs, and which HTTP methods should be idempotent?"*
* *"You noticed a specific API route is taking 4 seconds to respond. Walk me through exactly how you would debug and optimize this bottleneck, from the Node.js event loop down to the database level."*
