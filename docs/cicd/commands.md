# CI/CD Workflow Syntax & Commands ⚡

Examples of pipeline configuration files for **GitHub Actions**, **GitLab CI**, and **Jenkinsfile**.

---

## 1. GitHub Actions Workflow (`.github/workflows/ci.yml`)

```yaml
name: Production CI/CD Pipeline

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v4

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'

    - name: Install Dependencies
      run: npm ci

    - name: Run Tests
      run: npm test

    - name: Build Docker Image
      run: docker build -t myapp:${{ github.sha }} .
```

---

## 2. GitLab CI Configuration (`.gitlab-ci.yml`)

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building application..."
    - mkdir build && echo "compiled binary" > build/app.exe
  artifacts:
    paths:
      - build/

test_job:
  stage: test
  script:
    - echo "Running unit tests..."

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production server..."
  only:
    - main
```

---

## 3. Jenkins Declarative Pipeline (`Jenkinsfile`)

```groovy
pipeline {
    agent any

    environment {
        APP_NAME = 'my-web-app'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/user/repo.git'
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploying ${env.APP_NAME}..."
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
```