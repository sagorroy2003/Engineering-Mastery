# ⚙️ 10. Continuous Integration & Continuous Deployment (CI/CD)

![Difficulty: Advanced](https://img.shields.io/badge/Difficulty-Advanced-orange?style=for-the-badge)
![Focus: Automation](https://img.shields.io/badge/Focus-Automation-blue?style=for-the-badge)
![Tool: GitHub Actions](https://img.shields.io/badge/Tool-GitHub_Actions-2088FF?style=for-the-badge)

In a professional engineering team, human error is the greatest risk to deployment. If a developer forgets to run the test suite locally before merging their code, the production server for UniBazer crashes. 

CI/CD is the practice of automating the integration of code changes and the deployment of those changes. It is the heartbeat of modern DevOps.

---

## 🔄 Continuous Integration (CI) vs. Continuous Deployment (CD)



### Continuous Integration (The Shield)
CI is your automated defense mechanism. Every time a developer opens a Pull Request or pushes code to GitHub, a server automatically spins up to:
1. **Lint the Code:** Ensure styling matches the team's rules (e.g., ESLint, Prettier).
2. **Run Tests:** Execute unit and integration tests (e.g., Jest, Mocha).
3. **Build the App:** Compile TypeScript to JavaScript or build the frontend React application to ensure there are no compilation errors.

If any of these steps fail, the pipeline fails, and GitHub blocks the Pull Request from being merged.

### Continuous Deployment (The Delivery)
Once the CI pipeline glows green and the code is merged to the `main` branch, the CD pipeline takes over. It automatically:
1. **Containerizes:** Builds the new Docker image based on the latest code.
2. **Publishes:** Pushes that image to a registry (like Docker Hub or GitHub Container Registry).
3. **Deploys:** SSHs into your production VPS and restarts the services with the new image, or triggers a webhook.

---

## 🧠 GitHub Actions: Architecture & Optimization

GitHub Actions is the industry standard for CI/CD built directly into your repository.



* **Workflows:** The automated process (defined in a `.yml` file in `.github/workflows/`).
* **Events:** What triggers the workflow (e.g., `push` to `main`, or a `pull_request`).
* **Runners:** The actual Virtual Machine (usually Ubuntu) that GitHub provides to run your commands.
* **Jobs:** A set of steps executed on the same runner.

### Optimizing the Pipeline: Dependency Caching
**The Junior Mistake:** Running `npm install` from scratch on every single commit. This can take 2-3 minutes per run. If your team pushes 50 commits a day, you are wasting hours of compute time.

**The Senior Solution:** Cache your dependencies. The pipeline should only download packages if the `package-lock.json` file has changed.

---

## 🛠️ Real-World Example: The CI Pipeline YAML

Here is a production-grade CI workflow for a Node.js backend. It enforces linting, testing, and uses caching for optimization.

```yaml
# .github/workflows/backend-ci.yml
name: Backend CI Pipeline

# Trigger only on pushes or PRs to the main branch
on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  test-and-build:
    # Run on GitHub's Ubuntu servers
    runs-on: ubuntu-latest

    steps:
      # 1. Pull the code into the runner
      - name: Checkout Repository
        uses: actions/checkout@v4

      # 2. Setup Node.js securely
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm' # 🔥 Optimization: Caches ~/.npm to speed up future builds

      # 3. Install dependencies cleanly (fails if lockfile is out of sync)
      - name: Install Dependencies
        run: npm ci

      # 4. Enforce Code Quality
      - name: Run Linter
        run: npm run lint

      # 5. Run the automated tests
      - name: Execute Test Suite
        run: npm run test
        env:
          # Securely inject test database URLs from GitHub Secrets
          DATABASE_URL: ${{ secrets.TEST_DB_URL }} 
```

---

## 🔐 Secrets Management in CI/CD

Never hardcode your production database passwords, API keys, or Docker Hub credentials in your YAML files or codebase. 

1. Go to your repository settings on GitHub.
2. Navigate to **Secrets and variables > Actions**.
3. Add your secret (e.g., `DOCKER_PASSWORD`).
4. Access it in your YAML file using the exact syntax: `${{ secrets.DOCKER_PASSWORD }}`. GitHub will automatically mask this in the logs so it is never exposed.

---

## 🎯 Practical Exercises
1. **Create your first Workflow:** In your repository, create a folder structure exactly like this: `.github/workflows/test.yml`. Write a workflow that simply runs `echo "Hello CI/CD"` on an `ubuntu-latest` runner every time you push code.
2. **Implement Linter Blocking:** Introduce a deliberate syntax error in your JavaScript codebase. Push it to a new branch and open a PR. Watch your GitHub Actions pipeline catch the error and mark the PR with a red "X".
3. **Automate Docker Builds:** Write a CD workflow that triggers *only* when you create a new GitHub Release. Make the workflow build your multi-stage Dockerfile and push it to Docker Hub.

## 🎤 Interview Questions
* *"Our CI pipeline currently takes 15 minutes to run, which is slowing down developer velocity. Walk me through exactly how you would profile and optimize it."*
* *"What is the difference between Continuous Delivery and Continuous Deployment?"*
* *"You accidentally pushed a `.env` file to the main branch, and the CD pipeline deployed it to production. Walk me through your incident response protocol."*
