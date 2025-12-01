# Three Pipelines (Application Python/NodeJS/DotNet Image Build and Push to ACR Artifactory)
## The Redis and Postgress Container will be created direct from Docker Image

## 1. Result (NodeJS Application Image Build & Push  ) Pipeline
### Result app displays the count by fetching data from PostGress DB
```yaml
# Docker
# Build and push an image to Azure Container Registry
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker

trigger:
 paths:
  include:
    - result/*

resources:
- repo: self

variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: '9a09ee63-ac1c-4d59-93f6-d187a42bf201'
  imageRepository: 'resultapp'
  containerRegistry: 'conrg1.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/result/Dockerfile'
  tag: '$(Build.BuildId)'

  # Agent VM image name
pool:
 name: "Azure Pipelines"

stages:
- stage: Build
  displayName: Build stage
  jobs:
  - job: Build
    displayName: Build
    steps:
    - task: Docker@2
      displayName: Build the image
      inputs:
        containerRegistry: '$(dockerRegistryServiceConnection)'
        repository: '$(imageRepository)'
        command: 'build'
        Dockerfile: 'result/Dockerfile'
        tags: '$(tag)'
- stage: Push
  displayName: push stage
  jobs:
  - job: Push
    displayName: Push
    steps:
    - task: Docker@2
      displayName: push an image to container registry
      inputs:
        command: push
```
## 2. Voter  (Python Application Image Build & Push ) Pipeline
###  Vote app records the selection to In-Memory Redis DB
```yaml
# Docker
# Build and push an image to Azure Container Registry
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker

trigger:
 paths:
  include:
    - vote/*

resources:
- repo: self

variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: '9a09ee63-ac1c-4d59-93f6-d187a42bf201'
  imageRepository: 'voteapp'
  containerRegistry: 'conrg1.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/vote/Dockerfile'
  tag: '$(Build.BuildId)'

  # Agent VM image name
pool:
 name: "Azure Pipelines"

stages:
- stage: Build
  displayName: Build stage
  jobs:
  - job: Build
    displayName: Build
    steps:
    - task: Docker@2
      displayName: Build the image
      inputs:
        containerRegistry: '$(dockerRegistryServiceConnection)'
        repository: '$(imageRepository)'
        command: 'build'
        Dockerfile: 'vote/Dockerfile'
        tags: '$(tag)'
- stage: Push
  displayName: push stage
  jobs:
  - job: Push
    displayName: Push
    steps:
    - task: Docker@2
      displayName: push an image to container registry
      inputs:
        command: push
```
## 3. Worker ((DotNet Application Image Build & Push  )  Pipeline
### Worker app Fetchs  records from Redis and Populates the Postgress DB 
```yaml
# Docker
# Build and push an image to Azure Container Registry
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker

trigger:
 paths:
  include:
    - worker/*

resources:
- repo: self

variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: '9a09ee63-ac1c-4d59-93f6-d187a42bf201'
  imageRepository: 'workerapp'
  containerRegistry: 'conrg1.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/result/Dockerfile'
  tag: '$(Build.BuildId)'

  # Agent VM image name
pool:
 name: "Azure Pipelines"

stages:
- stage: Build
  displayName: Build stage
  jobs:
  - job: Build
    displayName: Build
    steps:
    - task: Docker@2
      displayName: Build the image
      inputs:
        containerRegistry: '$(dockerRegistryServiceConnection)'
        repository: '$(imageRepository)'
        command: 'build'
        Dockerfile: 'worker/Dockerfile'
        tags: '$(tag)'
- stage: Push
  displayName: push stage
  jobs:
  - job: Push
    displayName: Push
    steps:
    - task: Docker@2
      displayName: push an image to container registry
      inputs:
        command: push
        repository: $(imageRepository)
        dockerfile: $(dockerfilePath)
        containerRegistry: $(dockerRegistryServiceConnection)
        tags: |
          $(tag)

