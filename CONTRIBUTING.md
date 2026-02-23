# 🤝 Contributing to Engineering Mastery

![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=for-the-badge)
![Code Standard: Strict](https://img.shields.io/badge/Code_Standard-Strict-red?style=for-the-badge)

First off, thank you for considering contributing to **Engineering Mastery**. 

This repository operates under the strict engineering standards expected at top-tier software agencies. We prioritize architectural integrity, algorithmic optimization, and clean, readable code over simply "getting it to work." 

Whether you are fixing a typo, optimizing a database query from $O(n^2)$ to $O(n)$, or abstracting a raw CSS element into a reusable React component, your contributions are welcome. Please read this guide to understand our workflow.

---

## 🧭 1. Where Can I Contribute?

* **Code Optimization:** Spot a brute-force algorithm? Refactor it using Hash Maps or Sets and open a PR.
* **Architecture:** Notice business logic bleeding into an Express controller? Extract it into a Service layer.
* **Documentation:** Add clearer explanations or missing edge cases to any `README.md`.
* **Bug Fixes:** Check the [Issues](https://github.com/sagorroy2003/engineering-mastery/issues) tab for anything labeled `good first issue` or `bug`.

---

## 🌿 2. Branching Strategy

We use a feature-branching workflow. **Never commit directly to the `main` branch.**



1. **Fork** the repository and clone it to your local machine.
2. **Sync** your local `main` branch with the upstream repository.
3. **Create a new branch** for your feature or fix. 
   * *Format:* `<type>/<short-description>`
   * *Examples:* `feat/add-docker-compose`, `fix/auth-middleware-crash`, `refactor/vendor-search-query`, `docs/update-readme`

```bash
git checkout -b feat/optimize-vendor-search
```

---

## 🏷️ 3. Commit Message Standards

We strictly follow the [Conventional Commits](https://www.conventionalcommits.org/) specification. This allows us to auto-generate changelogs and maintain a clean history.

**Format:** `<type>(<scope>): <subject>`

* ✨ `feat:` A new feature.
* 🐛 `fix:` A bug fix.
* ♻️ `refactor:` A code change that neither fixes a bug nor adds a feature (e.g., Clean Architecture separation).
* ⚡ `perf:` A code change that improves performance (e.g., adding a database index).
* 📝 `docs:` Documentation only changes.

**Example of a good commit:**
`perf(api): optimize product search query using B-Tree index`

---

## 🕵️‍♂️ 4. The Pull Request (PR) Standard

When you open a PR, you are submitting a document defending your engineering decisions. Your PR will be reviewed by maintainers with a focus on three pillars:

### I. Algorithmic Complexity
If you submit a PR that loops through arrays unnecessarily or causes an N+1 query problem in the database, it will be rejected. You will be challenged to find a more efficient solution.

### II. Defensive Error Handling
Are you wrapping async database calls in `try/catch` blocks? Are you using centralized error handling? Swallowed exceptions are not permitted.

### III. Clean Component Design (Frontend)
If you are contributing to a UI module, ensure you are not duplicating raw CSS. Build or reuse abstracted, presentational components (e.g., `<Button variant="primary" />`).

---

## 🛠️ 5. Submitting Your PR

1. Push your branch to your forked repository.
2. Open a Pull Request against our `main` branch.
3. Fill out the **Pull Request Template** automatically provided by GitHub.
4. Ensure your PR passes all automated CI/CD checks (Linting, Tests).
5. Wait for a code review. Be prepared to discuss trade-offs and make adjustments based on feedback.

---

## 💻 Local Development Setup

To run the examples or test your changes locally, ensure you have the following installed:
* [Node.js](https://nodejs.org/) (v18 or higher)
* [Docker Desktop](https://www.docker.com/products/docker-desktop/) (For spinning up local PostgreSQL/Redis instances)
* [Git](https://git-scm.com/)

```bash
# Clone your fork
git clone [https://github.com/](https://github.com/)<your-username>/engineering-mastery.git

# Navigate into the project
cd engineering-mastery

# Install dependencies (if applicable to the module you are editing)
npm ci
```

---
*By contributing to this repository, you agree that your contributions will be licensed under its MIT License.*
