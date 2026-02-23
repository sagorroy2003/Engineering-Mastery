# ⚙️ 04. Backend Core & Clean Architecture

![Difficulty: Advanced](https://img.shields.io/badge/Difficulty-Advanced-orange?style=for-the-badge)
![Focus: Architecture](https://img.shields.io/badge/Focus-Architecture-blue?style=for-the-badge)
![Language: Node.js/Agnostic](https://img.shields.io/badge/Language-Node.js%2FAgnostic-brightgreen?style=for-the-badge)

A backend is the central nervous system of your application. If it is built poorly, no amount of frontend optimization will save you. 

---

## 🏛️ MVC vs. Clean Architecture

When designing an API, your structural decisions dictate long-term maintainability. Let's analyze the trade-offs.

### MVC (Model-View-Controller)
Great for MVPs, but business logic often bleeds into the Controller. The Controller becomes a "God File" that handles HTTP requests, database queries, and data formatting all at once.

### Clean Architecture (The Professional Approach)
We separate concerns into layers. The Controller handles HTTP. The Service handles Business Logic. The Repository handles the Database. 



**Trade-off:** Slower initial development speed due to boilerplate, but infinitely higher long-term scalability. You can swap out MongoDB for PostgreSQL without ever touching your Service logic.

---

## 🔍 Code Review: Refactoring a Bad Controller

Let's look at a typical junior-level Express.js route for a marketplace like `UniBazer`.

### ❌ The "Junior" Approach (Brute Force & Messy)
```javascript
// routes/products.js
app.post('/buy', async (req, res) => {
  // 1. Poor naming & missing validation
  let p = req.body.pId; 
  let u = req.body.uId;

  // 2. Business logic mixed with HTTP
  let product = await db.query(`SELECT * FROM products WHERE id = ${p}`);
  
  if(product.qty > 0) {
    // 3. No transaction wrapper! What if this fails?
    await db.query(`UPDATE products SET qty = qty - 1 WHERE id = ${p}`);
    await db.query(`INSERT INTO orders (uId, pId) VALUES (${u}, ${p})`);
    res.send("Success");
  } else {
    // 4. Inconsistent error responses
    res.status(400).send("Out of stock"); 
  }
});
