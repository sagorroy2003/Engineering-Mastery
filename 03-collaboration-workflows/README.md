# 🤝 03. Collaboration & Open Source Workflows

![Difficulty: Intermediate](https://img.shields.io/badge/Difficulty-Intermediate-yellow?style=for-the-badge)
![Focus: Process](https://img.shields.io/badge/Focus-Process-blue?style=for-the-badge)
![Status: Essential](https://img.shields.io/badge/Status-Essential-success?style=for-the-badge)

Building a complex platform like a multi-vendor university marketplace cannot happen in a silo. Top-tier software agencies expect you to understand branching strategies and semantic versioning before you even touch the codebase.

---

## 🛤️ Branching Strategies: GitFlow vs. Trunk-Based Development

You must choose a branching strategy based on your team size and deployment frequency. Here is the trade-off analysis:

### 1. GitFlow (The Enterprise Standard)
* **How it works:** Uses strict branches (`main`, `develop`, `feature/*`, `release/*`, `hotfix/*`).
* **Trade-offs:** * *Pros:* Extremely safe. Great for massive open-source projects or enterprise applications with scheduled release cycles.
  * *Cons:* Slow development speed. "Merge hell" can occur if feature branches live too long.

### 2. Trunk-Based Development (The Agile Standard)
* **How it works:** Developers push small, frequent updates directly to `main` (the trunk) or very short-lived feature branches.
* **Trade-offs:**
  * *Pros:* High development speed. Continuous Integration (CI) is truly continuous. 
  * *Cons:* Requires a high level of automated testing. If your tests fail, the trunk breaks.



---

## 🏷️ Writing Semantic Commits

Your commit history should read like a changelog. Use the **Conventional Commits** specification.

* `feat(auth): add JWT expiration refresh token` (New feature)
* `fix(cart): resolve undefined price calculation` (Bug fix)
* `refactor(db): optimize vendor search query` (Code change that neither fixes a bug nor adds a feature)
* `perf(api): reduce product fetch time` (A code change that improves performance)

---

## 🧑‍🏫 The "Senior Engineer" Code Review Checklist

When you review code (or your own PRs), do not just approve syntax. Review the architecture.

1. **Naming Conventions:** Are variables descriptive? (e.g., change `let d = res.data` to `let vendorProductList = res.data`).
2. **Algorithmic Complexity:** Is the author using a nested loop to compare two arrays? Challenge them to use a Hash Map or `Set` to drop the complexity from $O(n^2)$ to $O(n)$.
3. **Error Handling:** Did they wrap their async database calls in a `try/catch` block? Are they throwing custom error classes or just generic HTTP 500s?

---

## 🎯 Practical Exercises
1. Set up a GitFlow structure in a dummy repository. Create a `feature/vendor-dashboard` branch, make changes, and merge it into `develop`.
2. Find a massive open-source repository (like React or Node.js) and read their closed Pull Requests to see how professionals discuss code.
