# 🐙 02. GitHub Platform & Professional Collaboration

![Difficulty: Advanced](https://img.shields.io/badge/Difficulty-Advanced-orange?style=for-the-badge)
![Focus: Team Collaboration](https://img.shields.io/badge/Focus-Team_Collaboration-blue?style=for-the-badge)
![Code Quality: Strict](https://img.shields.io/badge/Code_Quality-Strict-red?style=for-the-badge)

Writing code is only 30% of software engineering. The other 70% is communicating your intent to other developers. This module bridges the gap between solo coding and working in a high-performance engineering team.

---

## 📝 Writing Professional Pull Requests (PRs)

A Pull Request is not just a request to merge code; it is a document defending your architectural decisions. 

### PR Best Practices
1. **Scope it Down:** If you are building `UniBazer`, do not put "Vendor Registration", "Product Upload", and "Stripe API" in one PR. One PR = One Feature.
2. **Context is King:** Explain *why* you chose a specific approach. 
3. **Include Visuals:** If it's a UI change, link the Figma design and include a "Before/After" screenshot of your React component.

### 💡 Example of a Great PR Description
```markdown
## What does this PR do?
Implements the `VendorDashboard` component and wires it to the `GET /api/vendors/stats` endpoint.

## Trade-offs & Decisions
* **Performance:** I noticed the initial data transformation for the sales chart was running in $O(n^2)$ time due to nested loops. I refactored this using a hash map to bring it down to $O(n)$ time.
* **Component Design:** Instead of hardcoding the stat cards, I abstracted a `<StatCard />` component into our UI library for reuse in the Admin panel later.

## Missing/Pending
* Needs robust error handling if the analytics API times out (currently just shows a generic "Error" text). Let's tackle this in the next PR.
