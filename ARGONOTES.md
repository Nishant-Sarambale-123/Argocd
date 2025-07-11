Here’s a **complete and detailed guide on GitHub Actions**, covering all core concepts, components, use cases, and practical examples. These notes are perfect for interview prep, hands-on usage, or DevOps learning.

---

# 🔧 GitHub Actions – Complete Notes

---

## ✅ What is GitHub Actions?

**GitHub Actions** is a CI/CD (Continuous Integration and Continuous Delivery) platform integrated into GitHub. It allows you to automate tasks like:

* Building, testing, and deploying code
* Automating workflows like code linting, pull request checks, deployments
* Triggering actions on GitHub events (push, pull request, etc.)

---

## 📦 Core Components

| Component    | Description                                                        |
| ------------ | ------------------------------------------------------------------ |
| **Workflow** | Automated procedure defined in `.yml` files                        |
| **Event**    | Triggers a workflow (e.g., push, PR, issue)                        |
| **Job**      | A set of steps running on the same runner                          |
| **Step**     | Individual task within a job                                       |
| **Action**   | Reusable commands/code blocks (like plugins)                       |
| **Runner**   | Machine that executes your workflow (GitHub-hosted or self-hosted) |

---

## 🗂️ Directory Structure

GitHub Actions workflows are stored in:

```
.github/workflows/
```

Example:

```
.github/
 └── workflows/
      └── ci.yml
```

---

## 🧾 Workflow Syntax (YAML File)

```yaml
name: CI Workflow

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install Dependencies
        run: npm install

      - name: Run Tests
        run: npm test
```

---

## 🚦 Triggering Events (`on:`)

You can trigger workflows based on GitHub events:

### Common Triggers

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
```

### Scheduled

```yaml
on:
  schedule:
    - cron: '0 0 * * *'  # every day at midnight UTC
```

### Manual trigger (workflow\_dispatch)

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment'
        required: true
        default: 'production'
```

---

## 🔀 Jobs

Each `job` runs in a separate VM unless using `needs` to define dependencies.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

  test:
    runs-on: ubuntu-latest
    needs: build
```

---

## 🧱 Steps

Steps can use either `uses:` (external actions) or `run:` (shell commands).

```yaml
steps:
  - name: Checkout repo
    uses: actions/checkout@v3

  - name: Print working directory
    run: pwd
```

---

## 🔁 Actions

* Actions are reusable pieces of code.
* Can be created using JavaScript or Docker.

### Using Existing Action

```yaml
uses: actions/setup-node@v3
with:
  node-version: '18'
```

### Create Your Own Action

```yaml
action.yml
```

```yaml
name: 'My Custom Action'
description: 'Prints Hello'
runs:
  using: 'node12'
  main: 'index.js'
```

---

## 💻 Runners

| Type              | Description                                             |
| ----------------- | ------------------------------------------------------- |
| **GitHub-hosted** | Comes with pre-installed tools (Ubuntu, Windows, macOS) |
| **Self-hosted**   | Your own machine, more control                          |

```yaml
runs-on: ubuntu-latest
# OR
runs-on: self-hosted
```

---

## 📥 Secrets and Environment Variables

### Define Secrets

Stored in GitHub repo → Settings → Secrets

```yaml
env:
  DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

### Inline with step:

```yaml
- run: echo "My secret is $DB_PASSWORD"
  env:
    DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

---

## 📦 Artifacts

Used to upload and download files (e.g., logs, build outputs).

### Upload:

```yaml
- uses: actions/upload-artifact@v4
  with:
    name: build-artifact
    path: ./dist
```

### Download:

```yaml
- uses: actions/download-artifact@v4
  with:
    name: build-artifact
```

---

## 🛑 Caching

Improves performance by reusing dependencies.

```yaml
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

---

## 🔄 Matrix Builds

Run the same job on multiple OS, language versions, etc.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node: [14, 16, 18]
    steps:
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}
```

---

## 📄 Reusable Workflows

You can call workflows from other workflows.

```yaml
jobs:
  call-workflow:
    uses: org/repo/.github/workflows/deploy.yml@main
    with:
      environment: 'prod'
```

---

## 🔍 Debugging

Enable debug logging:

1. Go to repo → Settings → Secrets
2. Add secret: `ACTIONS_STEP_DEBUG = true`

Also use `echo` or `set -x` in `run:` steps.

---

## 🧪 Common Use Cases

| Use Case        | Description                          |
| --------------- | ------------------------------------ |
| CI/CD Pipeline  | Build → Test → Deploy                |
| Linting         | Run linters like ESLint on PRs       |
| Testing         | Run unit/integration tests           |
| Container Build | Build and push Docker images         |
| Deployment      | Deploy to AWS, Azure, GCP, etc.      |
| Notifications   | Send messages to Slack, Teams, Email |

---

## 🗂️ Best Practices

* Use environment-specific secrets
* Minimize workflow duplication with reusable workflows
* Use matrix builds for better coverage
* Leverage caching for faster pipelines
* Avoid secrets in logs
* Use separate workflows for CI and CD

---

## 🧪 GitHub Actions vs Other CI/CD Tools

| Feature                   | GitHub Actions | Jenkins      | GitLab CI |
| ------------------------- | -------------- | ------------ | --------- |
| Native GitHub Integration | ✅              | ❌            | ✅         |
| UI Simplicity             | ✅              | ❌            | ✅         |
| Community Actions         | ✅              | ❌            | ✅         |
| Hosted Runners            | ✅              | ❌            | ✅         |
| Self-hosted Support       | ✅              | ✅            | ✅         |
| Cost (for private repos)  | Limited Free   | Self-managed | Free Tier |

---

## 📘 Example: Docker Build & Push

```yaml
name: Docker CI

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: username/app:latest
```

---

## 📚 Resources

* [Official Docs](https://docs.github.com/en/actions)
* [Actions Marketplace](https://github.com/marketplace?type=actions)
* [GitHub Actions Examples](https://github.com/actions)

---

Let me know if you'd like examples for AWS/GCP/Azure deployment using GitHub Actions or if you want interview questions based on GitHub Actions.
