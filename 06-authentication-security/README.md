# 🔒 06. Authentication, Security & RBAC

![Difficulty: Advanced](https://img.shields.io/badge/Difficulty-Advanced-orange?style=for-the-badge)
![Focus: Security Architecture](https://img.shields.io/badge/Focus-Security_Architecture-blue?style=for-the-badge)
![Status: Critical](https://img.shields.io/badge/Status-Critical-red?style=for-the-badge)

Security is not a feature you add at the end of a sprint; it is an architectural foundation. A single compromised vendor account or an exposed `.env` file can destroy the reputation of an entire marketplace. 

As a senior engineer, you must understand the difference between Authentication (AuthN) and Authorization (AuthZ), and how to defend against automated attacks.

---

## 🛡️ Authentication vs. Authorization

* **Authentication (AuthN):** *Who are you?* (Verifying identity via passwords, OAuth, or biometrics).
* **Authorization (AuthZ):** *What are you allowed to do?* (Checking if the authenticated user has the permissions to delete a product or view a financial dashboard).

---

## ⚖️ Architectural Trade-Off: JWT vs. Session-Based Auth

When building an API, how do you remember that a user is logged in? 



### 1. Session-Based Authentication (Stateful)
* **How it works:** The server verifies credentials, creates a "Session ID", stores it in a database or Redis cache, and sends the ID back to the client in an `HttpOnly` cookie.
* **The Trade-Off:** * *Pros:* Easy to revoke. If a user acts maliciously, you simply delete their Session ID from Redis, and they are instantly logged out.
  * *Cons:* Harder to scale horizontally. If you have 5 backend servers, they all need access to the centralized Redis instance to check if the session is valid.

### 2. JSON Web Tokens (JWT) (Stateless)
* **How it works:** The server verifies credentials and signs a JSON payload with a secret key (using algorithms like HMAC SHA-256). It sends this token to the client. The server does *not* store the token.
* **The Trade-Off:**
  * *Pros:* Infinitely scalable. The server doesn't need to look up the database to verify the token; it just recalculates the cryptographic signature. Great for microservices.
  * *Cons:* **Nearly impossible to revoke.** Because the server doesn't store state, a JWT is valid until its expiration time. If a hacker steals a JWT, they have access until it expires, unless you implement a complex "Denylist" architecture.

---

## 🔑 The Mathematics of Password Hashing

Never, ever store plain-text passwords. But simply encrypting them isn't enough either.

**❌ The Junior Mistake:** Using MD5 or SHA-256. These are *fast* cryptographic hashes. A modern GPU can calculate billions of SHA-256 hashes per second. If your database leaks, hackers will use a "Rainbow Table" (a massive pre-computed list of hashes) to crack your users' passwords in minutes.

**✅ The Senior Solution:** Use **Bcrypt** or **Argon2**. These algorithms are intentionally designed to be *computationally expensive* and *slow*. 
* **Salting:** Bcrypt automatically generates a random string (a "salt") and appends it to the password before hashing. This completely defeats Rainbow Tables.
* **Work Factor (Cost):** You define how many computational rounds the algorithm runs. By increasing the cost (e.g., `12`), hashing a single password might take 200 milliseconds. This is unnoticeable to a user logging in, but it makes brute-forcing a leaked database mathematically unfeasible.

---

## 🛂 Role-Based Access Control (RBAC)

In a complex platform like a university marketplace, you have strictly delineated roles: `STUDENT_BUYER`, `VENDOR`, and `SUPER_ADMIN`. 

You must enforce AuthZ at the middleware level, *before* the request ever hits your controller logic.

```javascript
// middleware/authorize.js

// 1. Verify the JWT first (AuthN)
const requireAuth = async (req, res, next) => { /* ... decodes JWT and attaches to req.user ... */ };

// 2. Check the Role (AuthZ)
const authorizeRoles = (...allowedRoles) => {
  return (req, res, next) => {
    // If the user's role isn't in the allowed list, block the request.
    if (!req.user || !allowedRoles.includes(req.user.role)) {
      return res.status(403).json({ 
        status: 'fail', 
        message: 'Forbidden: You lack the required permissions.' 
      });
    }
    next();
  };
};

// Usage in an Express Router:
// Only VENDORS and SUPER_ADMINS can create products. Buyers cannot.
app.post('/api/products', requireAuth, authorizeRoles('VENDOR', 'SUPER_ADMIN'), ProductController.create);
```

---

## 🦠 Defending Against Common Vulnerabilities

### 1. SQL Injection (SQLi)
* **The Attack:** An attacker enters `' OR 1=1 --` into an email login field. If you concatenate raw SQL strings, the query evaluates to `TRUE` and logs them in as the first user in the database (usually the Admin).
* **The Defense:** Always use **Parameterized Queries** or an ORM (like Prisma). This treats user input as literal text, not executable code.

### 2. Cross-Site Scripting (XSS)
* **The Attack:** An attacker leaves a product review containing a malicious `<script>` tag. When another user views the product, the script runs in their browser and steals their `localStorage` tokens.
* **The Defense:** Sanitize all user input before saving it to the database. If using React, it automatically escapes variables, but *never* use `dangerouslySetInnerHTML` with unsanitized data.

### 3. Cross-Site Request Forgery (CSRF)
* **The Attack:** An attacker tricks an authenticated user into clicking a link (e.g., `http://yourbank.com/transfer?amount=1000&to=hacker`). Because the user's browser automatically attaches their session cookies, the transfer succeeds.
* **The Defense:** Use `SameSite=Strict` attributes on your cookies, or implement Anti-CSRF tokens for mutating requests (POST, PUT, DELETE).

---

## 🎯 Practical Exercises
1. **Security Code Review:** Find an old login system you built. Are you returning the hashed password in the JSON response? Are you using `bcrypt`? Refactor it.
2. **Build an RBAC System:** Create a Node.js API with a `users` table that includes a `role` enum. Write a middleware that successfully blocks a `STUDENT` from accessing a `GET /api/admin/stats` route.
3. **Implement Refresh Tokens:** Standard JWTs should expire in 15 minutes for security. Implement a longer-lived "Refresh Token" (stored securely in an `HttpOnly` cookie) that can automatically request a new short-lived JWT without the user having to re-login.

## 🎤 Interview Questions
* *"What is the exact sequence of events that happens when a user logs in via JWT, and how do you handle the token being compromised?"*
* *"Explain the difference between `localStorage` and an `HttpOnly` cookie. Where should you store sensitive authentication tokens and why?"*
* *"A junior developer suggests we speed up our login route by changing our Bcrypt salt rounds from 12 to 1. Walk me through how you would explain why this is a catastrophic idea."*
