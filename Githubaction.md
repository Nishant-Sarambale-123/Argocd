Here’s a **comprehensive, deep-dive guide on GitHub Actions**—a powerful CI/CD platform built into GitHub. This guide includes **detailed explanations**, **examples**, and **official GitHub documentation links**.

---

# 📘 GitHub Actions: Complete Detailed Notes

---

## 🌟 What is GitHub Actions?

**GitHub Actions** is a feature that allows you to **automate, customize, and execute your software development workflows** right in your GitHub repository.

You can write individual tasks, called **actions**, and combine them to create a **workflow**. Workflows can be triggered by events like **push**, **pull requests**, **issue comments**, or **cron jobs**.

🔗 [Official Docs: Introduction to GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions)

---

## 📂 Directory Structure

GitHub Actions live inside your repository in this location:

```
.github/workflows/
```

All your workflow `.yml` or `.yaml` files go here.

---

## 🛠 Workflow Syntax

GitHub Actions uses **YAML** syntax to define workflows.

```yaml
name: My First Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
```

🔗 [Workflow Syntax Docs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)

---

## 📅 `on`: Triggering Events

The `on` field defines what **event** triggers your workflow.

### ✅ Examples

```yaml
on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - dev
  schedule:
    - cron: '0 0 * * *'   # every day at midnight
  workflow_dispatch:      # manual trigger
```

🔗 [Events that trigger workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)

---

## 🧱 `jobs`: Defining Jobs

A **job** is a set of steps that execute on the same runner.

### ✅ Syntax

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Step name
        run: echo "Hello World"
```

🔗 [Jobs documentation](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow)

---

## 🧑‍🔧 `runs-on`: Specify Runner

You define what **virtual machine** (runner) your job runs on:

* `ubuntu-latest`
* `windows-latest`
* `macos-latest`
* Self-hosted runners

🔗 [About GitHub-hosted runners](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners)

---

## 🪜 `steps`: What a Job Does

Steps run commands or use actions.

### ✅ Example

```yaml
steps:
  - name: Checkout code
    uses: actions/checkout@v3

  - name: Run tests
    run: npm test
```

🔗 [Understanding the workflow file](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#jobsjob_idsteps)

---

## 🔁 Reusable Actions

There are two ways to reuse functionality:

### 1. **Using Marketplace Actions**

```yaml
uses: actions/checkout@v3
```

🔗 [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)

### 2. **Creating Your Own Actions**

You can create:

* **JavaScript Actions**: Runs in Node.js
* **Docker Actions**: Runs in containers
* **Composite Actions**: Group multiple steps

🔗 [Creating Actions](https://docs.github.com/en/actions/creating-actions)

---

## 📥 `uses` vs `run`

* `uses` is for using an action or a Docker/JS package.
* `run` is for shell script commands.

### ✅ Example

```yaml
- name: Use an action
  uses: actions/setup-node@v3

- name: Run a command
  run: node app.js
```

---

## 🧪 Environments and Secrets

Use **Secrets** to manage sensitive info like tokens, passwords.

### ✅ Add secret from UI:

GitHub repo → Settings → Secrets → New repository secret

### ✅ Use in workflow

```yaml
env:
  API_KEY: ${{ secrets.API_KEY }}
```

🔗 [Encrypted Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)

---

## 📁 Artifacts

Used to **store and share files** between jobs or after workflow execution.

### ✅ Example

```yaml
- name: Upload artifact
  uses: actions/upload-artifact@v4
  with:
    name: logs
    path: ./logs
```

🔗 [Storing workflow data as artifacts](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts)

---

## 🔄 Matrix Strategy (Run jobs in parallel)

Used to run a job with multiple versions or environments.

### ✅ Example

```yaml
strategy:
  matrix:
    node: [14, 16, 18]

steps:
  - name: Setup Node.js
    uses: actions/setup-node@v3
    with:
      node-version: ${{ matrix.node }}
```

🔗 [Using matrix strategies](https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs)

---

## 🔐 Permissions

You can set fine-grained permissions using the `permissions` keyword.

```yaml
permissions:
  contents: read
  issues: write
```

🔗 [Workflow permissions](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#permissions)

---

## 🧠 Contexts and Expressions

**Contexts** are variables you can use in expressions:

* `${{ github }}` — info about the event/repo
* `${{ env }}` — environment variables
* `${{ secrets }}` — secrets

### ✅ Example

```yaml
run: echo "Triggered by ${{ github.event_name }}"
```

🔗 [Contexts and expressions](https://docs.github.com/en/actions/learn-github-actions/contexts)

---

## ⏱ Timeout and Retry

You can control job execution with:

```yaml
timeout-minutes: 10
continue-on-error: true
```

---

## 🧾 Caching Dependencies

Speed up workflows using caching:

### ✅ Example for Node.js

```yaml
- name: Cache node modules
  uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

🔗 [Caching dependencies](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows)

---

## 📌 Job/Step Conditions

Use `if` to control execution.

```yaml
if: github.ref == 'refs/heads/main'
```

🔗 [Conditional execution](https://docs.github.com/en/actions/learn-github-actions/expressions#about-expressions)

---

## 🧩 Composite Actions

Group multiple shell steps into one reusable action.

### ✅ Example `action.yml`

```yaml
name: My Composite Action
runs:
  using: "composite"
  steps:
    - run: echo "Step 1"
    - run: echo "Step 2"
```

🔗 [About composite actions](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action)

---

## 🏁 Status Check and Badges

Add a badge to your repo’s README to show workflow status:

```markdown
![CI](https://github.com/<user>/<repo>/actions/workflows/<file>.yml/badge.svg)
```

🔗 [Workflow status badges](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge)

---

## 📚 Advanced Topics

### ➤ **Workflow Call** (Reusing entire workflows)

```yaml
on:
  workflow_call:
    inputs:
      name:
        required: true
        type: string
```

🔗 [Reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

---

### ➤ **Manual Triggers with `workflow_dispatch`**

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deployment environment'
        required: true
        default: 'staging'
```

🔗 [Manual triggers](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch)

---

### ➤ **Concurrency**

Prevent race conditions:

```yaml
concurrency:
  group: deploy-production
  cancel-in-progress: true
```

🔗 [Concurrency](https://docs.github.com/en/actions/using-jobs/using-concurrency)

---

### ➤ **Environment Protection Rules**

Enforce rules like approvals for deployments.

🔗 [Environments](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment)

---

## 🚀 Example Full Workflow

```yaml
name: CI Pipeline

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

      - name: Upload logs
        uses: actions/upload-artifact@v4
        with:
          name: test-logs
          path: ./logs
```

---

## 🧪 Testing Workflows Locally

Use [**act**](https://github.com/nektos/act) to run workflows locally.

```bash
brew install act
act
```

---

## 🧠 GitHub Actions Interview Questions (Bonus)

1. What is the difference between `run` and `uses`?
2. How do you share data between jobs?
3. How do you cache dependencies?
4. What are matrix strategies?
5. Explain how secrets work in GitHub Actions.
6. How do you trigger workflows manually?
7. How do you handle failure in a step?
8. Can workflows call each other?
9. What is a composite action vs a reusable workflow?
10. How to debug a failed GitHub Action?

---

If you'd like, I can also create **practice YAMLs**, real-world **CI/CD pipelines**, or **Docker + GitHub Action projects** for hands-on learning.

Would you like a **PDF version** or **project-based example** next?
