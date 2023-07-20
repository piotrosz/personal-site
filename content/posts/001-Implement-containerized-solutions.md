---
title: "Implement containerized solutions"
date: 2023-07-19T22:59:43+02:00
draft: false
---
# Implement containerized solutions

## Create and manage container images for solutions

ACR = Azure Container Registry

- private Docker registry service
- based on Docker Registry
- to store Docker images

Azure Container Registry tasks - to build containers in Az
    - build on demand
    - triggers (e.g. code commits, base image updates)

### Use cases

- Orchestrators: e.g. Kubernetes, Docker swarm
- Az services: e.g. AKS, App Service, Batch, Service Fabric, etc.
- CI/CD pipelines e.g. Azure pipelines, Jenkins

### ACR service tiers

- Basic - for learning.
- Standard - more storage, image throughput, for most prod scenarios.
- Premium - geo-replication, private link - restricted access, for high volume scenarios.

### Storage capabilities

- Encryption-at-rest - decrypted on the fly when pulled.
- Regional storage - stored in one region by default.
- Zone redundancy - only in Premium - uses availability zones to copy registry to min. 3 zones in each enabled region.
- Scalable storage - different limits in each tier.

### Build & manage containers with tasks

ACR task scenarios:
- Quick task: build & push container image to registry on demand. Like `docker build`, `docker run` in the cloud.

    - `docker build` -- `az acr build`

- Auto triggered tasks

    - source code update,
    - base image update,
    - on schedule

    `az acr task create` - specify Git repo, branch & Dockerfile

- Multi-step task - build-and-push for multi containers.
    - defined in YAML

task has a context - files or git repo

### Dockerfile

- instructions to build Docker image
    - base image
    - update base OS & install software
    - build artifacts
    - services to expose
    - run commands

```
az acr create --resource-group az204-acr-rg \
    --name <myContainerRegistry> --sku Basic
```

```
az acr repository list --name <myContainerRegistry> --output table
```

```
az acr run --registry <myContainerRegistry> \
    --cmd '$Registry/sample/hello-world:v1' /dev/null
```

## Publish an image to Azure Container Registry



## Run containers by using Azure Container Instance



## Create solutions by using Azure Container Apps