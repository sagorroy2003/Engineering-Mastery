# 🌳 01. Git Core Internals & Advanced Operations

![Difficulty: Intermediate](https://img.shields.io/badge/Difficulty-Intermediate-yellow?style=for-the-badge)
![Focus: Version Control](https://img.shields.io/badge/Focus-Version_Control-blue?style=for-the-badge)
![Status: Essential](https://img.shields.io/badge/Status-Essential-success?style=for-the-badge)

Most junior developers treat Git like a magic "save game" button. Senior engineers understand it as a **Directed Acyclic Graph (DAG)** of snapshots. Knowing how Git works under the hood is what saves a multi-vendor marketplace codebase from becoming an unrecoverable mess.

---

## 🧠 How Git *Actually* Works
Git does **not** store diffs; it stores snapshots. 

* 📂 **Working Tree:** The files you currently see on your disk.
* 📝 **Staging Area (Index):** A blueprint detailing exactly what will go into your next commit.
* 🎯 **HEAD:** A pointer to your current branch reference, which in turn points to the latest commit.

### The Git Object Model
When you commit, Git creates three types of objects:
1. **Blobs:** The actual file contents.
2. **Trees:** Directory listings (pointers to blobs and other trees).
3. **Commits:** Metadata (author, message) and a pointer to the top-level tree.

---

## ⚔️ Merge vs. Rebase: The Ultimate Trade-Off

When integrating a feature branch (e.g., `feat/vendor-auth`), you have two primary choices. 

| Feature | 🔀 `git merge` | 🛤️ `git rebase` |
| :--- | :--- | :--- |
| **History** | Preserves exact history, including a "merge commit". | Rewrites history to create a single, clean linear progression. |
| **Traceability** | **High.** You see exactly when a branch was integrated. | **Low.** It looks like the work was done sequentially. |
| **Conflicts** | Resolve all conflicts *once* at the merge commit. | Resolve conflicts *commit-by-commit*. |
| **Best For** | Merging a completed feature into `main`. | Updating your local feature branch with fresh changes from `main`. |

🚨 **The Golden Rule of Rebasing:** *Never rebase commits that have been pushed to a public repository.* It rewrites history and will completely break your teammates' local environments.

---

## ⏪ The Professional Workflow: Reset vs. Revert

Mistakes happen. How you fix them defines your seniority.

* 🧨 **`git reset --hard <commit>`**: Moves HEAD backwards and wipes out your working directory changes. 
  * *Use Case:* Local cleanup only. You experimented and want to trash the code.
* 🛡️ **`git revert <commit>`**: Creates a *new* commit that undoes the changes of a previous commit. 
  * *Use Case:* Production rollbacks. If a bug slips into the live university marketplace, you revert. It preserves the history of the mistake and the fix.

---

## 🎯 Practical Exercises
1. Initialize a repo, create a file, and use `git cat-file -p HEAD^{tree}` to explore the internal tree structure.
2. Create a merge conflict on purpose between two branches modifying the same line. Resolve it using a 3-way merge tool.
3. Use `git rebase -i HEAD~3` (Interactive Rebase) to squash 3 messy "WIP" commits into one clean commit.

## 🎤 Interview Questions
* *"What is the difference between a Git fetch and a Git pull?"*
* *"You accidentally committed a `.env` file containing database passwords and pushed it to GitHub. How do you resolve this securely?"* (Hint: Just deleting the file and committing again is the wrong answer).
