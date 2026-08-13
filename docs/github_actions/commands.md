# GitHub Actions YAML Syntax & CLI Cheat Sheet ⚡

A comprehensive syntax guide for writing `.github/workflows/*.yml` files and managing actions via GitHub CLI (`gh`).

---

## 1. Essential Workflow Triggers (`on:`)

```yaml
# Multiple Events
on:
  push:
    branches: [ "main", "feature/*" ]
    paths-ignore:
      - '**.md'
  pull_request:
    types: [opened, synchronize, reopened]
  schedule:
    - cron: '0 0 * * *'  # Midnight daily
  workflow_dispatch:      # Manual trigger with inputs
    inputs:
      environment:
        description: 'Deploy target'
        required: true
        default: 'staging'
```

---

## 2. Comprehensive Workflow Template

```yaml
name: Full Production Pipeline

on:
  push:
    branches: [ "main" ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18.x, 20.x]
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install Dependencies
        run: npm ci

      - name: Run Unit Tests
        run: npm test

  deploy:
    needs: test  # Run only after 'test' job succeeds
    runs-on: ubuntu-latest
    steps:
      - name: Deploy Application
        env:
          API_KEY: ${{ secrets.PROD_API_KEY }}
        run: |
          echo "Deploying to production..."
          echo "Using secret token"
```

---

## 3. GitHub CLI Commands (`gh`) for Workflows

```bash
# List all workflow runs in current repo
gh run list

# View live logs for a specific run ID
gh run view <RUN_ID> --log

# Trigger a workflow dispatch manually
gh workflow run main.yml -f environment=production

# View workflow file status
gh workflow list
```