# DevSecOps Commands & Pipeline Cheat Sheet ⚡

Categorized command sheet for scanning code, container images, secrets, and sample DevSecOps GitHub Actions workflow.

---

## 1. Trivy Scanning Commands

```bash
# Scan a local Docker Image for OS and package vulnerabilities
trivy image my-app:latest

# Fail build only if HIGH or CRITICAL vulnerabilities are found
trivy image --severity HIGH,CRITICAL --exit-code 1 my-app:latest

# Scan local repository filesystem for vulnerabilities and misconfigurations
trivy fs --security-checks config,vuln,secret .

# Generate an SBOM (Software Bill of Materials) in CycloneDX format
trivy image --format cyclonedx --output sbom.json my-app:latest
```

---

## 2. Gitleaks Secret Scanning Commands

```bash
# Scan local git repository for uncommitted secrets
gitleaks detect --source . -v

# Scan full commit history and export results to JSON
gitleaks detect --source . --report-path secrets-report.json

# Protect uncommitted code (protect mode)
gitleaks protect --verbose
```

---

## 3. Integrated DevSecOps GitHub Actions Pipeline

```yaml
name: DevSecOps Automated Pipeline

on:
  push:
    branches: [ "main" ]

jobs:
  security-scans:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Gate 1 - Secret Scanning
        uses: gitleaks/gitleaks-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: Gate 2 - SCA & Container Scan
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: 'python:3.9-slim'
          format: 'table'
          exit-code: '1'
          severity: 'CRITICAL,HIGH'
```