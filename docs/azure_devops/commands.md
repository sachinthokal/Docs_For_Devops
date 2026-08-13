# Azure DevOps YAML Pipeline Syntax & CLI Cheat Sheet ⚡

Complete YAML reference for `azure-pipelines.yml` and Azure CLI (`az devops`) management.

---

## 1. Complete Multi-Stage Pipeline Example

```yaml
trigger:
  branches:
    include:
      - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'

stages:
- stage: Build
  displayName: 'Build and Test Stage'
  jobs:
  - job: BuildJob
    steps:
    - task: UseDotNet@2
      inputs:
        packageType: 'sdk'
        version: '8.x'

    - script: dotnet build --configuration $(buildConfiguration)
      displayName: 'dotnet build'

    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Pipeline.Workspace)'
        artifact: 'drop'

- stage: Deploy
  displayName: 'Deploy to Production'
  dependsOn: Build
  condition: succeeded()
  jobs:
  - deployment: DeployWeb
    environment: 'production'
    strategy:
      runOnce:
        deploy:
          steps:
          - script: echo "Deploying to production server..."
```

---

## 2. Azure CLI (`az devops`) Commands

```bash
# Set default organization and project
az devops configure --defaults organization=https://dev.azure.com/YourOrg project=YourProject

# Pipeline Management
az pipelines list
az pipelines run --name "My-Pipeline"
az pipelines build show --id 101

# Repository & Pull Request Management
az repos list
az repos pr list
az repos pr create --title "Fix bug" --source-branch feature/fix --target-branch main
```