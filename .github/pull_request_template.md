## 📝 Description
Provide a concise summary of the changes introduced. Explain *why* this change is necessary and detail any architectural trade-offs you made (e.g., Development Speed vs. Long-term Scalability).

## 🔗 Related Issue
Fixes # (issue number)

## 🔄 Type of Change
Please check the applicable options:
- [ ] 🐛 **Bug fix** (non-breaking change which fixes an issue)
- [ ] ✨ **New feature** (non-breaking change which adds functionality)
- [ ] ♻️ **Refactoring** (Clean Architecture separation, no functional changes)
- [ ] ⚡ **Performance Optimization** (Algorithmic improvements, DB indexing)
- [ ] 📝 **Documentation** (README updates, comments)

## 🧠 Engineering Standards Justification
Defend your engineering decisions. Reviewers will check these closely.

* **Algorithmic Complexity:** Are there any brute-force $O(n^2)$ loops? Explain how you optimized your data transformations (e.g., using Hash Maps for $O(n)$ lookups) or database queries (e.g., B-Tree indexing).
* **Frontend / UI:** Did you abstract raw CSS into reusable presentational components based on the design system, or are you repeating classes?
* **Backend / Architecture:** Is the business logic strictly separated from the HTTP controllers into the Service layer?

## ✅ Pre-Merge Checklist
Before requesting a review, ensure you have completed the following:

- [ ] My commit messages follow the Conventional Commits format (e.g., `feat:`, `fix:`, `refactor:`).
- [ ] I have performed a self-review of my code, actively looking for optimization opportunities.
- [ ] Variable and function names are explicitly descriptive (no single-letter variables outside of standard loops).
- [ ] All async database or external API calls are wrapped in `try/catch` blocks using the centralized `AppError` class.
- [ ] I have not duplicated raw CSS; UI elements use our centralized component library.
- [ ] There are no `console.log()` statements left in production code.
- [ ] My code successfully passes all automated CI/CD checks (Linting, Tests).
