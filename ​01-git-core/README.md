# 🌳 01. Git Core Internals & Advanced Operations

![Difficulty: Intermediate](https://img.shields.io/badge/Difficulty-Intermediate-yellow?style=for-the-badge)
![Focus: Version Control](https://img.shields.io/badge/Focus-Version_Control-blue?style=for-the-badge)
![Status: Essential](https://img.shields.io/badge/Status-Essential-success?style=for-the-badge)

Most junior developers treat Git like a magic "save game" button. Senior engineers understand it as a **Directed Acyclic Graph (DAG)** of snapshots. Knowing how Git works under the hood is what saves a complex codebase from becoming an unrecoverable mess.

---

## 🧠 How Git *Actually* Works
Git does **not** store diffs (changes); it stores snapshots of your entire project at a given point in time.

* 📂 **Working Tree:** The files you currently see and edit on your disk.
* 📝 **Staging Area (Index):** A blueprint detailing exactly what will go into your next commit.
* 🎯 **HEAD:** A pointer to your current branch reference, which in turn points to the latest commit.

### The Git Object Model
When you commit, Git creates three types of objects under the `.git/objects` directory:
1. **Blobs:** The actual file contents.
2. **Trees:** Directory listings (pointers to blobs and other nested trees).
3. **Commits:** Metadata (author, message, timestamp) and a pointer to the top-level tree.



---

## 🏗️ Core Commands: The Lifecycle of a File

* `git add <file>`: Moves changes from the Working Tree to the Staging Area.
* `git commit -m "..."`: Packages the Staging Area into a permanent Snapshot (Commit object).
* `git fetch`: Downloads history from the remote repository to your local machine, **but does not integrate it** into your working files.
* `git pull`: A combination command. It runs `git fetch` followed immediately by `git merge`.

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

## 🧰 Advanced Professional Tools

### 1. The Stash (`git stash`)
You are halfway through building the vendor dashboard when a critical bug is reported in production. You aren't ready to commit your broken code.
* `git stash`: Saves your uncommitted changes on a temporary stack and gives you a clean working directory.
* `git stash pop`: Reapplies the saved changes so you can resume work.

### 2. Cherry-Picking (`git cherry-pick <commit-hash>`)
You fixed a bug on the `develop` branch, but you need that exact fix on `main` *right now* without merging the rest of the unstable development code. Cherry-picking allows you to grab a single, specific commit from anywhere in the graph and apply it to your current branch.

### 3. Git Hooks
Git can automatically fire off custom scripts when certain important actions occur.
* **Pre-commit Hook:** Used professionally (via tools like Husky) to automatically run linters or code formatters *before* Git allows the commit to be created. If the code is messy, the commit is blocked.

---

## ⏪ The Professional Workflow: Reset vs. Revert

Mistakes happen. How you fix them defines your seniority.

* 🧨 **`git reset --hard <commit>`**: Moves HEAD backwards and wipes out your working directory changes entirely. 
  * *Use Case:* Local cleanup only. You experimented, it failed, and you want to completely trash the code.
* 🛡️ **`git revert <commit>`**: Creates a *new* commit that is the exact inverse of a previous commit. 
  * *Use Case:* Production rollbacks. If a bug slips into the live marketplace, you revert. It safely preserves the history of the mistake and the fix without breaking the remote graph.

---

## 🚑 Troubleshooting: The "Detached HEAD" State
If you checkout a specific commit hash instead of a branch name (e.g., `git checkout a1b2c3d`), you enter a "Detached HEAD" state. 
* **What it means:** HEAD is pointing directly to a commit, not a branch.
* **The danger:** Any new commits you make here will be orphaned and garbage-collected when you switch branches. 
* **The fix:** Create a new branch immediately: `git checkout -b new-branch-name`.

---

## 🎯 Practical Exercises
1. Initialize a repo, create a file, and use `git cat-file -p HEAD^{tree}` to explore the internal tree structure.
2. Create a merge conflict on purpose between two branches modifying the same line. Resolve it using a 3-way merge tool.
3. Use `git rebase -i HEAD~3` (Interactive Rebase) to squash 3 messy "WIP" commits into one clean, professional commit.
4. Set up a simple `pre-commit` hook that blocks commits if the word "console.log" is found in the staged files.

## 🎤 Interview Questions
* *"What is the difference between a Git fetch and a Git pull?"*
* *"You accidentally committed a `.env` file containing database passwords and pushed it to GitHub. How do you resolve this securely?"* (Hint: Just deleting the file and committing again is the wrong answer—it's still in the history. You need `git filter-repo` or BFG Repo-Cleaner, and to rotate your keys immediately).
* *"Explain the difference between `git reset --soft` and `git reset --hard`."*
