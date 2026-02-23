# 🚀 Engineering Mastery: Full-Stack & DevOps Fundamentals

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)
![Status: Active](https://img.shields.io/badge/Status-Active-success.svg)

Welcome to **Engineering Mastery**. This repository is not a collection of basic syntax tutorials. It is a comprehensive, production-quality learning path designed to bridge the gap between building localized side projects and architecting scalable, real-world software systems.

Whether you are preparing for technical interviews at top-tier software agencies or building complex platforms (like a multi-vendor marketplace), this repository serves as a blueprint for professional engineering.

---

## 🧠 Core Engineering Philosophies

This curriculum is built on four non-negotiable pillars of software engineering:

### 1. Architecture & Trade-offs Over Syntax
Knowing how to write a function is easy; knowing *where* that function belongs in a massive codebase is hard. We focus on Clean Architecture, separating controllers from business logic (services) and data layers (repositories). Every solution presented here comes with an analysis of its trade-offs (e.g., Development Speed vs. Long-term Scalability).

### 2. Algorithmic Optimization
We treat backend performance as strictly as a competitive programming environment. If a data transformation or database query runs in $O(n^2)$ time, we challenge it. You will find deep dives into indexing, query optimization, and refactoring brute-force logic into efficient $O(n \log n)$ or $O(n)$ solutions.

### 3. Bridging Design to Code (Figma to React)
Frontend engineering is not about writing raw, scattered CSS. It is about translating a Figma design system into a robust, reusable component library. We focus on abstracting UI elements (Buttons, Inputs, Modals) so that your React codebase remains DRY (Don't Repeat Yourself) and visually consistent.

### 4. The DevOps Mindset
"It works on my machine" is an unacceptable excuse in production. This guide integrates Docker, Linux fundamentals, and CI/CD pipelines as first-class citizens, ensuring you know how to package, deploy, and monitor your applications reliably.



---

## 🗺️ The Learning Roadmap

The repository is structured sequentially. It is highly recommended to study the modules in this exact order, as each builds upon the architectural concepts of the last.

### Phase 1: The Professional Baseline
* 📂 [**01-git-core:**](./01-git-core/) Internal Git object models, branching strategies, and resolving complex rebases.
* 📂 [**02-github-platform:**](./02-github-platform/) PR lifecycles, code review etiquette, and repository security.
* 📂 [**03-collaboration-workflows:**](./03-collaboration-workflows/) GitFlow, writing clean semantic commits, and team collaboration.

### Phase 2: System Architecture & Data
* 📂 [**04-backend-core:**](./04-backend-core/) REST principles, MVC vs. Clean Architecture, and centralized error handling.
* 📂 [**05-database-design:**](./05-database-design/) Relational modeling, normalization, B-Tree indexing, and query optimization.
* 📂 [**06-authentication-security:**](./06-authentication-security/) Deep dive into JWTs, session state, RBAC (Role-Based Access Control), and preventing injection attacks.

### Phase 3: Frontend Engineering
* 📂 [**07-frontend-architecture:**](./07-frontend-architecture/) State management, custom hooks, performance optimization (memoization), and building reusable UI component libraries.

### Phase 4: Infrastructure & Deployment
* 📂 [**08-linux-fundamentals:**](./08-linux-fundamentals/) Permissions, process management, and secure server configuration.
* 📂 [**09-docker-basics:**](./09-docker-basics/) Containerization, multi-stage builds for optimized image sizes, and Docker Compose.
* 📂 [**10-ci-cd:**](./10-ci-cd/) Automating testing, building, and deployment using GitHub Actions.
* 📂 [**11-deployment-guide:**](./11-deployment-guide/) VPS provisioning, Nginx reverse proxy setup, SSL termination, and production monitoring.

---

## 🏗️ The Capstone Architecture
Throughout these modules, the concepts are grounded in a unified, real-world scenario: **Building a Scalable Multi-Vendor Marketplace**. 

By following this repository, you will understand how to design the database schema for thousands of products, secure the routes for different user roles (Buyers vs. Vendors), build the reusable React dashboard, and deploy the entire microservice stack using Docker and Nginx.

---

## 🛠️ How to Use This Repository

1. **Read the Deep Dives:** Do not skim. The README in each folder contains architectural explanations, not just command lists.
2. **Review the Code Snippets:** Look for explicit examples of what *not* to do (anti-patterns) alongside the correct, optimized implementations. Pay attention to variable naming and error handling.
3. **Do the Exercises:** Each module ends with practical exercises and common technical interview questions.
4. **Challenge the Code:** If you see a solution that can be optimized further, or a missing edge case in the error handling, open a Pull Request.

## 🤝 Contributing
We treat this repository with the same rigor as a production enterprise application. 
* All code submissions must handle edge cases and utilize descriptive variable names.
* All PRs must explain the *why* behind a change.
* See `02-github-platform/CONTRIBUTING.md` for full guidelines.

---
*Maintained by the Open Source Engineering Community.*
